
### October 15th, 2024

**Objective:** Testing on LiDAR Sensor

**Experiment:** I have no micro usb to usb. So, connect by UART (pins TX, RX).
- Pins on LiDAR Sensor
	- Orange: TX
	- Green: RX
	- Black: VCC_5V
	- White: GND
	- Red: GND_MOTO
	- Blue: CTRL_MOTO
	- Yellow: 5V
![[RPLIDAR A1M8 Cables.png]]
![[Raspberry Pi 4 Pins.jpg]]
But its turn out not working! Dr. Block suggest me try to use micro usb instead. I borrow the micro usb to usb from the HFI lab, its work!
![[Connect LiDAR into Raspberry Pi by micro usb.png]]
Checking the sensor on rviz by sunning this `roslaunch rplidar_ros view_rplidar_a1.launch` on both pink computer and Raspberry Pi then get both error! (different error).

**Result:** Cannot use in the rviz yet.


### October 16th, 2024

**Objective:** Testing the doorbell

**Experiment:** After I failed to use the lidar sensor from yesterday. So, I move on into doorbell instead for today.
![[RF Receiver MX-RM-5V.png]]
- RF Receiver pins (Start from left)
	- 1st: GND - Connect to a ground in pin 6th
	- 2nd: DATA (Signal) - Connect to a GPIO pin 11th
	- 3rd: ANT (Antenna) - Skip
	- 4th: 5V power - Connect to a 5V in pin 4th

After all cables are connected. To setup the software before use the RF receiver.
- Install WiringPi, which will help control GPIO pins
	- `sudo apt install wiringpi`
- Install 433Utils
	- `cd kobuki_ws/src`
	- `git clone https://github.com/ninjablocks/433Utils.git`
	- `cd 433Utils/RPi_utils`
	- `make`

But I cannot run `make` I get some error (RCSwitch library is missing). I try to fix by
- Install the RCSwitch manually
	- `cd kobuki_ws/src/433Utils/RPi_utils`
	- `git clone https://github.com/sui77/rc-switch.git`
	- `make`

I still get the error but different one so this one is wiringPi library is missing (So confused due to I just install it). So, I fix it by
- `git clone https://github.com/WiringPi/WiringPi.git`
- `cd WiringPi`
- `./build`

I cannot build this package so I try to use another package instead.
- Install pigpio
	- `wget https://github.com/joan2937/pigpio/archive/master.zip`
	- `unzip master.zip`
	- `cd pigpio-master`
	- `make`
	- `sudo make install`
- To start the pigpio
	- sudo systemctl enable pigpiod

ERROR! pigpoid is not exist!

I try in another way. So, with new connection.
- RF Receiver pins (Start from left) | Picture on the above
	- 1st: GND - Connect to a ground in pin 6th
	- 2nd: DATA (Signal) - Connect to a GPIO pin 11th/13th
	- 3rd: ANT (Antenna) - Skip
	- 4th: 5V power - Connect to a 5V in pin 4th
- Open terminal
	- `sudo apt-get install python3-pip`
	- `sudp pip3 install rpi-rf`
	- `nano rpi-rf_receive.py`
```
#!/usr/bin/env python3

import argparse
import signal
import sys
import time
import logging

from rpi_rf import RFDevice

rfdevice = None

# pylint: disable=unused-argument
def exithandler(signal, frame):
    rfdevice.cleanup()
    sys.exit(0)

logging.basicConfig(level=logging.INFO, datefmt='%Y-%m-%d %H:%M:%S',
                    format='%(asctime)-15s - [%(levelname)s] %(module)s: %(message)s', )
  
parser = argparse.ArgumentParser(description='Receives a decimal code via a 433/315MHz GPIO device')
parser.add_argument('-g', dest='gpio', type=int, default=27,
                    help="GPIO pin (Default: 27)")
args = parser.parse_args()
 
signal.signal(signal.SIGINT, exithandler)
rfdevice = RFDevice(args.gpio)
rfdevice.enable_rx()
timestamp = None
logging.info("Listening for codes on GPIO " + str(args.gpio))
while True:
    if rfdevice.rx_code_timestamp != timestamp:
        timestamp = rfdevice.rx_code_timestamp
        logging.info(str(rfdevice.rx_code) +
                     " [pulselength " + str(rfdevice.rx_pulselength) +
                     ", protocol " + str(rfdevice.rx_proto) + "]")
    time.sleep(0.01)
rfdevice.cleanup()
```
- After finished the setup try the code
	- `sudo python3 rpi-rf_receive.py`
