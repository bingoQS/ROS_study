# ROS图像与Opencv图像的相互转换 #

# 摘要

本文将介绍ROS图像与Opencv图像转换的API，并通过编写一个节点进行测试。

## 1.综述 ##

ROS用自己的sensor_msgs/Image的消息格式显示图像，为了方便开发者用OpenCV中的接口，ROS提供了CbBridge类来完成两者的相互转换。下面将介绍两者转换的函数。



![image-20210426104251051](C:\Users\qiubin\AppData\Roaming\Typora\typora-user-images\image-20210426104251051.png)

## 2.ROS图像转化为OpenCV图像

头文件：<cv_bridge/cv_bridge.h>。利用cv_bridge::CvImagePtr类得到Opencv图像，两种方法：

（1）如果要对图像进行修改，利用下面的函数复制一份图像数据。

```c++
cv_bridge::CvImagePtr cv_cam;
//对于sensor_msgs::Image类型的ROS图像
cv_cam = cv_bridge::toCvCopy(const sensor_msgs::Image& source,
                    const std::string& encoding = std::string());
//对于sensor_msgs::ImageConstPtr类型的ROS图像
cv_cam = cv_bridge::toCvCopy(const sensor_msgs::ImageConstPtr& source,
                    const std::string& encoding = std::string());
```

（2）如果不需要修改图像数据，可以安全的分享ROS图像数据。

```c++
//对于sensor_msgs::Image类型的ROS图像
cv_cam = cv_bridge::toCvShare(const sensor_msgs::Image& source,
                    const std::string& encoding = std::string());
//对于sensor_msgs::ImageConstPtr类型的ROS图像
cv_cam = cv_bridge::toCvShare(const sensor_msgs::ImageConstPtr& source,
                    const std::string& encoding = std::string());
```

## 3.OpenCV图像转换到ROS图像

使用cv_bridge::CvImagePtr类的成员函数：toImageMsg()。

```c++
sensor_msgs::ImagePtr toImageMsg() const;
```

## 4.一个ROS节点例子

```c++
//
// Created by qiubin on 2021/4/23.
//
#include <ros/ros.h>
#include<cv_bridge/cv_bridge.h>
#include<sensor_msgs/Image.h>
#include<opencv2/opencv.hpp>

cv::Mat image_cv;
ros::Publisher pub;

void callback_image(const sensor_msgs::Image& img)
{
    cv_bridge::CvImagePtr cv_cam;
    try{
        cv_cam =cv_bridge::toCvCopy(img,sensor_msgs::image_encodings::RGB8);
    }
    catch (cv_bridge::Exception& ex)
    {
        ROS_ERROR("cv_bridge exception in rgbcallback: %s", ex.what());
        exit(-1);
    }

    if(cv_cam->image.rows>60&&cv_cam->image.rows>60)
    {
        cv::circle(cv_cam->image,cv::Point(60,60),30,CV_RGB(0,0,255)); 
    }

    cv::imshow("处理后",cv_cam->image);
    cv::waitKey(3);

    pub.publish(cv_cam->toImageMsg());
}

int main(int argc,char** argv){
    ros::init(argc,argv,"vision_test");
    ros::NodeHandle n;
    ros::Subscriber sub=n.subscribe("usb_cam1/image_raw",1,callback_image);//订阅的图像topic
    pub=n.advertise<sensor_msgs::Image>("pub_image",10);//将修改后的图像发布出来
    ros::spin();//等待回调函数
    return 0;
}
```

