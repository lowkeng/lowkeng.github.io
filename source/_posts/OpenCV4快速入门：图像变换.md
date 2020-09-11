---
title: OpenCV4快速入门：图像变换
date: 2020-08-24 17:38:28
tags: [OpenCV,OpenCV4快速入门,图像处理,图像变换]
catagories: OpenCV学习
---
# 3.3图像变换
本节将介绍如何通过OpenCV4中的函数实现图像形状的变换，包括图像的尺度变换、图像翻转以及图像的旋转。
## 3.3.1图像连接
vconcat()函数用于实现图像数据或矩阵数据的上下连接，该函数可以连接存放在数组变量中的两个或多个Mat类型的数据。
<!--more-->
vconcat()函数原型1
```c
void cv::vconcat ( const Mat *src,
                             size_t nsrc,
                             OutputArray dst)
```
>- src：Mat矩阵类型的数组（要求数组中所有Mat类元素具有相同的**列数**并且具有相同的**数据类型**和**通道数**）
>- nsrc：数组中Mat类型数据的数目
>- dst：连接后Mat类矩阵

vconcat()函数原型1
```c
void cv::vconcat ( InputArray src1,
                             InputArray src2,
                             OutputArray dst )
```
>- src1：第一个需要连接的Mat类矩阵
>- src2：第二个需要连接的Mat类矩阵，与第一个具有相同的宽度、数据类型和通道数
>- dst：连接后的Mat类型矩阵

在拼接结果中src1在上方，src2在下方
同理，实现图像数据或矩阵数据左右横向连接的函数是hconcat(),也有两种原型，不再赘述。

