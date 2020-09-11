---
layout: "\x96"
title: MoveIt可视化配置和仿真指南（2）
date: 2020-08-24 17:06:53
tags: [MoveIt,ROS,古月居]
catagories: ROS机器人
---
# 3.MoveIt + Gazebo仿真配置
## ros_control
- 控制器管理器
提供一种通用的接口来管理不同的控制器
- 控制器
读取硬件状态，发布控制指令，完成每个joint的控制
- 硬件资源
为上下两层提供硬件资源的接口
- 机器人硬件抽象
机器人硬件抽象和硬件资源直接打交道，通过write和read方法完成硬件操作
- 真实机器人
执行接收到的命令
<!--more-->

![](http://qey02i1ot.hb-bkt.clouddn.com/control.jpg)
![](http://qey02i1ot.hb-bkt.clouddn.com/kongzhi.jpg)
<center>MoveIt控制框架</center>

![](http://qey02i1ot.hb-bkt.clouddn.com/fangzhen.jpg)
<center>MoveIt+Gazebo仿真框架</center>

## 启动demo
```bash
$roslaunch marm_moveit_config demo_gazebo.launch
```
![](http://qey02i1ot.hb-bkt.clouddn.com/gazebo.jpg)