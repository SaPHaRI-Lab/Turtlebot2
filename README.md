 **![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcaUwHYFXEhbpmRKNrs-rXpKaGHXryAO5pWwI0KPXQLf8ZRtAFBG4KbLanLjZCD1Y8Nc-D5kFyhZKBfeRNSerG6eZFb7FVuGOkFQI4rZeSYTbf_icit9P8G553bO5lqjwXBgcHPJrQ7J-258zmAfjyJPk4d?key=s6kx8w9aAKgfuro0Mrp5kw)

# Turtlebot2

## Background
TurtleBot is a low-cost, personal robot kit with open-source software. With TurtleBot, you’ll be able to build a robot that can drive around your house. This is Kobuki TurtleBot (TurtleBot 2). The TurtleBot has a mobile base that can move in any direction, a Kinect camera and sensor to see its surroundings and to measure distances and help map its environment. And we have 7 of them.
## Goal
Be able to communicate with all 7 robots at once and have them communicate with each other, enable them to move simultaneously as a swarm, then have them interact with people and measure human-robot behavior.

## Turtlebot2 Overview
**Components:**
1. Kinect Camera and Sensor
2. Speaker
3. Rapsberry Pi (replaces default Jetson TK1)
4. Wheels
5. Body
6. Battery

  
**Kinect Camera**
The Kinect provides depth information and forms a 3-dimensional. Have 3 inbuilt cameras, an RGB, an Infrared, and Depth sensor. And have 4 microphones to receive a sound.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeskm3ebEl5Yl-tMTF7yQx7VJ5V2nBzQb6SV3CYEpYzkWu9CrLgay0EUHtTr3C8g-TcgNBDZvpiNUoQYFrktiGONYxdIWC2FxQk1zEDC86M5TkFA0bh562Q2ldybVhwQ3KvOgbigHGavhhUOBKvD7CQv0G_?key=s6kx8w9aAKgfuro0Mrp5kw)

  

**Raspberry Pi**
The brain of the computer/robot, this is where all the calculations and decisions are made. In this research I use Raspberry Pi 4 Model B 8GB with Starter Kit (including case, fan, heat sync, charger, etc.).

Link from Amazon: [Raspberry Pi](https://www.amazon.com/CanaKit-Raspberry-4GB-Starter-Kit/dp/B08956GVXN/ref=sr_1_2_sspa?crid=1G4BEMXOX5NVU&dib=eyJ2IjoiMSJ9.mP4drOfyakW9P2E6ytjWi16gj2s3LrQBGuFeMtbTEh_hMvprgoi-t-zlc_pvFQPcF8E2O6AESj6Om7ZB9CrmRyba7rYttBJ7UUUxLrY5W3aubaUiDeB1AUSutuUgoQoQpqV06pSq0PIWx2_OFJHtnuSn5FbIeruK-kpbvBaTi5gvTZ_BBcB4iG9R8Kykr_gSTNvACwM9L2azfpDbIXxaEcS_7iFzVMzvMllRlt5t2BY.OT51DETFITwREkwJcV8xwrQ18fpsFBqTiz1Cksqw4_w&dib_tag=se&keywords=raspberry%2Bpi%2B4&qid=1720193522&sprefix=rasp%2Caps%2C98&sr=8-2-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&th=1)


Install Ubuntu 20.04 (64-bit) and all necessary packages:
- kobuki 
- turtlebot
- yujin_ocs
- libfreenect
- turtlebot_msgs
- turtlebot_apps
- turtlebot_simulator

## Getting Started

Install Ubuntu Desktop on Raspberry Pi 4 8GB:

- Install Ubuntu Desktop on Raspberry Pi on Micro SD Card (32 GB preferred ____)
- Download the [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
- Open Raspberry Pi Imager
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfEk9S9muGok_69JQ9_wFfhn9Vb88JbpKIRL2qaasyb4bSmwAiA5fotHrRxcxwO5d0C-XGX7vVeHMuHdPsQ3t0X1FL_Hio2OFS62wKQki54P0nCACh-WYkXC1Mosvx1dzNd7V-5BUceXTIJep92OGYqMKJ9?key=s6kx8w9aAKgfuro0Mrp5kw)

- Choose device: Raspberry Pi 4
- Choose OS: Other general-purpose OS -> Ubuntu -> Ubuntu Server 20.04.05 LTS (64-bit)
- Choose Storage: your Micro SD Card
- Choose Next -> Edit setting
- The hostname, username, password, etc. can be anything. We configured wireless LAN and used:
	- Hostname: ubuntu
	- Username: SaPHaRI
	- Password: **7TurtleBotsX!** (with X being our assigned Turtlebot numbers)
	- Configured wireless LAN to our own lab network
    

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdtskk92z3T32FkdkXlHTUhXhYmyUOzf7909V-DsNr4zZ1j7_h5aMhnyEpfKWwHSow_K2CAhBmuVZ99TTMqj08_ASnJqinstS5OMA39LVOL9En1b9SBI9He2VDz0X8eHLBhYO94lo33F1ZGT1mz1js8n4zx?key=s6kx8w9aAKgfuro0Mrp5kw)

