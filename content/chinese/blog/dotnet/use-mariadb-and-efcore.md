---
title: "在 .Net 中使用 EF Core 连接 MariaDB"
meta_title: ""
description: "本文讲述了，如何使用 EF Core 连接 MariaDB，操作数据库的内容。"
categories: ["dotnet"]
author: "Akaishi Toshiya"
date: 2023-12-14T07:03:52+08:00
tags: [".NET", "EntityFramework", "数据库", "MariaDB", "MySQL"]
draft: false
---

## 前言

EF Core 支持两种模式，一种是以代码为基准的 Code-first 模式，还有一种可以让你在现有的数据库为基准，建立模型的 Database-first、Model-first 模式。
这里我们只讨论第一种，也就是 **Code-first** 模式。

### 什么是 EF Core

全称为 Entity Framework Core，为微软自家的数据库 ORM 框架，配合不同的数据库连接库可以支持各种不同的数据库。

### 什么是 MariaDB

可能说到 MySQL，你不会陌生，其实 MariaDB 跟 MySQL 原本是一家，不过 MariaDB 是有社区开发和维护的一个 Fork。

## 准备工作

### 安装如下 NuGet 包

- `Pomelo.EntityFrameworkCore.MySql` （MySQL 官方也有一个包，但是貌似不支持 MariaDB）
- `Microsoft.EntityFrameworkCore.Design` （不确定是否为必须的）

### 创建 Model

在项目中，创建 `Models` 文件夹，并开始编写我们在数据库中所需要的模型。

#### 常用的 Attribute

```C#
public record Entity
{
  [DatabaseGenerated(DatabaseGeneratedOption.Identity)] // 标识为自增 ID，自增必须为整数类型。
  [Key] // 标识为主键
  public int Id { get; set; }
  
  [Required] // 标识为不可为空
  public required string Name { get; set; }

  public ICollection<Info> Infos { get; set; }

  public DateTimeOffset Created { get; set; }
}

public record Info
{
  [Key]
  public string Id { get; set; }

  public int OriginId { get; set; }
  public Entity Origin { get; set; }

  [NotMapped] // 不会映射到数据库中
  public string MetaId { get; set; }

  [Column(TypeName = "varchar(20)")] // 指定类型和约束
  public string Name { get; set; }

  [Column("AnotherName")] // 映射到其他字段名
  public string AltName { get; set; }

  [MaxLength(50)] // 设定最大长度约束
  public string Desc { get; set; }

  [Precision(14, 2)] // 设定浮点数的精度和小数位数
  public decimal Length { get; set; }

  [Comment("Some Comment")] // 对字段设定注释
  public string XXX { get; set; }
}

public record OrderedModel 
{
  [Key]
  [Column(Order = 0)]
  public int Id { get; set; }

  [Column(Order = 1)]
  public string Name { get; set; }
}
```

上面我们用到了这些属性：
* `[Key]` 指定主键
* `[Required]` 标识为必需的字段
* `[MaxLength(n)]` 指定最大长度
* `[Precision(n)]` 指定精度和小数位数
* `[NotMapped]` 不映射到数据库中
* `[DatabaseGenerated(DatabaseGeneratedOption.Identity)]` 标记自增
* `[Comment]` 为字段添加注释
* `[Column]` 属性
  * `[Column("name")]` 使用 `name` 作为字段名，映射到数据库中
  * `[Column(Order = n)]` 对字段进行排序
  * `[Column(TypeName = "")]` 设定字段的数据类型

更多其他用法，可以参考 [EF 官方文档](https://learn.microsoft.com/zh-cn/ef/core/modeling/entity-properties?tabs=data-annotations%2Cwithout-nrt)

### 创建 Context

Context 相当于一个对所有实体进行管理的对象，同时，我们也需要在这个类中进行关系的设定和键的设定。

```C#
using Microsoft.EntityFrameworkCore;

namespace Data;

public class DataContext : DbContext
{
    // 下面是所有实体的集合对象，需要为 DbSet 类型。
    public DbSet<Entity> Entities { get; set; }
    public DbSet<Info> Infos { get; set; }
    public DbSet<OrderedModel> OrderedModels { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseMySql(
            // 连接字符串
            $"server={Env.DatabaseHost};port={Env.DatabasePort};database={Env.DatabaseName};user={Env.DatabaseUser};password={Env.DatabasePassword};charset=utf8",
            // 设定 MariaDB 的版本，如果是使用 MySQL，则需要 new MySqlServerVersion。
            new MariaDbServerVersion(new Version(10, 9, 8))
        );
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        // 为每个实体进行设定
        modelBuilder.Entity<Entity>(e => {
          // 唯一键设定
          e.HasIndex(x=>x.Name).IsUnique();

          // 索引
          e.HasIndex(x=>x.Created);

          // 多对一外键
          e.HasMany(x=>x.Infos).WithOne(x=>x.Origin).HasForeignKey(x=>x.OriginId).IsRequired(false);
        });
        modelBuilder.Entity<Info>();
        modelBuilder.Entity<OrderedModel>();
    }
}
```

关于键的设定，可以参考[官方文档](https://learn.microsoft.com/zh-cn/ef/core/modeling/indexes?tabs=data-annotations)。

### 创建 migration 并更新数据库

此时运行代码的话，EF 不会创建任何数据表。
我们还需要一些额外的操作来创建这些数据表（每次修改过 Model 之后也需要执行这一步）

打开命令提示符，并且导航到这个项目目录下。

```bash
dotnet tool install --global dotnet-ef
# 上面这一步安装只有在没有安装过 dotnet-ef 的时候才需要执行

dotnet ef migrations add <name>
dotnet ef database update
```

上文中的 `name` 需要修改以下，每次修改过 Model 之后，你就应该生成一个 migration （类似于版本），而这个 `name` 是这个版本的标识。

### 插入样例

```C#
using var ctx = new DataContext();
var entity = new Entity { Name = "some", Created = DateTimeOffset.Now };
ctx.Entities.Add(entity);
await ctx.SaveChangesAsync();
return entity; // 包含自动生成的 id
```

**⚠️注意：** 同一个 Context 对象并不能用于多个线程，所以需要为每个线程新建单独的一个实例。

## 与 IoC 联动

如果你使用 ASP.NET，可以使用下面的代码将 DataContext 加入到服务中。

```C#
builder.Services.AddDbContext<AuthContext>();
```

