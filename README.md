**Authors:** Awais Tahir, Abdul Rauf, Naveed Manzoor Afridi
# Software-Architecture-for-Mobile-Robot-TIAGO
The work has been performed to improve the fuctionalities of mobile robot (TIAGO) by integrating complex packages in order to detect and avoid risen dynamic obstacle which has not provided by the given map which helps local and global planner to make correct path planning. This helps to avoid collision of the mobile robot (TIAGO). 
## Problem
  Mobile Robot like TIAGO build the map or use already given map which is provided using the sensor(Lidar) that has placed at the bottom in order to navigate in the environment. Basically, It usually build the map by using the information from the LIDAR sensor which is placed at the bottom of the TIAGO and can't detect and map the high risen obstacles like TABLE.
## Solution 
  In order to navigate in the environment by corrected map, the integration of the RGB-D(Kinect) sensor with the TIAGO has been done to use depth information to correct the map.
For that, I have used STVL layer of the Navigation2 package which dynamically updating the local ad global map by using depth information of the kinect sensor. This helps navigating in the environment safely without colliding with the dynamic or static obstacles (like Table) which were not detected by the map which built by the Lidar sensor connected at the bottom of the robot.

## Quick Start
- Install [ROS2 Galactic](https://docs.ros.org/en/galactic/Installation/Alternatives/Ubuntu-Development-Setup.html)
- Install the STVL package and the webots package:
```
$ sudo apt-get install ros-galactic-spatio-temporal-voxel-layer
```
- Install webots package using [Webots](https://github.com/cyberbotics/webots_ros2/wiki/Build-and-Install)
- Clone this repository in a new workspace and build it:
```
$ mkdir my_ros2_ws && cd my_ros2_ws && mkdir src && cd src
$ git clone https://github.com/awaistahir29/Software-Architecture-for-Mobile-Robot-TIAGO-using-ROS2.git
$ cd .. && colcon build
$ source install local/setup.bash
```
- For Navigation package checkout to the galactic branch
```
$ git checkout nav2
$ cd .. && colcon build
$ source install local/setup.bash
```
- To launch the Tiago with Navigation package with whole implemetation.
```
$ ros2 launch webots_ros2_tiago robot_launch.py
```
# Spatio-Temporal Voxel Layer (STVL)
## Introduction
The [Spatio-Temporal Voxel Layer](https://github.com/SteveMacenski/spatio_temporal_voxel_layer/tree/galactic) is used to update  local and global costmap, using a simulated TIAGO robot. We manually atached RGBD Kinect sensor at the head of the TIAGO in the simulation then we needed to perform the transformation with respect to the base_link which has been performed.
Additionally, lab experiments will be conducted on the **TIAGo** robot from **PAL Robotics** to confirm our simulations.

## Procedure
### 1. Installation
Firstly, we installed the [Galactic Geochelone](https://docs.ros.org/en/galactic/index.html) distribution of **ROS2**.

On top of that, we installed the ROS2 [Navigation 2 Framework v2.1.0.0](https://navigation.ros.org/) and the [STVL](https://github.com/SteveMacenski/spatio_temporal_voxel_layer/tree/galactic) plugin through **apt**.

More specifically, we used the **nav2_bringup** simulation, and we added the STVL layers as plugins to the **nav2_params.yaml**.
For more details follow the tutorial [(STVL) Using an External Costmap Plugin](https://navigation.ros.org/tutorials/docs/navigation2_with_stvl.html).

The simulation was run on [WeBots](https://cyberbotics.com/) and monitored on [RViz2](https://index.ros.org/p/rviz2/).

### 2. Environment Building
To test the STVL plugin, we needed test environments through which to run the simulated robot. For this purpose, Webots is used to build the environments and SLAM was used to map them. Additionally, the map is provided by the package simulation of **TIAGo Iron**. So, we can use any of the options as we have given the map we just need to give position and goal using rviz.
### 3. Kinect Integration

#### Transformation Node
To attach the kinect sensor to the robot the node has been made which basically performs the transformation of the kinect with respect ro the base_link.
The Kinect sensor is added manually in the simulation but the problem was the time at which it was publishing the data was not syncrinozed with the robot. For that we simply turned off the the use_sim_time parameter. 
### 4. Mapping with SLAM
We chose the SLAM toolbox to map our environments as it was the quickest option available to us. The toolbox was installed through apt, following the nav2 documentation ["Navigating While Mapping"](https://navigation.ros.org/tutorials/docs/navigation2_with_slam.html).  
<br>
With **TIAGo Iron**, running SLAM is very simple: the TIAGO simulation is called through its launch file, as per usual and we just need to set *True* the parameters that we want to use. Either we can use already given map or we can build map using slam toolbox.
Then, using [teleop_twist_keyboard](http://wiki.ros.org/teleop_twist_keyboard), we explored the entire environment whilst mapping it, and then we saved the map through the map_saver_cli node, as detailed in the nav2 documentation ["Map Saver / Saver"](https://navigation.ros.org/configuration/packages/configuring-map-server.html).
<br>
### Navigation Package
In navigation package we modified parameter server to add the STVL Layyer which subscribe the pointcloud2 information of the kinect sensor and use this information to correct the local and global costmap.
### Conclusion
To sum it all up, the whole architecture is made correct local and global costmaps that allow the robot to navigate correctly in the environment in presense of obstaces like Table, or any risen obstacle which was not mapped by the Lidar sensor.

![diagram](https://github.com/awaistahir29/Software-Architecture-for-Mobile-Robot-TIAGO-using-ROS2/blob/main/UML.png)

### Encountered Issues
We have faced a lot of challanges while deploying all these things. 
Initially, We have used complex webot package **TIAGO** and then configured its launch file to use multiple functionalities integrated with the robot simulation. 
Thereafter, navigation2 package has been modified in order to use depth information to project this dynamic depth information onto the map so that local and global planner can plan the correct trajectory to reach the goal.
