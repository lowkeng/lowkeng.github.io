---
title: MoveIt编程入门（2）
date: 2020-08-24 17:35:51
catagoris: ROS机器人
tags: [MoveIt,ROS,古月居]
---
# 3.笛卡尔空间运动
笛卡尔路径约束，路径点之间的路径形状是一条直线/圆弧或其他轨迹

工作空间规划例程
```bash
$roslaunch probot_anno_moveit_config demo.launch
$rosrun probot_demo moveit_cartesian_demo.py _cartesian:=True(走直线）
$rosrun probot_demo moveit_cartesian_demo.py _cartesian:=False(走自由曲线）
```
<!--more-->
关键API的实现
```python
(plan, fraction) = arm.compute_cartesian_path (
		                            waypoints,   # waypoint poses，路点列表
		                            0.01,          # eef_step，终端步进值
		                            0.0,           # jump_threshold，跳跃阈值
		                            True)        # avoid_collisions，避障规划
```
- plan：规划出来的运动轨迹
- fraction：描述规划成功的轨迹在给定路点列表中的覆盖率[0~1]。如果fraction小于1，说明给定路点列表没办法完整规划

# 4.自主避障运动
MoveIt可以在运动规划时检测碰撞，并规划轨迹绕过障碍
![](http://qey02i1ot.hb-bkt.clouddn.com/plan.jpg)
<center>规划场景模块的结构</center>

监听信息
>- 状态信息（state information）  机器人的关节话题
>- 传感器信息（sensor information）机器人的传感器数据
>- 外界环境信息（world geometry information） 周围环境信息

## 第一种方式 Rviz插件接口
通过MoveIt可视化插件添加模型，在运动规划时会考虑碰撞检测

启动demo.launch(打开Rviz，加载模型)
```bash
$roslaunch probot_anno_moveit_config demo.launch
```
scene objects --> publish scene

## 第二种方式 编程接口
附着物体避障例程
```bash
$roslaunch probot_anno_moveit_config demo.launch
$rosrun probot_demo moveit_attached_object_demo.py
```
`moveit_attached_object_demo.py`
```python
# 移除场景中之前运行残留的物体
scene.remove_attached_object(end_effector_link, 'tool')
scene.remove_world_object('table') 
scene.remove_world_object('target')

# 设置桌面的高度
table_ground = 0.6

# 设置table和tool的三维尺寸
table_size = [0.1, 0.7, 0.01]
tool_size = [0.2, 0.02, 0.02]

# 设置tool的位姿
p = PoseStamped()
p.header.frame_id = end_effector_link

p.pose.position.x = tool_size[0] / 2.0 - 0.025
p.pose.position.y = -0.015
p.pose.position.z = 0.0
p.pose.orientation.x = 0
p.pose.orientation.y = 0
p.pose.orientation.z = 0
p.pose.orientation.w = 1

# 将tool附着到机器人的终端
scene.attach_box(end_effector_link, 'tool', p, tool_size)

# 将table加入场景当中
table_pose = PoseStamped()
table_pose.header.frame_id = 'base_link'
table_pose.pose.position.x = 0.25
table_pose.pose.position.y = 0.0
table_pose.pose.position.z = table_ground + table_size[2] / 2.0
table_pose.pose.orientation.w = 1.0
scene.add_box('table', table_pose, table_size)

rospy.sleep(2)  

# 更新当前的位姿
arm.set_start_state_to_current_state()

# 设置机械臂的目标位置，使用六轴的位置数据进行描述（单位：弧度）
joint_positions = [0.827228546495185, 0.29496592875743577, 1.1185644936946095, -0.7987583317769674, -0.18950024740190782, 0.11752152218233858]
arm.set_joint_value_target(joint_positions)

# 控制机械臂完成运动
arm.go()
rospy.sleep(1)

# 控制机械臂回到初始化位置
arm.set_named_target('home')
arm.go()
```