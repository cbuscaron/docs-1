# Tutorial: Interfacing Third Party Grippers with Fetch’s Arm

Fetch offers a modular mobile manipulator platform, capable of completing
a variety of tasks across complex enviorments. Mechanisms such as those present in grippers are the
 physical basis for how a robot interacts with the world.  Grippers offer different utility based
  on their governing mechanism allowing a robot to achieve a desired task.  The Fetch gripper is
   a parallel pincher which offers a wide range of utility, however, there may be instances
    where a different configuration is desirable.  The following tutorial outlines the 
  procedure for interfacing different robot grippers creating a procedural approach for 
  doing this using Fetch’s mechanical, electrical, and software interfaces. After a review
   of systems two specific examples will be presented.

## Mechanical Gripper Interface of Fetch:

The gripper flange has four M6x1.0 thread holes for attaching a gripper to the robot. 
The holes are spaced equally around a 50 mm diameter circle. The bolts should be tightened to 8 N·m,
 making sure there is 8 to 10 mm of threads engaged. For rotational alignment, an optional 6 mm 
 diameter hole is provided for an alignment pin. For accurate centering of mounted tools, the gripper 
 flange’s 6.5 mm high, 63 mm diameter boss can be factored into the tool design. A cable path is provided 
 for optional cable routing.

