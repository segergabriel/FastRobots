## Lab 7: Kalman Filter

### Lab Objective
The goal for this lab is to incorporate a Kalman Filter to improve the performance and speed of execution of the task from Lab 5. The Kalman Filter will be used to supplement the slowly sampled ToF values.

### Estimate Drag and Momentum
The Kalman Filter operates by first predicting the robot's position using a state-space model and then refining this forecast with actual sensor data. See the function below:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7KF1.png?raw=true" alt="Kalman function" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

To identify the A, B, and C matrices necessary for the Kalman Filter, I created a state-space model specific to our robot. This model uses the distance between the robot and the wall (position) and the robot's velocity. See the details below:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7KF2.png?raw=true" alt="State space model" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

To estimate drag (d) and momentum (m) I used the following two formulas:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7d.png?raw=true" alt="Drag formula" style="max-width:320px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7m.png?raw=true" alt="Momentum formula" style="max-width:320px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

For the step response, the car was driven at a constant speed towards a wall to find steady-state velocity, d, and m with a PWM value of 200. The 90% rise time, representing the time it takes for the car to reach 90% of steady-state velocity, was also determined. This approach generated the following data:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7fig1.png?raw=true" alt="Step response figure" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

The 90% step time required finding the time at which the car reached 2200 mm/s * 0.9 = 1980 mm/s. The 90% rise time is around 1s. So we have:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7math.png?raw=true" alt="90% rise time calculation" style="max-width:360px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

And my matrices then become the following:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7abc.png?raw=true" alt="Matrices" style="max-width:420px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Note: the C matrix was initialized as **C = [-1, 0]**, same as in lecture, because we are measuring negative distance from the wall.

---

### Initialize Kalman Filter
To discretize the matrices, it was necessary to select a specific sampling rate for predicting filter values. I used a sampling rate of 20ms. The formula is shown below:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7initKF.png?raw=true" alt="Discretization formula" style="max-width:320px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

So my matrices are now the following:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7ad.png?raw=true" alt="Discrete matrices" style="max-width:420px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Next, I had to estimate the noise variables, sigma_u and sigma_z:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7math2.png?raw=true" alt="Noise variables" style="max-width:380px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

### Implement and Test the Kalman Filter in Jupyter
Having calculated these values, I was able to implement the Kalman Filter using the provided function. I added my matrices into the given code. This implementation and the result are shown below:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7code.png?raw=true" alt="Kalman Filter implementation" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/7est.png?raw=true" alt="Kalman Filter results" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

The Kalman Filter effectively follows the pattern of the data and accurately predicts the subsequent ToF measurement. A sampling rate of 100ms was used, aligning with the sensor's sampling frequency.

---

<div class="nav-buttons" style="display:flex; justify-content:space-between; gap:12px; margin-top:2em;">
  <a href="./Lab6.html" class="nav-btn">← Prev Lab</a>
  <a href="./Lab8.html" class="nav-btn">Next Lab →</a>
</div>
