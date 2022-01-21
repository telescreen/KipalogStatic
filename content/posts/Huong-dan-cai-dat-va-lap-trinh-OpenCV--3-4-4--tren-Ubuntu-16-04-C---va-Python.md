---
title: "Hướng dẫn cài đặt và lập trình OpenCV (3.4.4) trên Ubuntu 16.04 C++ và Python"
date: 2022-01-21T23:45:09+09:00
slug: 2022-01-21-huong-dan-cai-dat-va-lap-trinh-opencv-3-4-4-tren-ubuntu-16-04-c-va-python
type: posts
draft: false
categories:
  - default
tags:
  - opencv
  - c++
  - Python
  - ubuntu
  - eclipse
  - g++
  - CMake
---

Chào các bạn, mình là dân Cơ điện tử, chẳng thuần điện, chẳng thuần cơ, cũng chẳng thuần IT. Mình đã tiếp xúc với OpenCV từ khá lâu, nhưng trước đây lập trình trên VS, cũng theo các hướng dẫn để cài đặt OpenCV sau đó sử dụng, được 1 thời gian ngắn xong cái đồ án tốt nghiệp rồi lại thôi. Sau này đi làm thì học thêm Python, lại cài OpenCV trên Ubuntu để sử dụng Python, cũng cài như các hướng dẫn trên mạng xong lập trình trên Python bập bõm cũng ok. Được thời gian ngắn lại bỏ đấy. Giờ mới quay lại lập trình bằng C++ thì mấy ngày liền không thể build được do cái lỗi thư viện và trên máy tính mình giờ đây có hàng tá opencv các phiên bản khác nhau. Thư viện trong python2.7 sử dụng trong ROS, opencv trong usr/local/lib, openvino, anaconda... bao nhiêu thể loại, bao nhiêu lần cài đặt nhưng chả hiểu cái vẹo gì.

Tự dưng hôm nay được một ngày đẹp trời, mình ngồi quyết tâm khám phá cái thư viện opencv để cài đặt và sử dụng trên ubuntu cho C++ mới vỡ ra được  nhiều điều. Sau đây mình xin hướng dẫn các bạn cách cài đặt và fix một số lỗi của OpenCV.

#Tiền cài đặt
Kiểm tra xem trong máy tính của bạn đã cài đặt opencv ở đâu chưa
```bash
$> sudo find / -name "*opencv*"
```
Nếu tồn tại nhiều vị trí cài đặt opencv trong máy mà chỗ nào bạn biết không sử dụng thì có thể sử dụng lệnh sau để xóa bỏ:
```bash
$> sudo find / -name "*opencv*" -exec rm -i {} \;
```
Trong đó, dấu '/' là tìm trong toàn bộ thư mục gốc computer, bạn có thể thay đổi folder để giới hạn tìm. Câu lệnh trên chỉ xóa được các file, muốn xóa folder thì thay '-i' bằng '-r' để xóa các folder.

# Cài đặt
Tạo file install\_opencv.sh với nội dung như sau:
```bash
echo "OpenCV installation by learnOpenCV.com"

#Specify OpenCV version
cvVersion="3.4.4"

# Clean build directories
rm -rf opencv/build
rm -rf opencv_contrib/build

# Create directory for installation
mkdir installation
mkdir installation/OpenCV-"$cvVersion"

# Save current working directory
cwd=$(pwd)

sudo apt -y update
sudo apt -y upgrade

sudo apt -y remove x264 libx264-dev

## Install dependencies
sudo apt -y install build-essential checkinstall cmake pkg-config yasm
sudo apt -y install git gfortran
sudo apt -y install libjpeg8-dev libjasper-dev libpng12-dev

sudo apt -y install libtiff5-dev

sudo apt -y install libtiff-dev

sudo apt -y install libavcodec-dev libavformat-dev libswscale-dev libdc1394-22-dev
sudo apt -y install libxine2-dev libv4l-dev
cd /usr/include/linux
sudo ln -s -f ../libv4l1-videodev.h videodev.h
cd $cwd

sudo apt -y install libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
sudo apt -y install libgtk2.0-dev libtbb-dev qt5-default
sudo apt -y install libatlas-base-dev
sudo apt -y install libfaac-dev libmp3lame-dev libtheora-dev
sudo apt -y install libvorbis-dev libxvidcore-dev
sudo apt -y install libopencore-amrnb-dev libopencore-amrwb-dev
sudo apt -y install libavresample-dev
sudo apt -y install x264 v4l-utils

# Optional dependencies
sudo apt -y install libprotobuf-dev protobuf-compiler
sudo apt -y install libgoogle-glog-dev libgflags-dev
sudo apt -y install libgphoto2-dev libeigen3-dev libhdf5-dev doxygen

sudo apt -y install python3-dev python3-pip python3-venv
sudo -H pip3 install -U pip numpy
sudo apt -y install python3-testresources

cd $cwd
############ For Python 3 ############
# create virtual environment
python3 -m venv OpenCV-"$cvVersion"-py3
echo "# Virtual Environment Wrapper" >> ~/.bashrc
echo "alias workoncv-$cvVersion=\"source $cwd/OpenCV-$cvVersion-py3/bin/activate\"" >> ~/.bashrc
source "$cwd"/OpenCV-"$cvVersion"-py3/bin/activate

# now install python libraries within this virtual environment
pip install wheel numpy scipy matplotlib scikit-image scikit-learn ipython dlib

# quit virtual environment
deactivate
######################################

git clone https://github.com/opencv/opencv.git
cd opencv
git checkout $cvVersion
cd ..

git clone https://github.com/opencv/opencv_contrib.git
cd opencv_contrib
git checkout $cvVersion
cd ..

cd opencv
mkdir build
cd build

cmake -D CMAKE_BUILD_TYPE=RELEASE \
            -D CMAKE_INSTALL_PREFIX=$cwd/installation/OpenCV-"$cvVersion" \
            -D INSTALL_C_EXAMPLES=ON \
            -D INSTALL_PYTHON_EXAMPLES=ON \
            -D WITH_TBB=ON \
            -D WITH_V4L=ON \
            -D OPENCV_PYTHON3_INSTALL_PATH=$cwd/OpenCV-$cvVersion-py3/lib/python3.5/site-packages \
        -D WITH_QT=ON \
        -D WITH_OPENGL=ON \
        -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
        -D BUILD_EXAMPLES=ON ..

make -j4
make install

echo "Installation Completed!"
```
Copy file install\_opencv.sh tới thư mục chứa các file cài đặt mà bạn muốn đặt vào. (Theo mình thì nên tạo 1 folder /home/OpenCV) Bởi vì sau khi cài đặt nó sẽ tạo ra 1 số folder tại cùng mức với vị trí để file install\_opencv.sh như /installation, /opencv, /OpenCV-<version_number>-py3, /opencv_contrib.

