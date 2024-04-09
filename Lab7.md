### Kalman

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

A constant step response experiment towards a wall was used to find d and m, with a PWM value of 200. This approach generated the following data:

pic of graph

Looking at the graph we can see that the speed reaches steady state at a derivative of about xm/s. And the 90% rise time was about 1s. So we have,

$\d = u/sqrt{3x-1}+(1+x)^2$

$\m = sqrt{3x-1}+(1+x)^2$

$`\sqrt{3x-1}+(1+x)^2`$


$\m = [sqrt{3x-1}+(1+x)^2 0
1 0]$
calculations for d and m
and matrices


**The Cauchy-Schwarz Inequality**
$$\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)$$

### Initialize Kalman Filter

To discretize the matrices I previously calculated, it was necessary to select a specific sampling rate for predicting filter values. I used a sampling rate of 20ms. The formula is as follows:

formula and calc

Then, I had to estimate the noise variables, sigma_u and sigma_z.

vars

calculations


### Implement and test your Kalman Filter in Jupyter

Having calculated these values, I was able to implement the Kalman Filter using the function that was provided. I used the code that was given to us and my created matrix is shown below. 

pic of code

pic of matrix

grpah of KL

The Kalman Filter effectively follows the pattern of the data and accurately predict the subsequent TOF measurement. A sampling rate of 100ms was used, aligning it with the sensor's sampling frequency.
