# HTC Vive teleop stuff

## Install

First, follow the [INSTALL](INSTALL.md) instructions to get Ubuntu 16.04 and SteamVR (beta) on your machine.

Then get my fork of pyopenvr and install it 
(it contains a workaround to be able to read the controller's
state correctly, only necessary until [PR #42](https://github.com/cmbruns/pyopenvr/pull/42) is merged).
```bash
git clone https://github.com/awesomebytes/pyopenvr
cd pyopenvr
sudo python setup.py install
```

To run my node you'll need to have installed basic ROS TF stuff:

```bash
# In case you are missing it
sudo apt-get install ros-kinetic-tf ros-kinetic-tf2*

```

Then check out and fake-compile (it's all Python) this repository in your catkin workspace:
```bash
mkdir -p vive_ws/src
cd vive_ws/src
git clone https://github.com/uts-magic-lab/htc_vive_teleop_stuff
cd ..
catkin_make
source devel/setup.bash
```

## Run node
The node you want to run is [scripts/vive_tf_and_joy.py](scripts/vive_tf_and_joy.py) which is nicely prepared in a launchfile for you.
```bash
roslaunch htc_vive_teleop_stuff htc_vive_tf_and_joy.launch
```

You'll see plenty of output (that's the OpenVR initializing) and you may need to touch your controllers to get it started. Then you'll see the output: `Running!`

You'll find the topics:
```
Topic       Type                Rate
/tf         tf2_msgs/TFMessage  250Hz
/vive_left  sensor_msgs/Joy     On Event
/vive_right sensor_msgs/Joy     On Event
```

The TF tree looks like (there is only one lighthouse because I only had one plugged in):
![tf_tree.png](tf_tree.png)

The `sensor_msgs/Joy` topics are as:
```
header: 
  seq: 541
  stamp: 
    secs: 1508649347
    nsecs: 147578954
# which controller
  frame_id: left_controller
# Trigger, Trackpad X, Trackpad Y
axes: [0.0, 0.0, 0.0]
# Trigger, Trackpad touched, Trackpad pressed, Menu, Gripper
buttons: [0, 0, 0, 0, 0]
```

![controllers.png](controllers.png)

This image pertains to HTC from [this user guide](http://www.htc.com/managed-assets/shared/desktop/vive/Vive_PRE_User_Guide.pdf).

Note that the systme button is unreadable. Pressing it will prevent the other keys from being read. 
Press it again to resume data transmission.

You can find another launch file called `vive_tf_joy_and_ps.launch` which in addition provides two topics:
```
Topic                               Type                        Rate
/left_controller_as_posestamped     geometry_msgs/PoseStamped   30Hz
/right_controller_as_posestamped    geometry_msgs/PoseStamped   30Hz
```

Which are the poses of the controllers in reference to the headset, useful for teleoperation purposes.
It uses [scripts/frame_as_posestamped.py](scripts/frame_as_posestamped.py) which is a commandline tool:
```bash
Usage:
./frame_as_posestamped.py frame_to_posestamped reference_frame [rate]
```
That takes a TF frame that you want published as a `geometry_msgs/PoseStamped` in reference to another frame, with
whatever rate you want.

## To run PR2 Teleop demo
*TODO*
```
sudo apt-get install libnlopt-dev
sudo apt-get install ros-kinetic-moveit-ros
sudo apt-get install swig

git clone https://bitbucket.org/awesomebytes/trac_ik
cd trac_ik
git checkout python_wrapper_swig_based

cd ../..
catkin_make
```



