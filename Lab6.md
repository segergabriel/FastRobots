## Lab 6: Orientation Control

### Lab Objective
In this lab, we continue our exploration of PID control, shifting our focus to implementing an orientation PID using the IMU. Previously, we applied PID control to manage wall distance through ToF sensors. This time, the objective is to control the yaw orientation of the robot. 

### Lab Tasks

#### Bluetooth
Bluetooth communication was set up in the same way as Lab 5. The only addition was an extra buffer to track the speed of both wheels, which was needed to graph motor offsets. 

#### PID Discussion 
For an in-depth PID discussion refer to [Lab 5](https://segergabriel.github.io/FastRobots/Lab5.html). The same principles and math are taken into account for my implementation in this lab. The procedure also follows the same protocol: starting with Kp, followed by Ki and Kd. Note that these values are adjusted for my trials, as shown in the results section.  

Theoretically, as one increases the proportional gain, the system becomes faster but might become unstable. The integral term reduces the steady-state error but can increase overshoot. Finally, increasing the derivative term decreases overshoot and yields higher gain with stability, but it also makes the system more sensitive to noise. 

#### PID Implementation
The first step was to implement the PID controller to control the orientation of the robot while it is stationary. The input to the PID controller is the yaw of the robot, which is integrated from the gyroscope readings. The set point is the orientation of the robot at startup, and the output is half the difference between the two wheel PWM inputs.

I created my own PID control function, very similar to Lab 5. It operates by inputting the computed `duty_cycle` into the `difference` variable that is passed along to `writeDifferential()`, which adjusts the speed of the wheels. To calculate the error, it uses the global variable `current_orientation`, which I will explain later. Below is my implementation:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6orient.png?raw=true" alt="Orientation PID code" style="max-width:420px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

I created `updateOrientation()` which updates the global variable `current_orientation`. It does this by integrating angular velocity to get change in orientation. The formula is:  
**Angle θ = gyrz * elapsedTime**  

It’s called right before the PID function in my loop, ensuring it’s always updated when a new reading is ready:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6update.png?raw=true" alt="updateOrientation code" style="max-width:400px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Finally, `writeDifferential()` was created to adjust the speed of the motors. As mentioned earlier, it takes `duty_cycle` as an input and determines the speed of all motors. I also used the `constrain()` function to bound the values:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6writediff.png?raw=true" alt="writeDifferential code" style="max-width:400px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Similar to Lab 5, an if-statement was established in my main loop to monitor the IMU for new data availability. When the flag is high and new data is detected, `updateOrientation` and `orientPid` are called:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6loop.png?raw=true" alt="Loop code" style="max-width:260px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### Input Signal and Derivative Term
Integrating gyroscope readings is important for accurately determining the robot's orientation. It converts angular velocity into rotation angle over time, which is important for navigation and stability.  

However, it also introduces challenges like drift and noise accumulation over time. Addressing these might require filtering (e.g., Kalman filter), or increasing the sampling rate. Combining gyroscope data with accelerometers can also provide better estimates. I did not observe extreme bias for my sensors, so this was not a problem here.  

Differentiating a noisy gyroscope signal can amplify noise, leading to unpredictable control signals. This might require low-pass filtering to smooth out the noise. Derivative kick also occurs when a setpoint changes quickly, causing spikes in the output. It can be mitigated by implementing derivative on measurement rather than error, or gradually adjusting setpoints. In my system, adjusting setpoints worsened results, so I kept them constant.  

### Results

I started by focusing only on the Kp term and increased it up to 20. At this value, the system responded fairly quickly but overshot a bit.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/04hKdvyMYvU" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle.png?raw=true" alt="Trial 1 angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed.png?raw=true" alt="Trial 1 speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

For my second trial, I added the Ki term (set to 0.1) while keeping Kp at 20. The car responded quicker but overshot more, which was expected.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/90U-nfSJgQw" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle2.png?raw=true" alt="Trial 2 angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed2.png?raw=true" alt="Trial 2 speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

For the third trial, I added the Kd term (set to 0.1) while the other two were kept the same. The car was stable and responded quickly, but still overshot slightly. 

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/L9Z9cazYddM" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle3.png?raw=true" alt="Trial 3 angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed3.png?raw=true" alt="Trial 3 speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

I kept adjusting the parameters by progressively increasing Kp, Ki, and Kd, each time pausing when I observed oscillations or undesirable behavior. For rotation, it was important to maintain moderate speeds due to the gyroscope's maximum measurable rate per reading.  

The final values I settled on were **Kp = 35, Ki = 0.004, Kd = 35**. This produced a quick response with small overshoot.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/eIzzpz8a5vg" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle4.png?raw=true" alt="Final trial angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed4.png?raw=true" alt="Final trial speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

<div class="nav-buttons" style="display:flex; justify-content:space-between; gap:12px; margin-top:2em;">
  <a href="./Lab5.html" class="nav-btn">← Prev Lab</a>
  <a href="./Lab7.html" class="nav-btn">Next Lab →</a>
</div>

