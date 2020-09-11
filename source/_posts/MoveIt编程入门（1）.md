---
title: MoveIt编程入门（1）
date: 2020-08-24 17:28:45
catagories: ROS机器人
tags: [ROS,MoveIt,古月居]
---
# MoveIt编程接口
![](http://qey02i1ot.hb-bkt.clouddn.com/move_group.jpg)
<center>MoveIt核心节点--move_group</center>

- 连接控制需要的规划组planning group
- 设置目标位姿（关节空间或笛卡尔空间）
- 设置运动约束（可选）
- 使用MoveIt规划一条到达目标的轨迹
- 修改轨迹（如速度等参数）
- 执行规划出的轨迹
<!--more-->
# 2.关节空间运动
点到点运动：不需要在笛卡尔空间规划末端运动轨迹，机器人各个关节运动不需要联动（每个关节的运动是独立规划的）

## 第一种方法 设置各个关节点位姿角度
正向运动学规划例程
```bash
$roslaunch probot_anno_moveit_config demo.launch
$rosrun probot_demo moveit_fk_demo.py
```
`moveit_fk_demo.py`
```python
# 初始化需要使用move group控制的机械臂中的arm group
arm = moveit_commander.MoveGroupCommander('manipulator')

# 设置机械臂运动的允许误差值
arm.set_goal_joint_tolerance(0.001)

# 设置允许的最大速度和加速度
arm.set_max_acceleration_scaling_factor(0.5)
arm.set_max_velocity_scaling_factor(0.5)

# 控制机械臂先回到初始化位置
arm.set_named_target('home')
arm.go()
rospy.sleep(1)

# 设置机械臂的目标位置，使用六轴的位置数据进行描述（单位：弧度）
joint_positions = [0.391410, -0.676384, -0.376217, 0.0, 1.052834, 0.454125]
arm.set_joint_value_target(joint_positions)

# 控制机械臂完成运动
arm.go()
rospy.sleep(1)

# 控制机械臂先回到初始化位置
arm.set_named_target('home')
arm.go()
rospy.sleep(1)
```
关键API的实现步骤
```python
# 初始化需要使用move group控制的机械臂中的arm group
arm = moveit_commander.MoveGroupCommander('manipulator')
# 设置机械臂的目标位置，使用六轴的位置数据进行描述（单位：弧度）
joint_positions = [0.391410, -0.676384, -0.376217, 0.0, 1.052834, 0.454125]
arm.set_joint_value_target(joint_positions)
# 控制机械臂完成运动
arm.go()
```
- 创建规划组的控制对象
- 设置关节空间运动的目标位姿
- 完成规划并控制机械臂完成运动

## 第二种方法 设置xyz空间位置
逆向运动学规划例程
```bash
$roslaunch probot_anno_moveit_config demo.launch
$rosrun probot_demo moveit_ik_demo.py
```
`moveit_ik_demo.py`
```python
# 设置机械臂工作空间中的目标位姿，位置使用x、y、z坐标描述，
# 姿态使用四元数描述，基于base_link坐标系
target_pose = PoseStamped()
target_pose.header.frame_id = reference_frame
target_pose.header.stamp = rospy.Time.now()     
target_pose.pose.position.x = 0.2593
target_pose.pose.position.y = 0.0636
target_pose.pose.position.z = 0.1787
target_pose.pose.orientation.x = 0.70692
target_pose.pose.orientation.y = 0.0
target_pose.pose.orientation.z = 0.0
target_pose.pose.orientation.w = 0.70729

# 设置机器臂当前的状态作为运动初始状态
arm.set_start_state_to_current_state()

# 设置机械臂终端运动的目标位姿
arm.set_pose_target(target_pose, end_effector_link)

# 规划运动路径
traj = arm.plan()

# 按照规划的运动路径控制机械臂运动
arm.execute(traj)
rospy.sleep(1)
```
关键API的实现步骤
```python
# 初始化需要使用move group控制的机械臂中的arm group
arm = moveit_commander.MoveGroupCommander('manipulator')
# 获取终端link的名称
end_effector_link = arm.get_end_effector_link()
# 设置目标位置所使用的参考坐标系
reference_frame = 'base_link'
arm.set_pose_reference_frame(reference_frame)
# 规划运动路径
traj = arm.plan()
# 按照规划的运动路径控制机械臂运动
arm.execute(traj)
```
- 创建运动规划组的控制对象
- 获取机器人的终端link名称
- 设置目标位姿对应的参考坐标系和起始、终止位姿
- 完成规划并控制机械臂完成运动