- Press the doorbell button
	- receive the signal

Including the date and time to record; need to figure it out how to record this!

**Result:** Can received the signal.


### October 17th, 2024

**Objective:** Try to launch the TurtleBot by pressing the doorbell button.

**Experiment:** I add 2 more files and move all file into `cd kobuki_ws/src`.
- This file is to send the node to enable the TurtleBot to move autonomously
	- `cd kobuki_ws/src`
	- `nano rpi-rf_receive+launch.py`
```
#!/usr/bin/env python3

import argparse
import signal
import sys
import time
import logging
import os

from rpi_rf import RFDevice

rfdevice = None

# Handle exit signal
def exithandler(signal, frame):
    rfdevice.cleanup()
    sys.exit(0)

logging.basicConfig(level=logging.INFO, datefmt='%Y-%m-%d %H:%M:%S',
                    format='%(asctime)-15s - [%(levelname)s] %(module)s: %(message)s', )

# Argument parser for GPIO pin
parser = argparse.ArgumentParser(description='Receives a decimal code via a 433/315MHz GPIO device')
parser.add_argument('-g', dest='gpio', type=int, default=27,
                    help="GPIO pin (Default: 27)")
args = parser.parse_args()


# Setup exit handler
signal.signal(signal.SIGINT, exithandler)
rfdevice = RFDevice(args.gpio)
rfdevice.enable_rx()

timestamp = None
logging.info("Listening for codes on GPIO " + str(args.gpio))

# Main loop to listen for RF codes
while True:
    if rfdevice.rx_code_timestamp != timestamp:
        timestamp = rfdevice.rx_code_timestamp
        logging.info(str(rfdevice.rx_code) +
                     " [pulselength " + str(rfdevice.rx_pulselength) +
                     ", protocol " + str(rfdevice.rx_proto) + "]")
    time.sleep(0.01)

    # Trigger TurtleBot to move when an RF code is received
    if rfdevice.rx_code:
        os.system("python3 /home/SaPHaRI/kobuki_ws/src/send_goal.py")

rfdevice.cleanup()
```
- Need to find the exact coordinate to config in the file (send_goal.py)
	- Find by do path planning, using 2D pose and 2D navigate
	- It will show in the 5th terminal that you lunched rviz
		- This coordinate is just to test the system
		- Setting Pose
			- Frame: map
			- -0.087 -0.152 0.181
		- Setting Goal
			- Frame: map
			- Position: 0.655 -0.862 0.000
			- Orientation: 0.000 0.000 -0.753 0.658
			- Angle: -1.704
	- Create send_goal file
		- `cd kobuki_ws/src`
		- `nano send_goal.py`
```
#!/usr/bin/env python3

import rospy
from geometry_msgs.msg import PoseStamped

def send_goal():
    rospy.init_node('send_goal')

    pub = rospy.Publisher('/move_base_simple/goal', PoseStamped, queue_size=10)
    rospy.sleep(1)  # Allow time for the node to initialize

    goal = PoseStamped()

    # Set header information
    goal.header.frame_id = "map"
    goal.header.stamp = rospy.Time.now()

    # Set goal position (example values)
    goal.pose.position.x = 0.655
    goal.pose.position.y = -0.862
    goal.pose.position.z = 0.000

    # Set goal orientation (example quaternion)
    goal.pose.orientation.x = 0.0
    goal.pose.orientation.y = 0.0
    goal.pose.orientation.z = -0.0753
    goal.pose.orientation.w = 0.658
    
    pub.publish(goal)
    rospy.loginfo("Goal sent to TurtleBot!")

if __name__ == '__main__':
    try:
        send_goal()
    except rospy.ROSInterruptException:
        pass
```

