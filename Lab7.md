## Lab 7: Kalman Filter

### Lab Objective

The goal for this lab is to incorporate a Kalman Filter to help the performance and speed of execution of the task we did in Lab 5. The Kalman Filter will be used to supplement the slowly sampled ToF values.

### Estimate Drag and Momentum

The Kalman Filter operates by first predicitng the robot's position using a state space model and then refining this forecast with actual sensor data. See the function below.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7KF1.png?raw=true)

To identify the A, B, and C matrices necessary for the Kalman Filter, I created a state space model specific to our robot. This model uses the distance between the robot and the wall, which represents our position, and the robot's velocity. See the details of this model below:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7KF2.png?raw=true)

To estimate drag (d) and momentum (m) I used the following two formulas:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7d.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7m.png?raw=true)

For the step response, the car was driven at a constant speed towards a wall to find steady-state velocity, d and m, with a PWM value of 200. The 90% rise time, representing the time it takes for the car to reach 90% of the steady state velocity was also determined. This approach generated the following data:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7fig1.png?raw=true)

The 90% step time required finding the time at which the car reaches 2200mm/s * 0.9 = 1980 mm/s. And the 90% rise time is around 1s. So we have,

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7math.png?raw=true)

And my matrices then become the following:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7abc.png?raw=true)

Note that the C matrix was initialized as C = [-1, 0], same as in lecture because we are measuring negative distance from the wall.

### Initialize Kalman Filter

To discretize the matrices I previously calculated, it was necessary to select a specific sampling rate for predicting filter values. I used a sampling rate of 20ms. The formula is as follows:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7initKF.png?raw=true)

So my matrices are now the following:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7ad.png?raw=true)

Then, I had to estimate the noise variables, sigma_u and sigma_z.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7math2.png?raw=true)

### Implement and test the Kalman Filter in Jupyter

Having calculated these values, I was able to implement the Kalman Filter using the function that was provided. I used the code that was given to us and added my created matrices. This implementation and the yielded result is shown below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7code.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/7est.png?raw=true)

The Kalman Filter effectively follows the pattern of the data and accurately predict the subsequent TOF measurement. A sampling rate of 100ms was used, aligning it with the sensor's sampling frequency.
