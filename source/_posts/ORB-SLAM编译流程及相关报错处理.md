---
title: ORB_SLAM编译流程及相关报错处理
author: HWH
tags:
  - SLAM
  - Linux
categories: Tutorial
abbrlink: 5bc15fb5
date: 2022-11-18 19:56:33
---


## 写在前面
[ORB-SLAM](http://webdiis.unizar.es/~raulmur/orbslam/)是一种多功能且精确的单目SLAM解决方案，能够在各种环境下实时计算摄像机轨迹和稀疏的3D场景重建，从小型手持序列到在多个城市驾驶的汽车块。它能够关闭大型环路，并实时地从宽基线进行全局重新定位。
注意：本次编译使用的环境是安装在虚拟机中的Ubuntu18.04，且ORB_SLAM是基于ROS运行的，因此请确保已安装好linux环境及ROS系统。

## 准备工作
### 1.虚拟机及ROS准备(未使用虚拟机的可跳过此步)
安装虚拟机，并安装好Ubuntu18.04以及ROS系统，详细教程可参见[此处](https://www.ngui.cc/51cto/show-23557.html)
由于是在虚拟机中执行，需要让虚拟机连接到外接USB摄像头或者电脑本机摄像头，详细教程可参见[此处](https://blog.csdn.net/weixin_43849277/article/details/109061739)

### 2.ORB_SLAM所需环境准备
首先安装好opencv，网上教程颇多可以自行查阅。
然后使用如下两个命令分别安装boost和eigen3

> sudo apt-get install libboost-all-dev
>
> sudo apt-get install libeigen3-dev

详细内容可参考ORB_SLAM提供的README.md文档

### 3.安装
####  1.源码下载
访问[ORB_SLAM在github上的地址](https://github.com/raulmur/ORB_SLAM)下载源码。
或者在终端执行如下命令：

> git clone https://github.com/raulmur/ORB_SLAM.git ORB_SLAM

#### 2.添加环境变量
将克隆ORB_SLAM的路径添加到ROS_PACKAGE_PATH环境变量。为此，请修改.bashrc并在底部添加以下行（替换PATH_TO_PARENT_OF_ORB_SLAM）

> export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:PATH_TO_PARENT_OF_ORB_SLAM

例如：如果ORB_SLAM文件夹在主文件夹中则将PATH_TO_PARENT_OF_ORB_SLAM替换为~/ORB_SLAM

#### 3.编译g2o和DBoW2库
分别进入到Thirdparty/g2o/和Thirdparty/DBoW2/目录下执行如下命令：

>  mkdir build
 cd build
 cmake .. -DCMAKE_BUILD_TYPE=Release
 make

####  4.编译ORB_SLAM
退回到ORB_SLAM/目录下执行如下命令

>  mkdir build
 cd build
 cmake .. -DCMAKE_BUILD_TYPE=Release
 make

## 编译ORB_SLAM报错处理
### 1.缺少opencv2
报错如下图所示
![缺少opencv2](https://img-blog.csdnimg.cn/20210219094426398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)
解决方法：删除manifest.xml文件中的 ``< depend package=“opencv2”/ >`` 行，如下图所示

![修改manifest.xml](https://img-blog.csdnimg.cn/20210219094558784.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)

### 2.缺少头文件
出现类似如下错误：

```cpp
 In member function ‘void ORB_SLAM::ORBextractor::ComputeKeyPoints(std::vector<std::vectorcv::KeyPoint >&)’:
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:607:63: error: ‘FAST’ was not declared in this scope
FAST(cellImage,cellKeyPoints[i][j],fastTh,true);
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:616:34: error: ‘ORB’ has not been declared
if( scoreType == ORB::HARRIS_SCORE )
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:683:17: error: ‘KeyPointsFilter’ has not been declared
KeyPointsFilter::retainBest(keysCell,nToRetain[i][j]);
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:699:13: error: ‘KeyPointsFilter’ has not been declared
KeyPointsFilter::retainBest(keypoints,nDesiredFeatures);
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc: In member function ‘void ORB_SLAM::ORBextractor::operator()(cv::InputArray, cv::InputArray, std::vectorcv::KeyPoint&, cv::OutputArray)’:
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:760:82: error: ‘GaussianBlur’ was not declared in this scope
GaussianBlur(workingMat, workingMat, Size(7, 7), 2, 2, BORDER_REFLECT_101);
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc: In member function ‘void ORB_SLAM::ORBextractor::ComputePyramid(cv::Mat, cv::Mat)’:
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:800:78: error: ‘INTER_LINEAR’ was not declared in this scope
resize(mvImagePyramid[level-1], mvImagePyramid[level], sz, 0, 0, INTER_LINEAR);
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:800:90: error: ‘resize’ was not declared in this scope
resize(mvImagePyramid[level-1], mvImagePyramid[level], sz, 0, 0, INTER_LINEAR);
^
/home/jy/catkin_ws/src/ORB_SLAM-master/src/ORBextractor.cc:803:80: error: ‘INTER_NEAREST’ was not declared in this scope
resize(mvMaskPyramid[level-1], mvMaskPyramid[level], sz, 0, 0, INTER_NEAREST);
^
make[2]: *** [CMakeFiles/ORB_SLAM-master.dir/src/ORBextractor.cc.o] 错误 1
make[1]: *** [CMakeFiles/ORB_SLAM-master.dir/all] 错误 2
make: *** [all] 错误 2
```
解决方法：添加缺少的头文件，我的出错是ORBextractor.cc文件，修改如下：

![添加头文件](https://img-blog.csdnimg.cn/20210219095104204.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)

### 3.Eigen3库的bug
如果出现如下报错：

```cpp
/usr/local/include/eigen3/Eigen/src/Core/util/StaticAssert.h:32:40: error: static assertion failed:
YOU_MIXED_DIFFERENT_NUMERIC_TYPES__YOU_NEED_TO_USE_THE_CAST_METHOD_OF_MATRIXBASE_TO_CAST_NUMERIC_TYPES_EXPLICITLY
 #define EIGEN_STATIC_ASSERT(X,MSG) static_assert(X,#MSG);
```
解决方法：打开Thirdparty/g2o/g2o/solvers/linear_solver_eigen.h，将以下代码：

> typedef Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic, SparseMatrix::Index> PermutationMatrix;

替换为：

> typedef Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic, int> PermutationMatrix;

如下图所示：
![修改eigen](https://img-blog.csdnimg.cn/20210219100103228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)

### 4.undefined reference to symbol '_ZN5boost6system15system_categoryEv'
解决方法：如果出现此错误修改CMakeLists.txt文件如下所示：
![修改CMakeLists.txt](https://img-blog.csdnimg.cn/20210219100345756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)

## 写在最后
所有ORB_SLAM编译流程均参考ORB_SLAM提供的README.md文档，如有疑问可以详细阅读此文档，或者评论区指出。
参考文档：

> https://github.com/raulmur/ORB_SLAM/blob/master/README.md
> https://www.ngui.cc/51cto/show-23557.html
> https://blog.csdn.net/weixin_43849277/article/details/109061739
> https://www.geek-share.com/detail/2764681691.html
> https://blog.csdn.net/danmeng8068/article/details/83827245
