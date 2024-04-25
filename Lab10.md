## Localization (SIM)

### Lab Objective
The objective of this lab is to implement grid localization using Bayes filter in a simulation environment. This is important as robot localization is the process of determining where a 
mobile robot is located with respect to its environment.

### Algorithm

Every iteration of the Bayes filter has two steps, the algorithm I will be using is shown below.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10algo.png?raw=true" width="500" height="300">

Note that the prediction step increases uncertainty in the belief while the update step reduces uncertainty.

### Grid Localization

We convert the continuous state space into a discrete 3D grid space, segmented along the x, y, and theta axes. This discretization sacrifices some accuracy because it prevents differentiation between robot states within the same grid cell. However, it enables us to compute the belief over a finite set of states in reasonable time.

Each grid cell measures 0.3048 meters along the x and y axes, and 20 degrees along the theta axis. The grid is composed of (12, 9, 18) cells along these respective axes. Every cell holds the probability of the robot being located at that particular cell. The collective set of these probabilities, which sum to 1, represents the robot's belief about its location. The cell(s) with the highest probability indicate the robot's most likely position. Tracking the highest probability cells over time outlines the trajectory of the robot.

### Compute Control

To determine the probability of the robot's new pose, it is necessary to compare the robot's actual movement against the input. This involves calculating the first rotation, translation, and second rotation by comparing the current pose to the previous one. This resulted in the code below and returns an array containing the control values:

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10compute.png?raw=true" width="500" height="300">

### Odometry Motion 

After determining the robot's actual movement, the likelihood of this movement was calculates using a Gaussian distribution. See the following implementation:

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10odom.png?raw=true" width="500" height="300">

### Prediciton Step

The calculated probabilities were then used to predict the robot's position. I have six nested loops, reflecting the algorithm's complexity in evaluating the probability that the robot moved to its current pose from each possible grid cell. The algorithm skips adding probabilities for any grid cell with a previous belief approximated as 0.0001 or lower to optimize processing. For all other cells, the code iteratively computes the likelihood of the robot's presence in each cell using the odometry probability model.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10pred.png?raw=true" width="500" height="300">

### Sensor Model

This function is important for the update step because it computes the probability of each sensor measurement using a Gaussian model and reduces the uncertainty in the robot's position. See below.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10sensor.png?raw=true" width="500" height="300">

### Update Step

In the update step, the robot computes the belief for each grid cell by multiplying the predicted belief by the probability derived from the sensor measurements. This product forms the updated belief for each grid cell. These beliefs are normalized to ensure that they together form a valid probability distribution. See my implementation below. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10update.png?raw=true" width="500" height="300">

### Simulation

The Bayes Filter's was tested using the given simulator, where odometry data is shown in red, the ground truth in green, and the robot's estimated position in blue. The ground truth and the robot's belief was consistent over multiple runs. This indicates that the Bayes Filter is pretty effectively localizing the robot within the simulated environment. Below is one photo of a run followed by a video of my final simulation. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10sim.png?raw=true" width="560" height="320">

<iframe width="560" height="315" src="https://www.youtube.com/embed/XkgqxqXsHP8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