- In Services: Enable SSH (Use password authentication)
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc7DQ4zxonYHdd9CRBJykDt-v-UH9utN-Fb8nmPX2rnh63F9CSGTbtXYgmT045s-fv093jcw13pHN6yNutD-gHWdjSCG8Wc4733WVAtUUZrfiDY_SzSbksuMU4ienj0OeGh6_kFj-wNpzdZU95Qco6qNm4?key=s6kx8w9aAKgfuro0Mrp5kw)
- In Options: Depends on you

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd38wKhGEv3QKH1AZHkfQlUv25zBhKw72mxJQMFrHExRKOztEObG2c-e5WthZMxlwJpH2OUG-7psCbVr_SbEKNLU6soShJ8rZK7Tvji64948IDmLHP4ArEBZs1TTZ7imsUS8QKw0xxL7Ufas19hWuAtGDU?key=s6kx8w9aAKgfuro0Mrp5kw)

- Choose yes: Your files in Micro SD Card will erase and install Ubuntu Server instead
- Wait until it finishes
- Insert Micro SD Card into Raspberry Pi
- Connect Keyboard, Mouse, External Monitor (Micro HDMI to Regular HDMI) before powering up the Raspberry Pi
- After powering up the Raspberry Pi, it should boot up the Ubuntu Server
- When it shows cloud-init … finish at..
    

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdO9HAlX4wqU6ryVqCsdmnO74PqWFx0kfpd5OCYwJ7U5ySs1n9I0T__shiJeI551zsIoOU56mbmS2VIfblnFiLompRehPKmNPk7E2F_vl_D4e7mUw2as_bbub3yLSKsV1KQBNqkykOJStCzuCf-FX1bc7wj?key=s6kx8w9aAKgfuro0Mrp5kw)
- `Ctrl + Alt + F2`
- Login with your username and password that you set up in the beginning

- Update & Upgrade  
  - `sudo apt update`  
  - `sudo apt upgrade`  
  - `sudo apt update && sudo apt upgrade -y` (Recommended)

- Install Ubuntu Desktop  
  - `sudo apt install ubuntu-desktop -y`  
  - `startx` (To enter Ubuntu desktop)

