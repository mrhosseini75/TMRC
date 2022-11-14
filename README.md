# Topological Map Robot Control

_First assignment of experimental robot laboratoy._

Intriduction
==============
Main idea of building a robot that changes position from one location to another and during movement consumes the battery and must return to the initial location to recharge. The robot has to visit all locations, so you need to record the time when it passes away from one location.
<p align="center">
<img width="400" src="https://user-images.githubusercontent.com/80394968/200863752-0da59da4-dac3-4031-a93c-6fab05ed9516.jpg" alt="400">
</p>
<p align="center">
The map considered for this assignment
</p>

Tools
=======

To solve the following problem I used various tools, like:<br/>
1. *Protègè*: it was used to create the topological map and define the logical part.
2. *aRMOR*: it was used to load topological map and `manipulation` and `query` classes.
3. *Arch_skeleton*: it was used to build a plan between the points defined on the map.
4. *SMACH viewer*: to view robot status

Documentation
==============

For more information on nodes and sofar then refer to [Documentation](https://mrhosseini75.github.io/tmrc/)

Software architecture
========================

<p align="center">
<img width="800" src="https://user-images.githubusercontent.com/80394968/201729118-e406b565-b455-427e-ae00-a60402febd81.jpg" alt="800">
</p>
<p align="center">
UML graph
</p>
  
**Robot state node**

Consisting of a node that allows the client to use the ``GET_POSE`` and ``SET_POSE`` service and communicate with other nodes.

**Motion Controller**

It is built by two controller_client and controller nodes to move the robot between different locations.

**Motion Planner**

It is built by two planner_client and planner nodes to generate a map of points to switch from one room to another (points are defined inside the map / see map section).

**aRMOR**<br/>
Armor package is built from 6 nodes which allows me to do:
  - Manipulation in ontology map
  - Query from ontology map
  - Exception on ontology map
  - Architecture name mapper for each node via name_tag


**Finite state machine**

Inside this node, most of the functions have been defined and it allows me to view the robot status via **GUI SMACH viewer**



rqt graph
===========

<p align="center">
<img width="800" src="https://user-images.githubusercontent.com/80394968/201729229-b924a3cf-628f-4e8b-bee4-66fa636f2aea.png" alt="800">
</p>
</p>
<p align="center">
rqt graph 
</p>

`/finite_state_machine` node starts listing the rooms that have not been visited in a long time, consider the first room in the list as the `/target_point` post message to `/motion/palnner`. planner builds a `/path` and publishes it to `/motion/controller`. Controller will bring robot to destination and `/finit_state_machine` node will update `/smach_viewer`.<br/>
The messages communicated between the nodes are done through the `GET_POSE` and `SET_POSE` service defined in `/robot_state` node.


Usage
========

- You have to creat your own repository, to do that
  ```
  $ mkdir ros_ws /src
  $ cd src
  ```
- Frok package from my repository 
  ```
  $ git clone https://github.com/mrhosseini75/tmrc.git
  $ cd ..
  $ catkin_make
  ```
- You need to install SMACH viewer
  ```
  $ sudo apt-get update
  $ sudo apt-get install ros-noetic-smach ros-noetic-smach-ros ros-noetic-executive-smach ros-noetic-smach-viewer
  ```
- Now you can launch program
  ```
  $ roslaunch tmrc topological_map_robot_control
  ```
 !!!WARNING!!! <br/>
 In scripts, the system path has been specified in order to use the carried package. As following way:
  ```  
  import sys
  sys.path.append('/root/ros_ws/src/tmrc')
  ```
 if you clone repository in diffrenet path, to have to update this line of code.

Video
========

https://user-images.githubusercontent.com/80394968/200888022-bb64502d-19a7-43dd-a412-becc28484e7a.mp4

Hypothesis and environment
===========================
<p align="center">
<img width="400" src="https://user-images.githubusercontent.com/80394968/200889543-02b8153c-7d12-42d4-b3ea-d5b5d9584c4e.png" alt="400">
</p>
</p>
<p align="center">
Map 
</p>

**System’s features**

- Final environment was considered in this way. A map with size _10x10_ and there are 7 points indicating different locations. Planner uses these points to create `/path`.
- Each time robot passes by a room the time is recorded in the `visitedAt` method and it updates the list of room to visit. 
- If more than 7 seconds have passed since the last time the robot was there, the room was on the `URGENT` list.

**System’s limitations**

- The robot only charges in room E, and can only charge up to 20 percent.
- Every time the robot moves the battery is consumed and if it remains stationary in a room the battery is not consumed.
- If the robot battery reaches 7 percent, system indicates room E as the first urgent case to visit.


**Possible technical Improvements**

- It is possible to add a `world` in which the robot's behavior can be better visualized.
- Walls have not been considered so you can use `world` and `/laser_scan` topic to create an `obstacle_avoidness` node.
- No control over robot speed, you can use `/cmd_vel` topic to generate velocity.


Autor and contact
==================

- Mohammad Reza Haji Hosseini
- email: <mrhhosseini75@gmail.com>
