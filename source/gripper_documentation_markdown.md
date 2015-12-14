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

# Example One: Interfacing Robotiq 85