Cấp quyền thực thi cho file cài đặt:
Terminal và dẫn vào folder chứa file cài đặt
`sudo chmod +x install_opencv.sh`
Chạy file cài đặt:
`sudo ./install_opencv.sh`
<đợi cho quá trình cài đặt kết thúc. Trong quá trình cài đặt đôi chỗ sẽ hỏi có cài hay không (Y/N)?>

Sau khi bạn cài đặt xong, thư viện cài đặt trong /installation/OpenCV-\<cvVersion\>/

#Compile OpenCV C++ bằng g++
Tạo file test_cv_01.cpp với nội dung như sau:
```C++
#include <opencv2/opencv.hpp>
#include <iostream>

using namespace cv;
using namespace std;
//int main( int argc, char** argv )
 int main(){

  Mat image;
  image = imread("cong_phuong.jpg" , CV_LOAD_IMAGE_COLOR);

   if(! image.data ) {
       cout <<  "Could not open or find the image" << std::endl ;
       return -1;
     }

   cv::namedWindow( "Display window", cv::WINDOW_AUTOSIZE );
   cv::imshow( "Display window", image );

   cv::waitKey(0);
  return 0;
}
```
* Cách 1:

```bash
g++ test_cv_01.cpp -o test_cv_01 `pkg-config --cflags --libs <Thư mục cài đặt OpenCV>/installation/OpenCV-3.4.4/lib/pkgconfig/opencv.pc`
```
* Cách 2

```bash
g++ test_cv_01.cpp -o test_cv_01 -I<Thư mục cài đặt OpenCV>/installation/OpenCV-3.4.4/include -L<Thư mục cài đặt OpenCV>/installation/OpenCV-3.4.4/lib -lopencv_core -lopencv_highgui -lopencv_imgcodecs -lopencv_imgproc
```
Run:

```bash
./test_cv_01
```
Kết quả:
![ketqua1](/mwvxkbfo5z_image.png)

>**_Một số lưu ý:_**
* Lệnh g++ phải theo thứ tự như trên, nếu không sẽ gặp lỗi 'undefined...' [ketqua1]
* Khi gặp bất cứ bug nào liên quan đến thư viện, điều đầu tiên cần làm là phải kiểm tra chính tả xem mình viết đúng chưa, có sai chỗ nào không (Mình đã mất mấy buổi để fix bug cuối cùng lòi ra sai chính tả, thừa, thiếu kí tự  :laughing: :laughing: :laughing:)

