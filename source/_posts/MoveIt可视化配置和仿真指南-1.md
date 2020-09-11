---
title: MoveIt可视化配置和仿真指南（1）
date: 2020-08-18 19:14:44
tags: [古月居,MoveIt,ROS]
catagories: ROS机器人
---
# 1.MoveIt简介
PR2机器人动态检测到障碍物
MoveIt Setup Assistant配置助手 
Rviz中使用MoveIt插件，对机器人进行plan、stop等控制
MoveIt也可以控制夹爪
<!--more-->
一个易于使用的**集成化开发平台/软件框架**
由一系列**移动操作的功能包**组成
>- 运动规划
>- 操作控制
>- 3D感知
>- 运动学
>- 控制与导航算法
>- ......

提供友好的GUI
可应用于工业、商业、研发等领域
ROS社区使用度排名前三的功能包

# 2.MoveIt Setup Assistant可视化配置
启动MoveIt Setup Assistant
```bash
$roslaunch moveit_setup_assistant setup_assistant.launch
```
配置步骤1-9：
![](http://qey02i1ot.hb-bkt.clouddn.com/assistant.jpg)
![](http://qey02i1ot.hb-bkt.clouddn.com/assistant2.jpg)
> 启动Setup Assistant之前要：
> 1). 安装MoveIt功能包
>```bash
> =  upadate your source  =
> $sudo apt-get update
> = install moveit =
> $sudo apt-get install ros-melodic-moveit
> ```
> 2).配置环境变量
> ```bash
> $source ~/ros_ws/devel/setup.bash
> ```
> 不配置环境变量，第一步（1.选择模型）会报错：
> “Running xacro failed.Please check console for errors.”

启动demo
```bash
$roslaunch c800_moveit_config demo.launch
```
![](http://qey02i1ot.hb-bkt.clouddn.com/interact.jpg)
