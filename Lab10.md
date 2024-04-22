### Localization (SIM)

### Lab Objective
The objective of this lab is to implement grid localization using Bayes filter in a simulation environment. This is important as robot localization is the process of determining where a 
mobile robot is located with respect to its environment.

### Grid Localization
We convert the continuous state space into a discrete 3D grid space, segmented along the x, y, and theta axes. This discretization inevitably sacrifices some accuracy because it prevents differentiation between robot states within the same grid cell. However, it enables the computation of the belief across a finite set of states within a practical timeframe.

Each grid cell measures 0.3048 meters along the x and y axes, and 20 degrees along the theta axis. The grid is composed of (12, 9, 18) cells along these respective axes. Every cell holds the probability of the robot being located at that particular cell. The collective set of these probabilities, which sum to 1, represents the robot's belief about its location.

The Bayes filter algorithm is employed to update the probability of the robot’s presence in each grid cell as more information becomes available. The cell—or cells—with the highest probability indicate the robot's most likely position. Tracking the highest probability cells over time outlines the trajectory of the robot.

### Compute Control

### Odometry Motion 

### Prediciton Step

### Sensor Model

This function is important for the update step because it computes the probability of each sensor measurement using a Gaussian model.

### Update Step

### Simulation

