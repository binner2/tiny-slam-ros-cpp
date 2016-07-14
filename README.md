Copyright (c) 2016 JetBrains Research, Mobile Robot Algorithms Laboratory

Authors: Artur Huletski, Dmitriy Kartashov
TinySLAM C++ implementation for ROS

# Description

This package provides ROS implentation of the tinySLAM (https://openslam.org/tinyslam.html) that is one of the most simpliest and lightweight SLAM methods. Current implementation is compatible with ROS Jade and adds some improvements to the original method:
the alternative model of a grid map cell that keeps track of the average of all stored effective values (probability of being occupied with scan’s quality taken into account);
an option to compute probability of a cell being occupied based on how the laser beam splits the cell (the original implementation uses the constant value).

# Hardware Requirements

Current implementation supposes that the robot provides odometry data and laser scan measurements from the horizontally mounted fixed laser scanner. These data should be provided through the ROS topics (see Subscribed topics).

# Example

Use the provided launch-file to run and configure tinySLAM node:
roslaunch tinyslam_ros tinyslam_run.launch path:=[path to dataset]
Dataset should be in BAG format (http://wiki.ros.org/rosbag). You also may comment out rosbag node in the launch file if you use the real-time data or replace it with some other dataset player.

# Nodes 

## tiny_slam

The tiny_slam node takes in sensor_msgs/LaserScan messages and odometry data from /tf topic and builds a map (nav_msgs/OccupancyGrid) that can be retrieved via the ROS topic /map.

### Subscribed Topics

* tf ([tf/tfMessage](http://docs.ros.org/api/tf/html/msg/tfMessage.html)) Transforms necessary to relate frames for laser, base, and odometry (see below)
* laser_scan ([sensor_msgs/LaserScan](http://docs.ros.org/api/sensor_msgs/html/msg/LaserScan.html)) Laser scans to create the map from

### Published Topics

* map ([nav_msgs/OccupancyGrid](http://docs.ros.org/api/nav_msgs/html/msg/OccupancyGrid.html)) Get the map data from this topic (updated periodically)

### Parameters

* **~cell_type** (string, default: “avg”)
Accepted values: “avg” -- modified cell model; “base” -- original cell model
* **~occupancy_estimator** (string, default: "const")
Accepted values: “const” -- original occupancy estimator; “area” -- dynamic occupancy estimator
* **~skip_exceeding_lsr_vals** (boolean, default: false)
Determines how to handle out of range laser measurements: true -- ignore such measurements, false -- process as empty cells.
* **~base_occupied_prob** (float, default: 0.95)
The initial probability of cell to be occupied (i.e. the probability of laser scan data to be valid)
* **~base_empty_prob** (float, default: 0.01)
The initial probability of cell to be empty (i.e. the probability of out of range laser scan data to be valid)

### Required tf Transforms
*base_link → odom_combined*: usually provided by the odometry system (e.g., the driver for the mobile base)

### Provided tf Transforms
*map → robot_pose*: the current estimate of the robot's pose within the map frame 

