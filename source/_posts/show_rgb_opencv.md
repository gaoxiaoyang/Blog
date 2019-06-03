---
title: 'show image, convert yuv to rgb in opencv'
date: 2019-05-27 12:28:59
tags: 
     - opencv
     - rgb
     - yuv
---



下面程序读取camera.yuv ,然后使用`Mat` 保存，再用`cv:cvtColor`将`yuv` 数据转化成`rgb`显示



```c++
#include <opencv/cv.h>
#include <fstream>
#include <iostream>
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>

const int width = 1920;
const int height = 1080;
const int framesize = width * height * 3 / 2;
using namespace std;
using namespace cv;
int main()
{
		ifstream fin;
		fin.open("camera.yuv", ios_base::in | ios_base::binary);
		if (fin.fail())
		{
			cout << "the file is error" << endl;
			return -1;
		}

		fin.seekg(0, ios::end);//设置文件指针到文件流的尾部  
		streampos ps = fin.tellg();//指出当前的文件指针  
		unsigned long NumberPixe = ps;
		cout << "file size: " << ps << endl;//输出指针的位置  
		unsigned FrameCount = ps / framesize; //帧大小  
		cout << "frameNuber: " << FrameCount; //输出帧数  
		fin.close();
		FILE* fileIn = fopen("camera.yuv", "rb+");
		unsigned char* pYuvBuf = new unsigned char[framesize]; //一帧数据大小

		//存储到图像  
		cv::namedWindow("yuv", 1);

		for (int i = 0; i < FrameCount; ++i)
		{
			fread(pYuvBuf, framesize * sizeof(unsigned char), 1, fileIn);
			cv::Mat yuvImg;
			yuvImg.create(height * 3 / 2, width, CV_8UC1);
			memcpy(yuvImg.data, pYuvBuf, framesize * sizeof(unsigned char));
			cv::Mat rgbImg;
			cv::cvtColor(yuvImg, rgbImg, CV_YUV2BGR_I420);

			cv::imshow("yuv", yuvImg); //只显示y分量
			cv::imshow("rgbImg", rgbImg);

			printf("第 %d 帧\n", i);
			int c = waitKey(10000);
			if ((char)c == 27)
			{
				break;
			}
		}
		fclose(fileIn);
		cvDestroyWindow("yuv");
}
```