![alt text](https://github.com/arvinasokan/docs/blob/gripper_documentation/source/_static/Tool%20flange%20spec-01.png "tool flange")

## Electrical Gripper Interface of Fetch
Fetch’s Gripper Connector is 6 pin Hirose; Part Number DF11-6DS-2C. Carrying both power and ethernet on the same harness. The Proper mating connector housing is a Hirose DF11- 6DEP-2C.

## Software Gripper Interface of Fetch

For interfacing a gripper with the existing fetch software, there are five components which form the architecture. They are: the robot unified description format, the gripper controller, the gripper driver, config files, and Moveit setup.


For interfacing a gripper with the existing fetch software, there are five components which form the architecture. They are: the robot unified description format, the gripper controller, the gripper driver, config files, and Moveit setup.


**Robot Description Format:** The first step in interfacing the gripper would be to modify the fetch urdf and mount the third party gripper. This will be used in both simulating the grasps and also to check for collisions.  One aspect to note is that many grippers utilize mechanisms which form closed kinematic chains, which present challenges while implementing in URDF, because URDf parses and stores the model in a tree structure.  Many packages in ROS such as TF and Moveit! rely on the URDF.  To utilize these simplify the model into an open chain.

**Gripper Controller:** The fetch by default uses the parallel gripper controller from the robot controllers package which  is a robot control infrastructure, developed initially for Fetch and Freight, but designed to be robot-agnostic. Alternatively gripper specific controllers can be used, which can be provided by the gripper manufacturers. Gripper controllers are mainly used for simulation with gazebo.  

**Gripper Drivers:** Gripper drivers for different grippers could be obtained from the gripper manufacturer.  The gripper drivers communicate to the gripper at a lower level, to control the gripper. In order to use a gripper, the drivers must be installed.

**Config Files:** Specify the configuration of the systems used for the robot.  In order to interface a new gripper these can be augmented.

**Moveit!:** After initial setup after URDF augmentation, a new set of self collision matrices must be generated.  Moveit! setup assistant can be used to generate a new Fetch Moveit Config.

## Outline of Interfacing Grippers with Fetch

Later in this tutorial two examples will be presented, however, a general procedure outline can be identified.  To integrate various grippers with the Fetch platform.

1. Identify gripper communication protocol.
2. Test gripper first as a stand alone using computer.
3. Testing functionality.
4. Remove existing gripper.
5. Make a connector.  So it interfaces with the gripper and the fetch system.  
6. Augment the URDF to include new gripper.
   * Note: our robot controller is a parallel gripper robot controller.  If the gripper is not of this type of method is to simplify the URDF such as open chain with two prismatic fingers.
Set up robot controllers.
7. Generate move it config and collision matrix.

# Robotiq Adaptive Robot Gripper 2-Finger 85 Fetch Integration Documentation

![alt text](https://github.com/arvinasokan/docs/blob/gripper_documentation/source/_static/fetch_robotiq.JPG "fetch_robotiq")

The robotiq adaptive 85 gripper is a two finger gripper with a different mechanism than the one in the parallel gripper in fetch.

The robotiq adaptive 85 uses a five bar closed kinematic chain mechanism to perform its grasping.

Closed chain mechanisms like these are not supported by the URDF format itself as it parses the data in a tree structure and a closed loop would cause it to give out errors.This would cause significant technical difficulties in representing the robot in an accurate manner.

There are a couple of ways in which this could be dealt with,
One way is to modify the existing mechanism, by disconnecting some joints and making them fixed, thereby breaking the closed loop chain, however this will restrict the motion of the gripper in certain ways.

Another way to go about it is by coming up with a prismatic setup similar to existing gripper in fetch and cover all the possible collision geometry of the robotiq in the gripper base link.
 
A similar approach could be used to similar grippers with closed loop kinematic chains.

Next, the robot controllers have to be setup,
If the default robot controllers are used, then it is required that the two finger joints in the URDF be named as r_gripper_finger_joint and l_gripper_finger_joint as the default robot controllers look specifically for these joints. And the gripper pid’s have to be tuned according to the new gripper.

To control the actual gripper in the robot, the robotiq gripper package has to be downloaded
from https://github.com/ros-industrial/robotiq

By default the gripper is controlled by sending actionlib messages to the gripper drivers.
The robotiq package provides an action server to control the gripper.

Here the robotiq is controlled by using the modbus RTU protocol.

  ###### Tried and tested on

Operating System : Linux Ubuntu 14.04.3 LTS

Kernel Version : 3.16.0-45-generic

## Ros Driver Installation ## 

mkdir -p ~/robotiq/src

cd robotiq/src

catkin_init_workspace

cd ..

catkin_make

cd src

git clone https://github.com/ros-industrial/robotiq

Note:The EtherCAT soem library is also required to build the Robotiq package.

sudo apt-get install ros-indigo-soem

cd ..

catkin_make

source devel/setup.bash

Now the ROS drivers have been installed and ready to use

To launch the controller,

rosrun robotiq_c_model_control CModelRtuNode.py “/dev/ttyUSB0” 

This will launch the controller/driver for the gripper.

Note: USB port name may not be the same for all the computers and so to find the name of the usb port,

dmesg | grep tty

This will show the recent connected devices in the USB.


To perform a simple open close control manually,

Rosrun robotiq_c_model_control CModelSimpleController.py

this could be used to control the gripper using the keyboard.

To launch the actionserver provided by robotiq,

roslaunch robotiq_action_server robotiq_c_model_action_server.launch
Note: For the robotiq action server to work with the robotiq controller, the controller topics have to be remapped to /gripper/input and /gripper/output, this could be done by launching the controller with the following arguements

rosrun robotiq_c_model_control CModelRtuNode.py “/dev/ttyUSB0” CModelRobotInput:=/gripper/input CModelRobotOutput:=/gripper/output

# Barrett Hand BH-282 Fetch Integration Documentation
![alt text](https://github.com/arvinasokan/docs/blob/gripper_documentation/source/_static/fetch_barrett.JPG "fetch_barett")

##### Overview
The Barrett Hand is a three fingered hand from Barrett Technologies.
The hand uses CAN-BUS or RS-232 for communication, these connectors connect to a
control box which handles both the power supply and the communications of the hand.
For this particular setup a USB to CAN adapter is used (Preferably from PEAK)

###### Tried and tested on

Operating System : Linux Ubuntu 14.04.3 LTS

Kernel Version : 3.16.0-45-generic


#### Software Setup:

##### Note:
Before installing the ROS driver, the latest version of the Peak CAN drivers must be installed.
Please note that for recent kernel versions, only the recent version of the Peak CAN drivers will work properly.

###### Peak CAN drivers and python wrappers installation :

sudo apt-get install swig

###### Installing POPT
Go to http://www.linuxfromscratch.org/blfs/view/svn/general/popt.html

Download popt-1.16.tar.gz and extract it,

./configure --prefix=/usr --disable-static &&
make

sudo make install

###### Peak CAN Drivers:
Go to http://www.peak-system.com/ and download the latest version of the Peak CAN drivers.

once downloaded, extract the package,

make NET=NO_NETDEV_SUPPORT

sudo make install

###### Pcan Python:
Once the Peak CAN drivers are installed,

git clone https://github.com/RobotnikAutomation/pcan_python.git

make

sudo make install

Add /usr/lib to the PYTHONPATH in order to find the module.

Example: export PYTHONPATH=/usr/lib:$PYTHONPATH

Once all these drivers have been installed without any errors, the ROS drivers/Controller can be used.

###### ROS Driver Installation:

mkdir -p ~/bhand/src

cd bhand/src

catkin_init_workspace

cd ..

catkin_make

cd src

git clone https://github.com/RobotnikAutomation/barrett_hand

cd ..

catkin_make

source devel/setup.bash

Now the ROS drivers have been installed and ready to use

To launch the controller,

roslaunch bhand_controller bhand_controller.launch

The controller will show CAN Error if the CAN drivers have not been installed properly.

To control the barrett hand, through the RQT interface,

rosrun rqt_bhand rqt_bhand

##### List of Topic Published:
/bhand_node/command
/bhand_node/state
/bhand_node/tact_array
/joint_states


# Fetch Moveit Config

The fetch moveit config contains the srdf file which contains the self collision matrix.
A new set of fetch moveit config can be generated by using the Moveit setup assistant.

A version of moveit setup assistant for the Robotiq adaptive 85 and the Barrett hand can be found at ##some github link##

Moveit setup assistant can be downloaded from the following link

Note: There is an extra controllers.yaml file in the default fetch_moveit config. This file should be modified and included in the newly generated fetch_moveit_config

The gripper action server for robotiq uses GripperCommand messages to the gripper_controller action_ns.

However the barrett hand ros package does not use an action server.
And so the Barrett Hand can be controlled by sending "sensor_msgs/JointState" to the topic /bhand_node/command

![alt text](https://github.com/arvinasokan/docs/blob/gripper_documentation/source/_static/screenshot.png "fetch_barett_sim")

 
