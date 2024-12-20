## <u>12/2/24</u>
It's not Sunday, but here we are.
#### Modularity
On the desktop (master), uploading the `kobuki_ws` to a repo at [https://github.com/SaPHaRI-Lab/Turtlebot2-kobuki_ws](https://github.com/SaPHaRI-Lab/Turtlebot2-kobuki_ws).

While doing this initially, I accidentally deleted everything and had to recreate the folder. Sorry Chester.

There exists repos in this repo, so I went into every folder and ran `rm -rf .git` to remove repo data. To work with Git, these are my steps:
- Ensure Git's installed already. If it isn't, run `sudo apt-get install git-all`
- To make a new repo
- Navigate to `kobuki_ws`, then run:
	- `git init`
	- `git remote add origin https://github.com/SaPHaRI-Lab/Turtlebot2-kobuki_ws`
	- `git add .`
	- `echo "# Turtlebot 2 blah blah blah > README.md`
	- If you need a `.gitignore`, use:
		- `vim .gitignore` (then edit)
		- `git rm --cached .` or `git reset`
	- `git commit -m "init"`
- Now the slightly annoying part: **authentication**
	- I'm using [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager) (GCM) to simplify the auth process.
	- Test if you have .NET by running `dotnet` in the terminal. If not found, I installed [.NET here](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu-install?tabs=dotnet9&pivots=os-linux-ubuntu-2004) since I dislike `snap`
	- Then follow the [.NET Linux section](https://github.com/git-ecosystem/git-credential-manager/blob/release/docs/install.md#net-tool)–a restart is required!
	- `git-credential-manager configure`
	- Since we're on Linux with a GUI, we'll use secretservice. Run `export GCM_CREDENTIAL_STORE=secretservice`
- Config auth
	- `git config --global user.email <email>`
	- `git config --global user.name <name>`
	- Check with `cat ~/.gitignore`, if need to reset delete the file with `rm`
- Finally, `git push --set-upstream origin master`

### Communication (Day 2)
Switching from HTTP to [MQTT](https://randomnerdtutorials.com/what-is-mqtt-and-how-it-works/) (Message Querying Telemetry Transport).

Was originally going to use the Jetson, but for compatibility and convenience we'll be using the Desktop that's also the master for the Turtlebots.

**Goal:** We need the ESP32 to send messages to the Desktop (our broker) over WiFi via MQTT, then a response is sent both to the ESP32 and a corresponding Turtlebot.

Modified `main.cpp` for the ESP32 again, with comments explaining key parts of the code. 

For the Desktop, we need to use **Mosquitto** for a broker. Install with `sudo apt install mosquitto mosquitto-clients`. It should run automatically, but it can be manually started with `sudo systemctl start mosquitto` and `sudo systemctl enable mosquitto`.

For the Pis, I've created a fork of `minimal.launch` to `minimal_ext.launch` that incorporates the script `turtlebot_mqtt`. It'll be using the **Paho MQTT** client. It's available in [this directory of the repo](https://github.com/SaPHaRI-Lab/Turtlebot2-kobuki_ws/tree/master/src/turtlebot/turtlebot_bringup/scripts) created just earlier. This means we'll need to run `minimal_ext.launch` instead of `minimal.launch` for the Pis moving forward.

**TODO:** Haven't tested for any device. `main.cpp` should work and I'll test it using the mosquitto client on the Desktop. Then, I'll test it on a Turtlebot with a Pi to send a command to move the Turtlebot forward.
## <u>12/3/24</u>
### Communication (Day 3)
for custom, deployed movement of independant bot swarms...use `os.environ[<env var>]` and go by IP

![[Pasted image 20241203101559.png]]

#### ESP32 
Updated `main.cpp` to subscribe to topic `esp32/motor` and publish to topic `esp32/door`. 

Then, on the Desktop (broker) I subscribed using `mosquitto_sub -h localhost -t esp32/door`. There is a constant stream of beeps every 2 seconds. Success! I tested publishing with `mosquitto_pub -h localhost -t esp32/motor -m "hello"`, and monitored the ESP32's Serial output. Messages are being received. Success!

Cooked `.master.py` to be always run on the broker (Desktop), linked [here](https://github.com/SaPHaRI-Lab/Turtlebot2-kobuki_ws/blob/master/.master.py).
#### Turtlebots
Starting on the Pi 6, added `export PI_ID=<2-7>` to `.bashrc`. This will come in handy for Pi identification.

Updated the GitHub `kobuki_ws` with extra files that were on the Pi 6. Used a PAT to sign in instead of using Git Credential Manager.

Updated the file in `src/turtlebot/turtlebot_bringup/scripts`. The file will initialize the node `pi{ID}_roscomms` , and the other custom movement file will do the same...UNLESS this file will just do everything.

**TODO:** Look into how complex adding the custom code-based autonomous movement is, then decide if we need a separate file or we can integrate the entire movement in this same file. However, it is pretty standard to use `rospy` with `Sub` and `Pub`, so most likely we'll be using a separate file to control movement...

## <u>12/6/24</u>

#### Doorbell Receiver
RF Receiver (RXB6) arrived, [should work](https://stef-aap.github.io/RFLink-ESP/433%20MHz%20Transceivers.html). Using this wiring diagram:
![[Pasted image 20241207012231.png]]

I only have one ground port on the ESP32. So, the ground from the servo is the 2nd VCC into the RF Receiver...

Tried remapping ports, not using the `RCSwitch` library, and rewiring the RX6B. None worked, so I'm suspecting an issue with a frequency mismatch.

## <u>12/7/24</u>

### Communication (Day 4)
#### Doorbell Receiver (Day 2)
Tested doorbell signal with FlipperZero for a sanity check, and yes it is 433.92 MHz.

Ok, I was a complete idiot and thought that there was a second 3.3V pin on the ESP32. Turns out, there wasn't. Holy crap.

Used a breadboard and wired to pins 5, 7, 8 in the diagram above. Updated `main.cpp`; we should be done now!

### Turtlebot Behavior
#### Sound Effect
Install pip package `playsound`, then you get this code:
```python
from playsound import playsound
playsound('path/to/audio')
```
Yeah it's easy.
#### Communicating w/ Master
On Pi 5, updated to install `paho-mqtt` and `playsound` packages, and set `PI_ID=5`.

Launch:
- `minimal_ext.launch`
- `rplidar_a1tf.launch` <-- check which USB port the LiDAR is plugged into (use `ls -l /dev | grep ttyUSB`, config in the launch file
- `amcl_demo.launch map_file:=/...`

Getting laser scan errors though...Turtlebot moved the first time though.
## <u>12/8/24</u>
I broke the motor. Anyways, the Turtlebot moves every blue moon.

Many, many issues with the goal planning. It's something with the `move_base` server.