## Install ROS Noetic:
- Following this website “[ROS Noetic](http://wiki.ros.org/noetic/Installation/Ubuntu)”
- Install ROS Noetic Desktop Full version
- In the environment options you can use zshrc or bash, I use bash
  
Control TurtleBot by using the keyboard:
- Open your terminal
	- Make your workspace with `mkdir kobuki_ws`
	- `cd kobuki_ws/`
	- `mkdir src`
	- `make` `
	- `catkin_make`
- Run `cd src/` then clone these repositories in the src:
	- `git clone https://github.com/yujinrobot/kobuki.git`
	- `git clone https://github.com/turtlebot/turtlebot.git`
	- `git clone https://github.com/yujinrobot/yujin_ocs.git`
	- `git clone https://github.com/turtlebot/turtlebot_msgs.git`
	- `git clone https://github.com/turtlebot/turtlebot_apps.git`
	- `git clone https://github.com/turtlebot/turtlebot_simulator.git`
- Configuration 
	- Head to `kobuki_ws/src/yujin_ocs` then delete the folder except for `yocs_cmd_vel_mux`, `yocs_controllers`, and `yocs_velocity_smoother`
- Further installation	
	- `cd` _____
	- `sudo apt install liborocos-kdl-dev`
- Run rosdep command  
	- `cd kobuki_ws/`
	- `rosdep install --from-paths src --ignore-src -r -y`
	- `rosdep update`
	- `catkin_make`
- Source the workspace  
	- `source devel/setup.bash`
- Run the command to connect to the TurtleBot  
	- `lsusb` (to check if the TurtleBot is connected or not)  
	- `roslaunch turtlebot_bringup minimal.launch`
- Open another terminal to run the command to control  
	- `roslaunch turtlebot_teleop keyboard_teleop.launch`
- If in connect part or control part has an error  
	- `python3 --version`  
	- `sudo ln -s /usr/bin/python3 /usr/bin/python`
- After turning on the Raspberry Pi again might have an error  
	- `catkin_make`  
	- `source devel/setup.bash`
- Run the desired command again  
	- Use this command to avoid running `source devel/setup.bash` every time after `catkin_make`  
	- `echo "source your_ws/devel/setup.bash" >> ~/.bashrc`
	- `source ~/.bashrc`

## Turn on the camera:  
- Open the terminal
	- `sudo apt update && sudo apt upgrade -y` (Recommended)  
- Install the dependencies in your workspace
	- `cd kobuki_ws/src/`  
	- `sudo apt install git-core cmake freeglut3-dev pkg-config build-essential libxmu-dev libxi-dev libusb-1.0-0-dev -y`  
	- `git clone https://github.com/OpenKinect/libfreenect.git`
- Make and install 
	- `cd libfreenect`  
	- `mkdir build`  
	- `cd build`  
	- `cmake -L ..`  
	- `make`  
	- `sudo make install`  
	- `sudo ldconfig /usr/local/lib64/`
- To use Kinect without sudoing every time  
	- `sudo adduser $USER video`  
	- `sudo adduser $USER plugdev`
- Add some device rules  
	- `sudo nano /etc/udev/rules.d/51-kinect.rules`
	- Paste the following into above command and save:
```
# ATTR{product}=="Xbox NUI Motor"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02b0", MODE="0666"
# ATTR{product}=="Xbox NUI Audio"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02ad", MODE="0666"
# ATTR{product}=="Xbox NUI Camera"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02ae", MODE="0666"
# ATTR{product}=="Xbox NUI Motor"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02c2", MODE="0666"
# ATTR{product}=="Xbox NUI Motor"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02be", MODE="0666"
# ATTR{product}=="Xbox NUI Motor"
SUBSYSTEM=="usb", ATTR{idVendor}=="045e", ATTR{idProduct}=="02bf", MODE="0666"
```

**Need to generate audio drivers for support in freenect folder**
- `cd libfreenect`
- `python3 src/fwfetcher.py`
- Copy to a specific location (without this command still work)
- `sudo cp src/audios.bin /usr/local/share/libfreenect`
- Open the Kinect camera (Run command in libfreenect folder)
- `freenect-micview` (Checking the audio)
- `Ctrl + c`
- `freenect-glview` (Open the camera)
- `freenect-camtest` (To test camera)

**Turn on the camera on Rviz:**
- `cd kobuki_ws/src`
- `git clone https://github.com/ros-drivers/freenect_stack.git`
- Get back to workspace
	- `cd kobuki_ws`
	- `cd ..` (Recommended)
	- `catkin_make`
	- `source devel/setup.bash`
	- `roslaunch freenect_launch freenect.launch depth_registration:=true`
	- `rviz`
- Global Options
- Set fixed frame to `camera_link`

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcPv0RzYZM7lzzI1F9DROsNuaTHmjLlQAirSWPPS7z8yzQSPFgIrgPOIIl48zmf9XL8s6btg25xdVq6tP6Hr0gi7y6FEV_ReO4zjrVT0XNyKMw691bOJjgKyFg31QEXHIeUrOJz57HVogMZjfnl-34EMM4?key=s6kx8w9aAKgfuro0Mrp5kw)

- Add pointcloud2
- Set the topic -> /camera/depth_registered/points
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdsWZlzsUEnnLTaYFQ15p6EzN10owXgu0eOxB7GGbcdLU8TPaTNxYsUPr_A9eScc_nHrKBH4fOxCdBuyopa-SLmkIZ0Hdzk_owakUd99b5Jmv2S9Mp7tGhu20OZw1UMB1YLYS2o8w245_ufA-GZp3oIuRsE?key=s6kx8w9aAKgfuro0Mrp5kw)

## Turn on camera on RTAB-MAP and Rviz:
- Open the terminal  
- Connect to the TurtleBot  
- `roslaunch turtlebot_bringup minimal.launch`  
- Connect to the camera  
- `roslaunch freenect_launch freenect.launch depth_registration:=true`  
- Install RTAB-MAP package  
- `sudo apt install ros-noetic-rtabmap-ros -y`  
- Open the RTAB-MAP  
- `roslaunch rtabmap_ros rtabmap.launch rtabmap_args:="--delete_db_on_start"`  
- If facing any error on running the RTAB-MAP try this command  
- `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/ros/noetic/lib`  
- `echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/ros/noetic/lib' >> ~/.bashrc`  
- `source ~/.bashrc`  
- Open the Rviz  
- `rosrun rviz rviz`  
- Add option on Rviz  
- Global options -> Set fixed frame -> `map`  
- Add RobotModel  
- Add PointCloud2 -> Set topic -> `/rtabmap/cloud_map`