Cannot even launch this file, I get the error that no module geometry but I already installed.
- Install again
	- `sudo apt-get install ros-noetic-geometry-msgs`
	- `source /opt/ros/noetic/setup.bash`
	- `source ~/kobuki_ws/devel/setup.bash`
- Rebuild the workspace
	- `cd ~/kobuki_ws`
	- `catkin_make`
	- `source devel/setup.bash`
- Check the ROS python package path
	- `python3 -c "import geometry_msgs.msg; print(geometry_msgs.__file__)"`
	- Result should shown as below (Something like this)
		- `geometry_msgs under /opt/ros/noetic/lib/python3/dist-packages`

Test the system launch just `python3 /home/SaPHaRI/kobui_ws/src/send_goal.py`. But TurtleBot can receive the command but does not move!

**Result:** FAIL!!!!


### October 18th, 2024

**Objective:** Continue from yesterday.

**Experiment:** Yesterday, I tried to test the system by launch the send_goal file first before run the rpi-rf_receive+launch file BUT I my TurtleBot does not move. Before going home I have talked to Rubber Ducks! So, today it solved. TurtleBot moved but the mistake is just I did not launch all necessary file.
- Testing send_goal file
	- Terminal 1: `roslaunch turtlebot_bringup minimal.launch`
	- Terminal 2: `roslaunch freenect_launch freenect.launch depth_registration:=true`
	- Terminal 3: `roslaunch rtabmap_ros rtabmap.launch rtabmap_args:="--delete_db_on_start" depth_topic:=/camera/depth_registered/image_raw rgb_topic:=/camera/rgb/image_raw camera_info_topic:=/camera/rgb/camera_info`
	- Terminal 4: `roslaunch turtlebot_navigation amcl_demo.launch map_file:=/home/SaPHaRI/kobuki_ws/src/map/4thmap.yaml`
	- Terminal 5: 
		- `python3 /home/SaPHaRI/kobuki_ws/src/send_goal.py`
		- or
		- `cd kobuki_ws/src`
		- `python3 send_goal.py` if cannot launch use this `sudo python3 send_goal.py`

Testing the rpi-rf_receive+launch file still fail and I get this error "No module named 'geometry_msgs'" So, I try to fix it by add this command line `source /opt/ros/noetic/setup.bash` into rpi-rf_receive+launch file.
```
#!/usr/bin/env python3

import argparse
import signal
import sys
import time
import logging
import os

from rpi_rf import RFDevice  

rfdevice = None

# Handle exit signal
def exithandler(signal, frame):
    rfdevice.cleanup()
    sys.exit(0) 

logging.basicConfig(level=logging.INFO, datefmt='%Y-%m-%d %H:%M:%S',
                    format='%(asctime)-15s - [%(levelname)s] %(module)s: %(message)s', ) 

# Argument parser for GPIO pin
parser = argparse.ArgumentParser(description='Receives a decimal code via a 433/315MHz GPIO device')
parser.add_argument('-g', dest='gpio', type=int, default=27,
                    help="GPIO pin (Default: 27)")
args = parser.parse_args()

# Setup exit handler
signal.signal(signal.SIGINT, exithandler)
rfdevice = RFDevice(args.gpio)
rfdevice.enable_rx()

timestamp = None
logging.info("Listening for codes on GPIO " + str(args.gpio))

# Main loop to listen for RF codes
while True:
    if rfdevice.rx_code_timestamp != timestamp:
        timestamp = rfdevice.rx_code_timestamp
        logging.info(str(rfdevice.rx_code) +
                     " [pulselength " + str(rfdevice.rx_pulselength) +
                     ", protocol " + str(rfdevice.rx_proto) + "]")
    time.sleep(0.01)

    # Trigger TurtleBot to move when an RF code is received
    if rfdevice.rx_code:
        os.system("source /opt/ros/noetic/setup.bash && python3 /home/SaPHaRI/kobuki_ws/src/send_goal.py")

rfdevice.cleanup()
```
After I launch this edited file I get this error "sh: 1: source: not found" So, this error occurs because the source command is specific to bash, but when we use `os.system()`, it uses sh by default, which **doesn't recognize source**.

