Install packages for DJI PSDK ros2 wrapper

Necessary hardware
* Drone : Matrice 350RTK
* Jetson nano
* sd card 64Gb +
* Necessary Hardware for DJI connection [here](https://developer.dji.com/doc/payload-sdk-tutorial/en/quick-start/quick-guide/jetson-nano.html)

### Installation
1) Download and flash jetson nano Ubuntu 20.04 image from [here](https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image)

After booting on jetson image for first time run :
```
sudo apt-get update
sudo apt-get upgrade   
sudo apt-get install automake
sudo apt-get install libaio-dev
```

2) Follow the instructions to install libopus 1.3.1 [here](https://opus-codec.org/release/stable/2019/04/12/libopus-1_3_1.html)
```
tar -xzvf opus-1.3.1.tar.gz
cd opus-1.3.1/
autoreconf  -f  -i
./configure
make -j16
sudo make install
``` 

3) Follow the instructions to install ffmpeg.4.3.2 download from [here](https://ffmpeg.org/releases/?C=N;O=D)
```
tar -zxvf ffmpeg-4.3.2.tar.gz
./configure --enable-shared --prefix=/usr/local
make && make install
sudo ldconfig
ffmpeg -version
```

If you encouter errors ensure that the compiled FFmpeg binary has executable permissions. Run:
``` 
ls -l /home/administrator/ffmpeg-4.3.2/ffmpeg 
```

Look for the x in the permissions part of the output, which indicates executable permissions. If it's missing, you need to add executable permissions to the binary:
```
chmod +x /home/administrator/ffmpeg-4.3.2/ffmpeg
``` 

Attempt to run FFmpeg directly using its full path to ensure there's no issue with the binary itself:
```
/home/administrator/ffmpeg-4.3.2/ffmpeg -version
export LD_LIBRARY_PATH=/home/administrator/ffmpeg-4.3.2/lib:$LD_LIBRARY_PATH
```
add the last line to your ~/.bashrc
Verify opencv installation and install libusb
```
opencv_version
sudo apt-get install libusb-1.0-0-dev
```
PSDK offers a Jetson Nano Usb Bulk Configuration File download from [here](https://terra-1-g.djicdn.com/71a7d383e71a4fb8887a310eb746b47f/psdk/e-port/usb-bulk-configuration-reference.zip) ，including the configuration script (psdk-usb-configure.sh) and configuration program (startup_bulk).
```
/* Back up the bulk configuration file of jetson nano */
cp /opt/nvidia/l4t-usb-device-mode-start.sh ~/Desktop/nano-usb-config/nv-l4t-usb-device-mode-start-bk.sh
/* Using PSDK's bulk configuration file */
sudo cp startup_bulk/psdk-usb-configure.sh  /opt/nvidia/nv-l4t-usb-device-mode-start.sh
/* Set the startup_bulk file path in the /opt/nvidia/nv-l4t-usb-device-mode-start.sh */
/home/dji/Desktop/startup_bulk/startup_bulk /dev/usb-ffs/bulk1 &
/home/dji/Desktop/startup_bulk/startup_bulk /dev/usb-ffs/bulk2 &
/* After restarting the system, make sure that startup_bulk is running normally */
ps -aux | grep startup_bulk
```

 Register for a PSDK Developer Account and follow the rest installation instructions from the [PSDK](https://developer.dji.com/doc/payload-sdk-tutorial/en/quick-start/quick-guide/jetson-nano.html) 

In order to install network device if you have a wireless interface :
This command provides a list of devices with their VID and PID.
```
lsusb
```
You'll see output similar to this:
```
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 005: ID 045e:0752 Microsoft Corp. Wired Keyboard 400
Bus 001 Device 004: ID 04ca:0061 Lite-On Technology Corp. 4-Port USB 2.1 Hub
Bus 001 Device 003: ID 0bda:8179 Realtek Semiconductor Corp. RTL8188EUS 802.11n Wireless Network Adapter
```

The ID 0bda:8179 corresponds to the VID and PID of the device. 
These are the values you would use in the SDK configuration to specify the wireless network adapter you're working with. 
Configure the currently used wireless network interface controller device name, VID, and PID in the following files(lines 13-19).

samples/sample_c/platform/linux/nvidia_jetson/hal/dji_network.h
samples/sample_c++/platform/linux/nvidia_jetson/hal/dji_network.h

Install ROS2 Humble :
Follow the instructions [here](https://nvidia-isaac-ros.github.io/getting_started/isaac_ros_buildfarm_cdn.html)

Following the ROS2 Humble installation, install the psdk_ros2 wrapper following the installation steps [here](https://umdlife.github.io/psdk_ros2/documentation/Introduction.html)

Ensure you have the same app infos at all the following files:
PSDK can not run without filling in the information.

    samples/sample_c/platform/linux/nvidia_jetson/application/dji_sdk_app_info.h
    samples/sample_c++/platform/linux/nvidia_jetson/application/dji_sdk_app_info.h

Update with the proper app infos the ROS2 PSDK wrapper. 
```
~/psdk_ros2_ws/src/psdk_ros2/psdk_wrapper/cfg/psdk_params.yaml
```