## Communicate between 2 terminals:  
- First terminal  
- `roscore`  
- Second terminal  
- `rostopic pub /test std_msgs/String "data: 'Hello from master'"`  
- Third terminal  
- `rostopic echo /test`

**Communication between 2 Raspberry Pi:**  
- First Raspberry Pi  
	- First Terminal
		- `roscore`  
	- Second Terminal  
		- `export ROS_MASTER_URI=http://<Hostname or IP Address>:11311`  
		- `export ROS_IP=<IP of Machine>`  
		- `rosrun beginner_tutorials talker.py`  
- Second Raspberry Pi
	- First Terminal
		- `export ROS_MASTER_URI=http://<Hostname or IP Address>:11311`  
		- `export ROS_IP=<IP of Machine>`  
		- `rosrun beginner_tutorials listener.py`

**Communication between 2+ Raspberry Pis:**  
- Do the same as 2 Raspberry Pi  
- Add more devices  
	- `export ROS_MASTER_URI=http://<Hostname or IP Address>:11311`  
	- `export ROS_IP=<IP of Machine>`  
	- `rosrun beginner_tutorials listener.py`

## Control TurtleBot across Raspberry Pi:  
- Assumed Rasp1 is the master (talker) and Rasp 2 is a subscriber (listener). Control TurtleBot (Rasp 2) by using Rasp 1  
- Rasp 1 (IP Address: 192.168.0.91)  
	- `export ROS_MASTER_URI=http://192.168.0.91:11311`  
	- `export ROS_IP=192.168.0.91`  
	- `roscore`
- Rasp 2 (IP Address: 192.168.0.190)  
	- `export ROS_MASTER_URI=http://192.168.0.91:11311`  
	- `export ROS_IP=192.168.0.190`  
	- `roslaunch turtlebot_bringup minimal.launch`  
- Rasp 1  
	- In another terminal:	`roslaunch turtlebot_teleop keyboard_teleop.launch`

## Control 2 TurtleBots at the same time:  
- Assumed Rasp1 is the master (talker) and Rasp 2, 3 are subscribers (listeners). Control TurtleBot (Rasp 2, 3) by using Rasp 1  
- Rasp 1 (IP Address: 192.168.0.91)
	- `export ROS_MASTER_URI=http://192.168.0.91:11311`  
	- `export ROS_IP=192.168.0.91`  
	- `roscore`  
	- `roslaunch turtlebot_teleop keyboard_teleop.launch`  
- Rasp 2 (IP Address: 192.168.0.190)  
	- `export ROS_MASTER_URI=http://192.168.0.91:11311`  
	- `export ROS_IP=192.168.0.190`  
	- `roslaunch turtlebot_bringup minimal.launch __ns:=<turtlebotname>` (need this command because to control 2 TurtleBots at the same time, you need different node names)
	- In another terminal  
		- `rosrun topic_tools relay <original topic name> <new topic name because of namespace change>`
		- `/cmd_vel_mux/input/teleop`  
		- `/turtlename/cmd_vel_mux/input/teleop`
- Rasp 3 (IP Address: 192.168.0.8)  
	- `export ROS_MASTER_URI=http://192.168.0.91:11311`  
	- `export ROS_IP=192.168.0.8`  
	- `roslaunch turtlebot_bringup minimal.launch __ns:=<turtlebotname>` (need this command because to control 2 TurtleBots at the same time, you need different node names)
	- In another terminal  
		- `rostopic list`  
		- `rosrun topic_tools relay <original topic name> <new topic name because of namespace change>`  
		- `/cmd_vel_mux/input/teleop`  
		- `/turtlename/cmd_vel_mux/input/teleop`

## Adding the new user in ubuntu:
Add a User via Recovery Mode
- Reboot the System
	- Reboot the system, and as it boots up, hold the **Shift** key to access the **GRUB** boot menu
- Select Recovery Mode
	- In the GRUB menu, select the **Advanced options for Ubuntu.** Then select the **Recovery mode** for your current kernel (the one ending in (recovery mode))
- Drop to Root Shell
	- Once in the recovery mode, select **Root - Drop to root shell prompt.** 
- Remount the Filesystem
	- `mount -o remount,rw /`
- Create a New User
	- `adduser newusername`
	- newusername = enter the desire name
	- Then follow the pormpts to set the password and other details for the new user
	- In other details
		- Full name, Room number, Phone number, etc. you can skip by press **enter** key
- Grant Sudo Privileges
	- `usermod -aG sudo newusername`
	- newusername = enter the name that you put in the create a new user part
- Reboot the System
	- `reboot`
	- Boots up in log in with the new user
**Username: SaPHaRI** 
**Password: 7TurtleBots!**