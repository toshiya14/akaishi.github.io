---
title: "第三人称视角角色控制器"
meta_title: ""
description: "在 Unity 中使用第三人称视角角色控制器"
categories: ["unity"]
author: "Akaishi Toshiya"
date: 2021-09-16T11:44:29+08:00
tags: ["unity", "游戏开发相关"]
draft: false
---

## 添加 Character Controller

为 `Third Person Player` 添加 `Character Controller` 部件。

设置 `Redius` 和 `Height` 以匹配碰撞胶囊体与角色模型。

## 新建脚本

为 `Third Person Player` 添加 `Third Person Movement` 脚本。

### 暴露属性

```C#
public CharacterController controller;
public float speed = 6f;
```

### 更新方法

```C#
void Update()
{
    float horizontal = Input.GetAxisRaw("Horizontal");
    float vertical = Input.GetAxisRaw("Vertical");
    Vector3 direction = new Vector3(horizontal, 0f, vertical).normalized;

    if (direction.magnitude >= 0.1f)
    {
        float targetAngle = Mathf.Atan2(direction.x, direction.z) * Mathf.Rad2Deg;
        transform.rotation = Quaternion.Euler(0f, targetAngle, 0f);

        controller.Move(direction * speed * Time.deltaTime);
    }
}
```

## 平滑移动

### 暴露属性

```C#
// 平滑转向
public float turnSmoothTime = 0.1f;
float turnSmoothVelocity;
```

### 更新方法

```C#
void Update()
{
    // ...
    if (direction.magnitude >= 0.1f)
    {
        float targetAngle = Mathf.Atan2(direction.x, direction.z) * Mathf.Rad2Deg;
        float angle = Mathf.SmoothDampAngle(transform.eulerAngles.y, targetAngle, ref turnSmoothVelocity, turnSmoothTime);
        transform.rotation = Quaternion.Euler(0f, angle, 0f);

    // ...
```

## 让角色朝摄像机方向移动

### 暴露属性

```C#
public Transform cam;
```

### 更新方法

```C#
void Update()
{
    // ...
    if (direction.magnitude >= 0.1f)
    {
        float targetAngle = Mathf.Atan2(direction.x, direction.z) * Mathf.Rad2Deg + cam.eulerAngles.y;
        float angle = Mathf.SmoothDampAngle(transform.eulerAngles.y, targetAngle, ref turnSmoothVelocity, turnSmoothTime);
        transform.rotation = Quaternion.Euler(0f, angle, 0f);

        Vector3 moveDirection = Quaternion.Euler(0f, targetAngle, 0f) * Vector3.forward;
        controller.Move(moveDirection.normalized * speed * Time.deltaTime);
    // ...
```

### 赋值属性

回到 Unity 编辑器，将`Third Person Player` 拖拽到 `Third Person Controller`中的 `cam` 属性上。

## 防止障碍物遮挡

1. `Third Person Camera`组件最下方`Extensions`部分，点选`CinemachineCollider`。

2. 在新出现的`Cinemachine Collider`中：
   
   1. `Collide Against`：去掉`Default`，添加`Ground`。（这里主要看你的环境使用的哪个层级）
   2. `Ignore Tag`：添加`Player`。（同时也要为`Third Person Character`设定`Tag`为`Player`）
   3. `Strategy`：`Pull Camera Forward`。
