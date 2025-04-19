# Static Obstacle Avoidance with Frenet Coordinate and Spline Planner

This repository implements a static obstacle avoidance planner using the Frenet coordinate system and spline interpolation. The project is compatible with the F1TENTH simulator and real-vehicle execution.

## Overview

### frenet_utils.py

This module provides conversion functions between Cartesian coordinates and Frenet coordinates.  
It builds a smooth centerline using cubic splines and enables transformation between (x, y) and (s, d) coordinates.

This step is **prerequisite** for using `static_obs_spline_planner.py`, as it allows the planner to reason about obstacle positions and trajectories relative to the reference path.

### static_obs_spline_planner.py

This ROS node:
- Subscribes to global path waypoints and obstacle data.
- Computes avoidance trajectories by generating spline paths around obstacles in Frenet space.
- Publishes evasion waypoints and RViz markers for visualization.

To function correctly, the following topics must be available:
- `/global_path/optimal_trajectory_wpnt`: Global waypoints in `WpntArray` format.
- `/perception/detection/raw_obstacles`: Obstacle list from the perception module.
- `/lane`: Lane information for visualization and spline guidance (see below).

## Lane Visualization

The topic `/lane` is published using the script `lane_visualize.py`.  
This script renders the track lane from predefined waypoints, allowing planners and perception modules to align with lane geometry.

```bash
rosrun my_pkg lane_visualize.py
```

## TF Broadcasting

The `tf_broadcaster.launch` file defines the static transformations between vehicle sensors.

You must edit this file to reflect the real vehicle's physical specifications:

- `base_link → imu`
- `base_link → lidar`
- `base_link → odom`

This is essential for correct data alignment.

```bash
roslaunch my_pkg tf_broadcaster.launch
```

## Installation

```bash
# Create workspace and move into it
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src

# Clone this repository
git clone https://github.com/patrick-kook/f1tenth_pkg

# Build the workspace
cd ..
catkin_make
```

## Running the Planner

### 1. Visualize the lane

```bash
rosrun my_pkg lane_visualize.py
```

### 2. Run the static obstacle spline planner

```bash
rosrun my_pkg static_obs_spline_planner.py
```

### 3. Run the tf broadcaster

```bash
roslaunch my_pkg tf_broadcaster.launch
```

## Simulation Support

This package can be tested in the F1TENTH simulation environment.  
Please refer to the following simulator:

https://github.com/f1tenth/f1tenth_simulator
