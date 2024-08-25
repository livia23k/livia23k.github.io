---
title: Unity Mini Game - 鬼屋之旅
categories: [Computer Graphics & Game, Game]
tags: [tech, game, unity, game demo, project]
img_path: /assets/img/post/2024-08-16-hautedjaunt/
---

## 功能演示

### 客户端

#### 个人成果DEMO

<div style="text-align: center;">
    <video style="display: block; margin: 0 auto;" src="../../../assets/img/post/2024-08-16-hautedjaunt/demo-solo.mp4" width="600" controls></video>
    <p style="color: #666; margin-top: 10px; font-size: 14px;"></p>
</div>

#### 组队成果DEMO

<div style="text-align: center;">
    <video style="display: block; margin: 0 auto;" src="../../../assets/img/post/2024-08-16-hautedjaunt/demo-team.mp4" width="600" controls></video>
    <p style="color: #666; margin-top: 10px; font-size: 14px;"></p>
</div>

### 渲染

#### 法线外扩描边

![](outlinershader.png){: w="600px"}
_视图坐标系下的法线外扩描边_

#### 死亡溶解

<div style="text-align: center;">
    <video style="display: block; margin: 0 auto;" src="../../../assets/img/post/2024-08-16-hautedjaunt/dissolveshader.mp4" width="600" controls></video>
    <p style="color: #666; margin-top: 10px; font-size: 14px;">溶解特效, 支持自定义消融边缘变色范围和颜色</p>
</div>

#### 扫描线

原理: 利用相机深度纹理图，在compute shader中恢复场景在世界坐标系下的三维位置信息，计算其与目标角色的距离，距离相同的线在同一时刻绘制出来。

参考：[从深度纹理重建像素的世界空间位置](https://docs.unity3d.com/cn/Packages/com.unity.render-pipelines.universal@12.1/manual/writing-shaders-urp-reconstruct-world-position.html)

<div style="text-align: center;">
    <video style="display: block; margin: 0 auto;" src="../../../assets/img/post/2024-08-16-hautedjaunt/scanner1.mp4" width="600" controls></video>
    <p style="color: #666; margin-top: 10px; font-size: 14px;">扫描线特效, 支持自定义扫描线粗细, 传播速度和传播范围</p>
</div>

<div style="text-align: center;">
    <video style="display: block; margin: 0 auto;" src="../../../assets/img/post/2024-08-16-hautedjaunt/scanner2.mp4" width="600" controls></video>
    <p style="color: #666; margin-top: 10px; font-size: 14px;">扫描线仿多道水波纹理</p>
</div>


## 最终架构设计

### 客户端

Unity 客户端

![](clientstructure.png){: w="1000px"}
_Client Structure_

### 服务端

基于 libuv, Google Protobuf 和 TCP 协议的 c++ 游戏服务器

![](serverstructure1.png){: w="1000px"}
_Server Structure (1)_

![](serverstructure2.png){: w="500px"}
_Server Structure (2)_

### C/S 交互流

![](csinteraction00.png){: w="600px"}
_总交互流程_

![](csinteraction01.png){: w="400px"}
_通用交互流程_

![](csinteraction10.png){: w="600px"}
_玩家登陆/注册交互流程_

![](csinteraction11.png){: w="600px"}
_玩家关卡选择交互流程_

![](csinteraction12.png){: w="600px"}
_对局相关交互流程_

![](csinteraction13.png){: w="600px"}
_对局中断线重连交互流程_

![](csinteraction14.png){: w="600px"}
_商城背包交互流程_


## 声明

开发资源致谢 [John Lemon's Haunted Jaunt: 3D Beginner](https://learn.unity.com/project/john-lemon-s-haunted-jaunt-3d-beginner?uv=2020.3) @Unity 