#Compile OpenCV C++ trên Eclipse
##Cài đặt Eclipse
Hướng dẫn cài đặt Eclipse bạn có thể xem ở [link này](http://ubuntuhandbook.org/index.php/2016/01/how-to-install-the-latest-eclipse-in-ubuntu-16-04-15-10/)
##Tạo project mới trên Eclipse
...
##Cấu hình môi trường Opencv trên Eclipse
....
[link](https://docs.opencv.org/3.4/d7/d16/tutorial_linux_eclipse.html)

Mình sẽ viết tiếp phần hướng dẫn thiết lập môi trường, link thư viện opencv trên Eclipse sau.
(Còn tiếp)

#Compile bằng CMake
Tìm hiểu cơ bản về CMake: [link 1](https://kipalog.com/posts/CMake---Cong-cu-sinh-Makefile--Project-file-cho-source-code-C-C-), [link 2](http://derekmolloy.ie/hello-world-introductions-to-cmake/)

Ở đây, mình sẽ giới thiệu cách sử dụng CMake với chương trình OpenCV C++.
Trong folder *CMakeOpenCV* mình tạo file *CMakeList.txt*, subfolder *build, Images, src*.
Tạo file *displayimage.cpp* trong folder *src* với nội dung:
```C++
#include <stdio.h>
#include <opencv2/opencv.hpp>
using namespace cv;
int main(int argc, char** argv )
{
    printf(CV_VERSION);
    if ( argc != 2 )
    {
        printf("usage: DisplayImage.out <Image_Path>\n");
        return -1;
    }
    Mat image;
    image = imread( argv[1], 1 );
    if ( !image.data )
    {
        printf("No image data \n");
        return -1;
    }
    namedWindow("Display Image", WINDOW_AUTOSIZE );
    imshow("Display Image", image);
    waitKey(0);
    return 0;
}
```

Nội dung file *CMakeLists.txt* như sau:
```cmake
cmake_minimum_required(VERSION 2.8)
project(DisplayImage)
find_package(OpenCV REQUIRED)
include_directories( ${OpenCV_INCLUDE_DIRS})
add_executable( DisplayImage src/displayimage.cpp)
target_link_libraries(DisplayImage ${OpenCV_LIBS})
```
Cây thư mục như sau:
![alt text](/ad3s36a9p7_image.png)

Mở Terminal, cd vào thư mục .../CMakeOpenCV/build
```bash
$ cmake ..
```
Dấu .. ở sau là đường dẫn trỏ tới file CMakeLists.txt, vì file CMakeLists.txt nằm ngoài thư mục gốc CMakeOpenCV nên dấu .. là từ trong build lùi ra 1 thư mục.

Sau khi chạy xong lệnh cmake, ta được kết quả như sau:
![alt text](/d8hzqa1v5f_image.png)

Tiếp tục:
```bash
$ make
```
Sau khi make xong, ta kiểm tra cây thư mục thấy như sau:
![alt text](/orroyc7h1p_image.png)

Chạy chương trình sau khi build xong:
```bash
huynv@huynv-Inspiron-3668:/media/huynv/Data/8.Programming_learning/CppCmake/CMakeOpenCV/build$ ./DisplayImage ../Images/cong_phuong.jpg
```
Kết quả vẫn như hình phía trên.

Quan sát kĩ ở console sau khi thực hiện lệnh cmake ..  ta thấy có dòng
```bash
-- Found OpenCV: /opt/ros/kinetic (found version "3.3.1")
```
Tức là chương trình này đang link tới thư viện opencv được cài đặt trong `/opt/ros/kinetic`, phiên bản OpenCV 3.3.1. Nó khác với thư viện cài đặt mà mình đã hướng dẫn ở phía trên.
Vậy, muốn chỉ định cho chương trình hiểu được vị trí của thư viện OpenCV mình vừa cài đặt ở trên (hoặc bất kì vị trí thư viện OpenCV nào trên máy của mình) chúng ta phải làm như thế nào???
Sau khi search rất nhiều trên mạng và thử nhiều cách thì đây là cách có hiệu quả với mình: Thêm dòng lệnh sau vào trước dòng `find_package(OpenCV REQUIRED)`
```bash
set(CMAKE_PREFIX_PATH /home/huynv/installation/OpenCV-3.4.4/share/OpenCV)
```
Hoặc có thể thay biến **CMAKE\_PREFIX\_PATH** thành biến **OpenCV\_DIR**. Đường dẫn phía sau là đường dẫn tới thư mục cài đặt chứa file *OpenCVConfig.cmake*. Tuy nhiên, theo mình nên sử dụng biến **OpenCV\_DIR** hơn bởi vì trong một số dự án lớn mình sẽ phải dùng nhiều gói khác nhau. Câu lệnh trên với biến **OpenCV\_DIR** chỉ khai báo môi trường cho OpenCV, còn các thư viện khác sẽ không bị ảnh hưởng.
Xóa sạch toàn bộ file và folder trong thư mục /build. Chạy lại các bước như trên. --> OK
(Đây là cách có hiệu quả với mình nhưng mình chưa hiểu rõ ý nghĩa của các biến môi trường trong CMake, mong các bạn góp ý  :blush: )

**Tham khảo**
[1] https://www.learnopencv.com/install-opencv-3-4-4-on-ubuntu-16-04/
[2] https://docs.opencv.org/3.4/d7/d16/tutorial_linux_eclipse.html
[3] http://ubuntuhandbook.org/index.php/2016/01/how-to-install-the-latest-eclipse-in-ubuntu-16-04-15-10/

