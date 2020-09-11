---
title: ROS摄像头驱动和数据接口
date: 2020-08-12 16:11:00
tags: [古月居,ROS机器视觉开发入门]
catagories: ROS机器人
---

## usb_cam功能包中的话题


|  |名称 |类型  |描述  |
| :---:|:---: | :---: | :---: |
| Topic发布 |~<camera_name/image  |sensor_msgs/Image  |发布图像数据  |
<!--more-->
## usb_cam功能包中的参数

| 参数 |类型  |默认值  |描述  |
| --- | --- | --- | --- |
| ~video_device |string  |'/dev/video0'  |摄像头设备号  |
|~image_width  |int  | 640 |图像横向分辨率  |
|~image_height |int  | 480 |图像纵向分辨率  |
|~pixel_format  |string  |'mjpeg'  | 像素编码，可选值：mjpeg,yuyv,uyvy |
|~io_method  |string  |'mmap'  |IO通道，可选值：mmap,read,userptr  |
|~camera_frame_id  |string  |'head_camera'  |摄像头坐标系  |
|~framerate  |int  |30  |帧率  |
|~brightness  |int  |32  |亮度，0~255  |
|~saturation  |int  |32  |饱和度，0~255  |
|~contrast  |int  |32  |对比度，0~255  |
|~sharpness  |int  |22  |清晰度，0~255 |
|~autofocus  |bool  |false  |自动对焦  |
|~focus  |int  |51  |焦点（非自动对焦状态下有效）  |
|~camera_info_url  |string  |-  |摄像头校准文件路径  |
|~camera_name|string|'head_camera'|摄像头名称|

## 命令行安装usb_cam功能包(usb_cam驱动安装）
```bash
$ sudo apt-get install ros-melodic-usb-cam 
$ roslaunch usb_cam usb_cam-test.launch
$ rqt_image_view
```
第一条命令`$ sudo apt-get install ros-melodic-usb-cam` 回车后会出现以下问题
![](http://qey02i1ot.hb-bkt.clouddn.com/1.jpg)

可采用下面的代码安装usb_cam驱动
```bash
$ cd ros_ws/src
$ git clone https://github.com/bosch-ros-pkg/usb_cam.git
$ cd ..
$ catkin_make
$ source ros_ws/devel/setup.bash
```
第二条命令`roslaunch usb_cam usb_cam-test.launch`的作用是启动摄像头
查看文件`usb_cam-test.launch`
```html 
<launch>
  <node name="usb_cam" pkg="usb_cam" type="usb_cam_node" output="screen" >
    <param name="video_device" value="/dev/video0" />
    <param name="image_width" value="640" />
    <param name="image_height" value="480" />
    <param name="pixel_format" value="yuyv" />
    <param name="camera_frame_id" value="usb_cam" />
    <param name="io_method" value="mmap"/>
  </node>
  <node name="image_view" pkg="image_view" type="image_view" respawn="false" output="screen">
    <remap from="image" to="/usb_cam/image_raw"/>
    <param name="autosize" value="true" />
  </node>
</launch>
```

运行第二条命令后不要退出，另外打开新终端运行第三条Qt指令

## usb_cam话题发布二维图像消息的数据结构
命令行运行`rosmsg show sensor_msgs/Image`结果如下

![](http://qey02i1ot.hb-bkt.clouddn.com/2.jpg)


> - header：消息头，包括消息序号、时间戳、绑定坐标系(frame_id)
> - height：图像的纵向分辨率
> - width：图像的横向分辨率
> - encoding：图像的编码格式，包含RGB，YUV等常用格式，不涉及图像压缩编码
> - is_bigendian：图像数据的大小端存储模式
> - step：一行图像数据的字节数量，作为数据的步长参数
> - data：存储图像数据的数组，大小为step\*height
>
>1280\*720分辨率的摄像头产生一帧图像的数据大小是：3\*1280\*720=2764800byte=2.7648MB

## usb_cam话题压缩图像消息的数据结构
命令行运行`rosmsg show sensor_msgs/CompressedImage`显示结果如下


![](http://qey02i1ot.hb-bkt.clouddn.com/3.jpg)


>- format：图像的压缩编码格式（jpeg、png、bmp）
>- data 和 header同上

## 深度相机Kinect安装驱动和freenect_camera功能包
### freenect_camera功能包中的话题和服务
freenect_camera功能包中的话题和服务

![](http://qey02i1ot.hb-bkt.clouddn.com/Image.png)

### 安装驱动
```bash
$ sudo apt-get install ros-melodic-freenect-*
$ git clone https://github.com/avin2/SensorKinect.git
$ cd SensorKinect/Bin
$ tar xvf SensorKinect093-Bin-Linux-x64-v5.1.2.1.tar.bz2
$ sudo ./install.sh (在解压出来的文件夹路径下）
```
### 运行
```bash
$ roslaunch freenect_launch freenect.launch
```
 ## freenect_camera话题发布点云图像消息的数据结构
 命令行运行`rosmsg show sensor_msgs/PointCloud2`后的显示结果

![](http://qey02i1ot.hb-bkt.clouddn.com/Image2.png)

 >- height：点云图像的纵向分辨率
 >- width：点云图像的横向分辨率
 >- fields：每个点的数据类型
 >- is_biogendian：数据的大小端存储模式
 >- point_step：单点的数据字节步长
 >- row_step：一行数据的字节步长
 >- data：点云数据的存储数组，总字节大小为row_step\*height
 >- is_dense：是否有无效点
 >
 >点云单帧数据量也很大，如果使用分布式网络传输，需要考虑能否满足数据的传输要求，或者针对数据进行压缩

## 深度相机RealSense安装SDK和驱动
### 安装SDK
安装SDK
```bash
$ mkdir build
$ cd build
$ cmake ..
$ make
$ sudo make install
```
### 安装ROS驱动
安装ROS驱动
```bash
$ catkin_make-DCATKIN_ENABLE_TESTING=False-DCMAKE_BUILD_TYPE=Release
$ catkin_make install
$ echo "source ~/ros_ws/devel/setup.bash">> ~/.bashrcsource ~/.bashrc
```
### 点云显示
点云显示
```bash
$ roslaunch relalsense2_camera rs_rgbd.launch
$ rosrun rviz rviz
```
