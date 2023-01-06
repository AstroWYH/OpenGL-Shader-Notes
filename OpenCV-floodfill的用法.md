```cpp
#include <opencv2/opencv.hpp>  
#include <opencv2/imgproc/imgproc.hpp>  
using namespace cv;

int main()
{
	system("CHCP 65001");
	Mat src = imread("1.jpg");
	imshow("src", src);
	Rect ccomp;
    // src是
	floodFill(src, Point(50, 300), Scalar(155, 255, 55), &ccomp, Scalar(10, 10, 10), Scalar(10, 10, 10));
	imshow("dst", src);
	waitKey(0);
	return 0;
}

// 去小岛用例
// temp_lightnum为该小岛像素数
// label_image为原始图像
// cv::Point(x, y)为起始位置
// label_count为覆盖的new val
// 0,0为上下范围都为0，即严格覆盖和当前坐标(x, y)相同值的位置为new val
int temp_lightnum = cv::floodFill(label_image, cv::Point(x, y), label_count, &rect, 0, 0, 4);

```

