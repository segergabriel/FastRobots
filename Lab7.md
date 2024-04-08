### Kalman

### Lab Objective

The goal for this lab is to incorporate a Kalman Filter to help the performance and speed of execution of the task we did in Lab 5. The Kalman Filter will be used to supplement the slowly sampled ToF values.

### Estimate Drag and Momentum

The Kalman Filter operates by first predicitng the robot's position using a state space model and then refining this forecast with actual sensor data. See function below.

pic of formula

To identify the A, B, and C matrices necessary for the Kalman Filter, I created a state space model specific to our robot. This model uses the distance between the robot and the wall, which represents our position, and the robot's velocity. See the details of this model below:

pic of matrix

To estimate drag and momentum I used the following:

pic of d and m 

I used a constant step response experiment towards a wall, setting the PWM value to 200. This approach generated the following data:

pic of graph

Looking at the graph we can see that the speed reaches steady state at a derivative of about 22613mm/s

calculations for d and m and matrices



### Initialize Kalman Filter

To discretize the matrices I previously calculated, it was necessary to select a specific sampling rate for predicting filter values. I used a sampling rate of 20ms. The formula is as follows:

formula and calc

Then, I had to estimate the noise variables, sigma_u and sigma_z.

vars

calculations

Having calculated these values, I was able to implement the Kalman Filter using the function that was provided. I used the code that was given to us and my created matrix is shown below. 

code

grpah of KL

### Implement and test your Kalman Filter in Jupyter