To fix this, I need to tell the system to run the command using bash by change into this command line `os.system("bash -c 'source /opt/ros/noetic/setup.bash && source /home/SaPHaRI/kobuki_ws/devel/setup.bash && python3 /home/SaPHaRI/kobuki_ws/src/send_goal.py'")`. So, the whole code should look like below.
```
#!/usr/bin/env python3

import argparse
import signal
import sys
import time
import logging
import os  

from rpi_rf import RFDevice

rfdevice = None

# Handle exit signal
def exithandler(signal, frame):
    rfdevice.cleanup()
    sys.exit(0)

logging.basicConfig(level=logging.INFO, datefmt='%Y-%m-%d %H:%M:%S',
                    format='%(asctime)-15s - [%(levelname)s] %(module)s: %(message)s', )

# Argument parser for GPIO pin
parser = argparse.ArgumentParser(description='Receives a decimal code via a 433/315MHz GPIO device')
parser.add_argument('-g', dest='gpio', type=int, default=27,
                    help="GPIO pin (Default: 27)")
args = parser.parse_args()

# Setup exit handler
signal.signal(signal.SIGINT, exithandler)
rfdevice = RFDevice(args.gpio)
rfdevice.enable_rx()

timestamp = None
logging.info("Listening for codes on GPIO " + str(args.gpio))

# Main loop to listen for RF codes
while True:
    if rfdevice.rx_code_timestamp != timestamp:
        timestamp = rfdevice.rx_code_timestamp
        logging.info(str(rfdevice.rx_code) +
                     " [pulselength " + str(rfdevice.rx_pulselength) +
                     ", protocol " + str(rfdevice.rx_proto) + "]")
    time.sleep(0.01)

    # Trigger TurtleBot to move when an RF code is received
    if rfdevice.rx_code:
        os.system("bash -c 'source /opt/ros/noetic/setup.bash && source /home/SaPHaRI/kobuki_ws/devel/setup.bash && python3 /home/SaPHaRI/kobuki_ws/src/send_goal.py'")

rfdevice.cleanup()
```
It turns out **WORK!**

So, to test the system
- Testing the new rpi-rf_receive+launch file
	- Terminal 1:`roslaunch turtlebot_bringup minimal.launch`
	- Terminal 2: `roslaunch freenect_launch freenect.launch depth_registration:=true`
	- Terminal 3: `roslaunch rtabmap_ros rtabmap.launch rtabmap_args:="--delete_db_on_start" depth_topic:=/camera/depth_registered/image_raw rgb_topic:=/camera/rgb/image_raw camera_info_topic:=/camera/rgb/camera_info`
	- Terminal 4: `roslaunch turtlebot_navigation amcl_demo.launch map_file:=/home/SaPHaRI/kobuki_ws/src/map/4thmap.yaml`
	- Terminal 5: 
		- `sudo -python3 /home/SaPHaRI/kobuki_ws/src/rpi-rf_receive+launch.py`
		- or
		- `cd kobuki_ws/src`
		- `sudo python3 rpi-rf_receive+launch.py` 

**Result:** After press the doorbell button and RF receiver received the signal will trigger to enable the TurtleBot to move autonomously to specific position from our map.