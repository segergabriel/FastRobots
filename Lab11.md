## Localization (Real)

### Lab Objective


Last lab I demonstrated the effectiveness of the Bayes filter in a simulation. In this lab I will perform localization with the Bayes filter on the actual robot. Only the update step of the Bayes filter will be used as the motion is very noisy and the prediction step will therefore not be helpful.

### Bayes Filter (Sim)


### Bayes Filter (Real)


### Results

My results indicate that my robot has a very high confidence in its update position as the probability that the robot is at its belief is nearly 1 for all four locations. The predicted locations from the update step also matches well with the actual ground truth of the robot. All of the localization results accurately determined the x coordinate. However, some of the locations were of by one foot in the y direction. The blue point represents the belief of the robot and the red point represents the ground truth. Also note that the robot think it is offset by around 10 degrees, which is accurate as my robot still rotates around 10 degrees too much. My intention is to fix this before lab 12. 

(-3, -2)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot1.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data1.png?raw=true" width="500" height="200">

(5, -3)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot2.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data2.png?raw=true" width="500" height="200">

(5, 3)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot3.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data3.png?raw=true" width="500" height="200">

(0, 3)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot4.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data4.png?raw=true" width="500" height="200">

(0, 0)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11orig.png?raw=true" width="500" height="350">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/11dataori.png?raw=true" width="500" height="200">