以下代码给出了对小型Mat类矩阵连接和图像进行连接的示例程序。该程序中分别对数组和Mat类矩阵进行两个方向的连接，并对一幅图像的4个象限的子图像进行拼接，最终连接成原始图像。
```cpp
#include<opencv2/opencv.hpp>
#include<iostream>

using namespace cv;
using namespace std;

int main()
{
	//矩阵数组的横竖连接
	Mat matArray[] = { Mat(1,2,CV_32FC1,cv::Scalar(1)),Mat(1,2,CV_32FC1,cv::Scalar(2)) };
	Mat vout, hout;
	vconcat(matArray, 2, vout);
	cout << "图像数组竖向连接：" << endl << vout << endl;
	hconcat(matArray, 2, hout);
	cout << "图像数组横向连接：" << endl << hout << endl;

	//矩阵的横竖拼接
	Mat A = (cv::Mat_<float>(2, 2) << 1, 7, 2, 8);
	Mat B = (cv::Mat_<float>(2, 2) << 4, 10, 5, 11);
	Mat vC, hC;
	vconcat(A, B, vC);
	cout << "多个图像竖向连接：" << endl << vC << endl;
	hconcat(A, B, hC);
	cout << "多个图像横向连接：" << endl << hC << endl;

	//读取四个子图像，00左上角，01右上角，10右下角，11右下角
	Mat img00 = imread("lena00.png");
	Mat img01 = imread("lena01.png");
	Mat img10 = imread("lena10.png");
	Mat img11 = imread("lena11.png");
	if (img00.empty() || img01.empty() || img10.empty() || img11.empty())
	{
		cout << "请确认文件名称是否正确" << endl;
		return -1;
	}
	//显示4个子图像
	imshow("img00", img00);
	imshow("img01", img01);
	imshow("img10", img10);
	imshow("img11", img11);

	//图像连接
	Mat img, img0, img1;
	//图像横向连接
	hconcat(img00, img01, img0);
	hconcat(img10, img11, img1);
	//横向连接结果再进行竖向连接
	vconcat(img0, img1, img);

	//显示连接图像的结果
	imshow("img0", img0);
	imshow("img1", img1);
	imshow("img", img);
	waitKey(0);
	return 0;
}
```
程序中小型Mat矩阵的连接结果:
![](http://qey02i1ot.hb-bkt.clouddn.com/%E5%9B%BE3-19.png)
程序中图像连接的结果：
![](http://qey02i1ot.hb-bkt.clouddn.com/%E5%9B%BE3-20.png)

## 3.3.2图像尺寸变换
图像的尺寸变换实际就是改变图像的长和宽，实现图像的缩放。

将图像修改成指定尺寸的resize()函数的原型
```cpp
void cv::resize(InputArray src.
                        OutputArray dst,
                        Size dsize,
                        double fx = 0,
                        double fy = 0,
                        int interpolation = INTER_LINEAR)
```
>- dsize：输出图像的尺寸
>- fx：水平轴的比例因子，如果水平轴变成原来的两倍则赋值2
>- fy：垂直轴的比例因子，如果垂直轴变为原来的两倍则赋值2
>- interpolation：插值方法的标志，可选参数及含义见表3-3

resize()函数的dsize和fx(fy)同时可以调整输出图像的参数，因此两类参数在实际使用时只需要使用一类，当两种方法算出来的输出图像尺寸不一致时，以dsize设置的图像尺寸为准。两种调整图像尺寸的参数的关系如下式
```math
dsize = Size ( round(fx*src.cols),round(fy*src.rows))
```

表3-3   插值方法标志

| 编制参数 |简记  |作用  |
| --- | --- | --- |
| INTER_NEAREST |0  | 最近邻插值法 |
|INTER_LINEAR  |1  |双线性插值法  |
|INTER_CUBIC  | 2 |双三次插值法  |
|INTER_AREA  | 3 |使用像素区域关系重新采样，首选用于图像缩小  |
| INTER_LANCZOS4 |4  |Lanczo插值法  |
|INTER_LINEAR_EXACT  |5  |位精确双线性插值法  |
|INTER_MAX  |7  |用掩码进行插值  |
一般来讲，如果要缩小图像，那么通常使用INTER_AREA标志会有较好效果，而在放大图像时，采用INTER_CUBIC和INTER_LINEAR标志通常会有表较好的效果，前者计算速度慢，效果更好，后者计算速度快，效果也较好。

以下代码给出了图像缩放的示例程序。程序首先以灰度的形式读入一幅图像，之后利用INTER_AREA将图像缩小，并分别用INTER_CUBIC、INTER_NEAREST和INTE_LINEAR三种方法将图像放大到相同的尺寸。
```cpp
#include<opencv2/opencv.hpp>
#include<iostream>

using namespace cv;
using namespace std;

int main()
{
	Mat gray = imread("lena.png", IMREAD_GRAYSCALE);
	if (gray.empty())
	{
		cout << "请确认图像文件名称是否正确" << endl;
		return -1;
	}

	Mat smallImg, bigImg0, bigImg1, bigImg2;
	resize(gray, smallImg, Size(15, 15), 0, 0, INTER_AREA);  //先将图像缩小
	resize(smallImg, bigImg0, Size(30, 30), 0, 0, INTER_NEAREST);  //最近邻差值
	resize(smallImg, bigImg1, Size(30, 30), 0, 0, INTER_LINEAR);  //双线性差值
	resize(smallImg, bigImg2, Size(30, 30), 0, 0, INTER_CUBIC);  //双三次差值
	namedWindow("smallImg", WINDOW_NORMAL);  //图像尺寸太小，一定要设置可以调节窗口大小标志
	imshow("smallImg", smallImg);
	namedWindow("bigImg0", WINDOW_NORMAL);
	imshow("bigImg0", bigImg0);
	namedWindow("bigImg1", WINDOW_NORMAL);
	imshow("bigImg1", bigImg1);
	namedWindow("bigImg2", WINDOW_NORMAL);
	imshow("bigImg2", bigImg2);
	waitKey(0);
	return 0;
}
```
程序运行结果：
![](http://qey02i1ot.hb-bkt.clouddn.com/%E5%9B%BE3-21.png)

## 3.3.3图像翻转变换
图像翻转变换函数flip()函数原型
```cpp
void cv::flip(InputArray src,
                    OutputArray dst,
                    int flipCode)
```
>- flipCode：翻转方式标志。数值大于0表示绕y轴进行翻转；等于0表示绕x轴进行翻转；大于0表示既绕y轴又绕x轴翻转

该函数功能和参数都比较简单，不过多阐述。

## 3.3.4 图像仿射变换
在OpenCV4中并没有专门用于图像旋转的函数，而是通过图像的仿射变换实现旋转。实现图像的旋转，首先要确定旋转角度和旋转中心，之后确定旋转矩阵，最后通过仿射变换实现图像旋转。

计算旋转矩阵的getRotationMatrix2D()函数的函数原型：
```cpp
Mat cv::getRotationMatrix2D(Point2f center,
                                                  double angle,
                                                  double scale)
```
>- center：图像旋转中心位置
>- angle：图像旋转角度，单位为度，正值为逆时针旋转
>- scale：两个轴的比例因子，可以实现旋转过程中的图像缩放

该函数返回图像旋转矩阵，返回的数据类型是一个Mat类，一个2x3的矩阵。如果我们已知图像旋转矩阵，那么可以自己生成旋转矩阵而不用调用该函数。该函数生成的旋转矩阵与旋转角度和旋转中心的关系式如下：
```math
Rotation = \begin{bmatrix}
\alpha  & \beta  &\left ( 1-\alpha  \right )*center.x - \beta *center.y \\ 
-\beta  &\alpha   & \beta *center.x +\left ( 1-\alpha \right )*center.y
\end{bmatrix}
```
其中：
```math
\alpha = scale*cos(angle)  
\qquad\beta = scale*sin(angle)
```
在确定旋转矩阵后，通过warpAffine()函数进行仿射变换，就可以实现图像的旋转，
仿射变换warpAffine()函数的函数原型：
```cpp
void cv::warpAffine(InputArray src,
                                OutputArray dst,
                                InputArray M,
                                Size dsize,
                                int flags = INTER_LINEAR,
                                int borderMode = BORDER_CONSTANT,
                                const Scalar& borderValue = Scalar())
```
>- dst：仿射变换后输出图像，与src数据类型一致，尺寸与dsize相同
>- M：2x3旋转变换矩阵
>- dsize：输出图像尺寸
>- flags：插值方法标志，参数含义见表3-3和表3-4
>- borderMode：像素边界外推方法的标志
>- borderValue：填充边界使用的数值，默认情况下为0


表3-4    图像仿射变换插值方法标志：
| 方法标志 |简记  |作用  |
| --- | --- | --- |
|WARP_FILL_OUTLIERS  |8  |填充所有输出图像的像素，如果部分像素落在输入图像的边界外，则它们的值设定为fillval  |
|WARP_INVERSE_MAP  |16  |设置为M输出图像到输入图像的反变换  |

仿射变换就是图像的旋转、平移和缩放操作的统称，可以表示为线性变换和平移变换的叠加，其数学表示是先乘以一个线性变换矩阵再加上一个平移向量，其中线性变换矩阵为2x2的矩阵A，平移向量为2x1的向量B。旋转变换矩阵M与线性变换矩阵A、平移矩阵B的关系为：
```math
M = [A\quad B] =\begin{bmatrix}
a_{00} &a_{01} &b_{00}  \\ 
a_{10} &a_{11} &b_{10}  
\end{bmatrix}
```
用线性变换矩阵A和平移矩阵B，以及图像像素值[x,y]^T，仿射变换的数学原理可以表示为：
```math
T = A\begin{bmatrix}
x\\ 
y
\end{bmatrix}+B
```
T为变换后的像素值。

仿射变换又称为三点变换。如果知道变换前后两幅图像中3个像素点坐标的对应关系就可以求得仿射变换中的变换矩M。OpenCV4提供了利用3各对应像素点来确定变换矩阵M的函数getAffineTransform(),其原型如下：
```cpp
Mat cv::getAffineTransform(const Point2f src[],
                                               const Point2f dst[])
```
>- src[]：原图像中的3个像素坐标
>- dst[]：变换后图像中与原图像对应的3个像素坐标

该函数两个输入量都是存放浮点坐标的数组，在生成数组的时候，与像素点的输入顺序无关，但是要保证像素点的对应关系，函数的返回值是一个2x3的Mat类型矩阵。

以下代码例程实现了图像的旋转以及图像三点映射的仿射变换
```cpp
#include <opencv2\opencv.hpp>
#include <iostream>
#include <vector>

using namespace std;
using namespace cv;

int main()
{
	Mat img = imread("lena.png");
	if (img.empty())
	{
		cout << "请确认图像文件名称是否正确" << endl;
		return -1;
	}

	Mat rotation0, rotation1, img_warp0, img_warp1;
	double angle = 30;  //设置图像旋转的角度
	Size dst_size(img.rows, img.cols);  //设置输出图像的尺寸
	Point2f center(img.rows / 2.0, img.cols / 2.0);  //设置图像的旋转中心
	rotation0 = getRotationMatrix2D(center, angle, 1);  //计算放射变换矩阵
	warpAffine(img, img_warp0, rotation0, dst_size);  //进行仿射变换
	imshow("img_warp0", img_warp0);
	//根据定义的三个点进行仿射变换
	Point2f src_points[3];
	Point2f dst_points[3];
	src_points[0] = Point2f(0, 0);  //原始图像中的三个点
	src_points[1] = Point2f(0, (float)(img.cols - 1));
	src_points[2] = Point2f((float)(img.rows - 1), (float)(img.cols - 1));
	dst_points[0] = Point2f((float)(img.rows)*0.11, (float)(img.cols)*0.20);  //放射变换后图像中的三个点
	dst_points[1] = Point2f((float)(img.rows)*0.15, (float)(img.cols)*0.70);
	dst_points[2] = Point2f((float)(img.rows)*0.81, (float)(img.cols)*0.85);
	rotation1 = getAffineTransform(src_points, dst_points);  //根据对应点求取仿射变换矩阵
	warpAffine(img, img_warp1, rotation1, dst_size);  //进行仿射变换
	imshow("img_warp1", img_warp1);
	waitKey(0);
	return 0;
}
```
仿射变换程序运行结果如下图：
![](http://qey02i1ot.hb-bkt.clouddn.com/%E5%9B%BE3-23.png)

## 3.3.4图像透视变换
透视变换常用在机器人视觉导航研究中，由于相机视角与地面存在倾斜角使得物体成像产生畸变，通过透视变换可以实现对物体图像的校正。

在透视变换中，变换前后的图像关系可以用一个3x3的变换矩阵表示，该矩阵可以通过两幅图像中4各对应点像素坐标求得，因此透视变换又叫做四点变换。
4各对应点坐标求变换矩阵的getPerspectiveTransform()函数原型：
```cpp
Mat cv::getPerspectiveTransform(const Point2f src[],
                                                        const Point2f dst[],
                                                        int solveMethod = DECOMP_LU)
```
>- solveMethod：透视变换矩阵计算方法的标志，默认情况是选择最佳主轴元素的高斯消元法DECOMP_LU，参数含义见表3-6
表3-6 透视变换矩阵计算方法的标志

| 标志参数 |简记  |作用  |
| --- | --- | --- |
|DECOMP_LU  |0  |最佳主轴元素的高斯消元法  |
|DECOMP_SVD  |1  |奇异值分解（SVD）方法  |
|DECOMP_EIG  |2  |特征值分解法  |
|DECOMP_CHOLESKY  |3  |choleskey分解法  |
|DECOMP_QR |4  |QR分解法  |
|DECOMP_NORMAL |16  |使用正规方程公式，可以与其它标志一起使用  |

直接进行透视变换的函数warpPerspective()函数原型：
```cpp
void cv::warpPerspective(InputArray src,
                                OutputArray dst,
                                InputArray M,
                                Size dsize,
                                int flags = INTER_LINEAR,
                                int borderMode = BORDER_CONSTANT,
                                const Scalar& borderValue = Scalar())
```
该函数参数含义与warpAffine()函数的参数含义相同，不再赘述。

以下代码例程给出了将相机视线不垂直于平面拍摄的二维码图像经过透视变换变成相机视线垂直于平面的二维码图像。
```cpp
#include <opencv2\opencv.hpp>
#include <iostream>

using namespace cv;
using namespace std;

int main()
{
	Mat img = imread("noobcvqr.png");
	if (img.empty())
	{
		cout << "请确认图像文件名称是否正确" << endl;
		return -1;
	}

	Point2f src_points[4];
	Point2f dst_points[4];
	//通过Image Watch查看的二维码四个角点坐标
	src_points[0] = Point2f(94.0, 374.0);
	src_points[1] = Point2f(507.0, 380.0);
	src_points[2] = Point2f(1.0, 623.0);
	src_points[3] = Point2f(627.0, 627.0);
	//期望透视变换后二维码四个角点的坐标
	dst_points[0] = Point2f(0.0, 0.0);
	dst_points[1] = Point2f(627.0, 0.0);
	dst_points[2] = Point2f(0.0, 627.0);
	dst_points[3] = Point2f(627.0, 627.0);
	Mat rotation, img_warp;
	rotation = getPerspectiveTransform(src_points, dst_points);  //计算透视变换矩阵
	warpPerspective(img, img_warp, rotation, img.size());  //透视变换投影
	imshow("img", img);
	imshow("img_warp", img_warp);
	waitKey(0);
	return 0;
}
```
透视变换的程序效果如下：
![](http://qey02i1ot.hb-bkt.clouddn.com/%E5%9B%BE3-25.png)