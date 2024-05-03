## Localization (Real)

### Lab Objective

Last lab I demonstrated the effectiveness of the Bayes filter in a simulation. In this lab I will perform localization with the Bayes filter on the actual robot. Only the update step of the Bayes filter will be used as the motion is very noisy and therefore the  prediction step will not be helpful.

### Bayes Filter Simulation

Three files where provided to us to help us accomplishing this lab: [lab11_sim.ipynb](https://github.com/CEI-lab/FastRobots-lab11/blob/main/lab11_sim.ipynb), [lab11_real.ipynb](https://github.com/CEI-lab/FastRobots-lab11/blob/main/lab11_real.ipynb) and [localization_extras](https://github.com/CEI-lab/FastRobots-lab11/blob/main/localization_extras.py).
  - The lab11_sim.ipynb file is a Jupyter notebook that provides skeleton code to integrate the real robot with the localization code.
  - The lab11_real.ipynb file is a Jupyter notebook that demonstrates the Bayes filter implementation on the virtual robot.
  - localization_extras.py provides a fully-functional localization module that works on the virtual robot.

Our first task was to test localization in simulation again, but this time with the code given to us instead of our own implementation from lab 10. I did this by running the simulation notebook. The result is shown below. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/10sim.png?raw=true" width="500" height="350">

### Bayes Filter (Real)


### Results

My results indicate that my robot has a very high confidence in its update position as the probability that the robot is at its belief is nearly 1 for all four locations. The predicted locations from the update step also matches well with the actual ground truth of the robot. All of the localization results accurately determined the x coordinate. However, some of the locations were of by one foot in the y direction. The blue point represents the belief of the robot and the red point represents the ground truth. Also note that the robot think it is offset by around 10 degrees, which is accurate as my robot still rotates around 10 degrees too much. My intention is to fix this before lab 12 in order to succesfully navigate through the map. 

Location: (-3, -2)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot1.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data1.png?raw=true" width="500" height="200">

Location: (5, -3)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot2.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data2.png?raw=true" width="500" height="200">

Location: (5, 3)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot3.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data3.png?raw=true" width="500" height="200">

Location: (0, 3)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot4.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data4.png?raw=true" width="500" height="200">

Location: (0, 0)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11orig.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11dataori.png?raw=true" width="500" height="200">
