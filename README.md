# Streaming Kinect One (Kinect V2) sensors


## Abstract
This repository demonstrates how to stream Kinect One sensors with libfreenect2 and convert streaming images into cv::mat in OpenCV. 

## Installation
Before running the sample code provided in this repository, you will need to install [libfreenect2](https://github.com/OpenKinect/libfreenect2) and [OpenCV](https://github.com/Itseez/opencv). 

#### libfreenect2

libfreenect2 is an open-source driver for the Kinect for Windows v2 device (Kinect One). Please follow the instruction provided in [libfreenect2 installation](https://github.com/OpenKinect/libfreenect2#installation) to install the driver for using your Kinect One sensors. 

If you faced installation or running issues, please check [Troubleshooting](https://github.com/OpenKinect/libfreenect2/wiki/Troubleshooting) first. There are already some instructions addressing different issues reported by users. Most likely, there are already solutions for your problem. 


#### OpenCV

Please install whatever version of OpenCV suits you best. There are already a lot of installation guilds on the internet. Here is one example: [OpenCV 3.0 installation](http://docs.opencv.org/3.0-beta/doc/tutorials/introduction/linux_install/linux_install.html). 

## Hardware requirement for multiple Kinect Sensors
Each Kinect sensor requires about 3Gbps on the PCIe bus, therefore, in order to use multiple Kinect sensors, typically you will need to connect additional Kinect sensor with USB 3.0 controller. For example, if you want to use two Kinect sensors, you can connect first Kinect sensor directly to USB 3.0 port on your machine, and connect the second Kinect sensor to the additional USB 3.0 controller attach with your machine. I am personally using [Anker® Uspeed USB 3.0 PCI-E Express Card](https://www.anker.com/products/68UPPCIE-4SU). 

After connecting multiple Kinect sensors, you can run multiple instances of *Protonect* from libfreenect2 to see if you have enough bandwidth for streaming multiple Kinect sensors.

```C++
./Protonect 007618650247 //serial number of your Kinect device
./Protonect 114231241047 //serial number of your Kinect device
```

### Using multiple Kinect Sensors
The example code provided in this repository is only intended to work with single Kinect sensor. If you would like to extract streaming data from multiple Kinect sensors, you can simply initial additional **dev**, **listener** and **frame**. The most easiest and stupid way is shown as below. 

- Open multiple devices
```C++
dev_0 = freenect2.openDevice(serial_0); //serial_0 = serial number of your 1st Kinect device
dev_1 = freenect2.openDevice(serial_1); //serial_1 = serial number of your 2nd Kinect device
```
- Set up multiple listeners for each device
```C++
libfreenect2::SyncMultiFrameListener listener_0(libfreenect2::Frame::Color | libfreenect2::Frame::Depth | libfreenect2::Frame::Ir);
libfreenect2::SyncMultiFrameListener listener_1(libfreenect2::Frame::Color | libfreenect2::Frame::Depth | libfreenect2::Frame::Ir);
```
- Pass listeners to each device
```C++
dev_0->setColorFrameListener(&listener_0);
dev_0->setIrAndDepthFrameListener(&listener_0);
dev_1->setColorFrameListener(&listener_1);
dev_1->setIrAndDepthFrameListener(&listener_1);
```
- Start each device
```C++
dev_0->start();
dev_1->start();
```
- Initialize multiple frames for each device
```C++
libfreenect2::FrameMap frames_0;
libfreenect2::FrameMap frames_1;
```
- Extracting from multiple devices
```C++
listener_0.waitForNewFrame(frames_0);
libfreenect2::Frame *rgb = frames_0[libfreenect2::Frame::Color];
libfreenect2::Frame *ir = frames_0[libfreenect2::Frame::Ir];
libfreenect2::Frame *depth = frames_0[libfreenect2::Frame::Depth];

listener_1.waitForNewFrame(frames_1);
libfreenect2::Frame *rgb = frames_1[libfreenect2::Frame::Color];
libfreenect2::Frame *ir = frames_1[libfreenect2::Frame::Ir];
libfreenect2::Frame *depth = frames_1[libfreenect2::Frame::Depth];
```

- Do whatever fancy work after you obtained cv::Mat




