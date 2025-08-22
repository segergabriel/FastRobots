## Lab 6: Orientation Control

### Lab Objective
In this lab, we continue our exploration of PID control, shifting our focus to implementing an orientation PID using the IMU. Previously, we applied PID control to manage wall distance through ToF sensors. This time, the objective is to control the yaw orientation of the robot. 

---

### Lab Tasks

#### Bluetooth
Bluetooth communication was set up in the same way as Lab 5. The only addition was an extra buffer to track the speed of both wheels, which was needed to graph motor offsets. 

#### PID Discussion 
For an in-depth PID discussion, refer to [Lab 5](https://segergabriel.github.io/FastRobots/Lab5.html). The same principles and math are taken into account for my implementation here. The procedure also follows the same protocol: starting with Kp, followed by Ki and Kd.  

These values are adjusted during trials (see Results). Theoretically:  
- Increasing the proportional gain makes the system faster, but risks instability.  
- The integral term reduces steady-state error but can increase overshoot.  
- The derivative term decreases overshoot and yields higher gain with stability, but makes the system highly sensitive to noise.  

These tradeoffs were considered in my design.

---

### PID Implementation
The first step was to implement the PID controller to control the robot’s orientation while stationary.  

- **Input**: yaw of the robot, integrated from gyroscope readings.  
- **Setpoint**: orientation at startup.  
- **Output**: half the difference between the two wheel PWM inputs.  

I created my own PID control function, very similar to Lab 5. It operates by inputting the computed `duty_cycle` into the `difference` variable, which is passed to `writeDifferential()`. This adjusts the speed of the wheels. To calculate error, it uses the global variable `current_orientation`. Implementation is shown below:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6orient.png?raw=true" alt="PID orientation function" style="max-width:400px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

I created `updateOrientation()`, which updates the global variable `current_orientation`. It integrates angular velocity to get change in orientation using:

**θ = gyrz × elapsedTime**  

It’s called right before the PID function in the loop, ensuring it’s always updated when new readings are ready:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6update.png?raw=true" alt="Update orientation function" style="max-width:400px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Finally, `writeDifferential()` adjusts the motor speeds. It takes `duty_cycle` as input and determines the wheel speeds, with `constrain()` used to bound values:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6writediff.png?raw=true" alt="Write differential function" style="max-width:400px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Similar to Lab 5, an if-statement in the main loop checks the IMU for new data. When the flag is high and new data is detected, `updateOrientation()` and `orientPid()` are called:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6loop.png?raw=true" alt="Loop function" style="max-width:300px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

### Input Signal and Derivative Term
Integrating gyroscope readings is important for accurately determining orientation. It converts angular velocity into rotation angle over time, which is crucial for navigation and stability.  

However, it also introduces challenges like drift and noise accumulation. These could be mitigated with filtering (e.g., Kalman filter) or higher sampling rates. Combining gyroscope and accelerometer data can also improve accuracy. My sensors did not show extreme bias, so this wasn’t a problem in this lab.  

Differentiating integrated signals can amplify noise, producing unpredictable control signals. Low-pass filters could be used to address this.  

Derivative kick occurs when the setpoint changes quickly, causing output spikes. This can be solved by applying derivative to the measurement rather than the error, or by gradually adjusting setpoints. I attempted setpoint adjustments, but results were worse, so I kept the setpoint fixed.

---

### Results

#### Trial 1 – Proportional only  
I started with only Kp, increasing it up to 20. At this value, the system responded quickly but overshot slightly.  

<iframe width="420" height="315" src="https://www.youtube.com/embed/04hKdvyMYvU"></iframe>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle.png?raw=true" alt="Trial 1 angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>
<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed.png?raw=true" alt="Trial 1 speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

#### Trial 2 – Add integral  
Added Ki = 0.1 while keeping Kp = 20. The car responds quicker but overshoots more (expected).  

<iframe width="420" height="315" src="https://www.youtube.com/embed/90U-nfSJgQw"></iframe>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle2.png?raw=true" alt="Trial 2 angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>
<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed2.png?raw=true" alt="Trial 2 speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

#### Trial 3 – Add derivative  
Added Kd = 0.1, keeping Kp = 20 and Ki = 0.1. The car is stable and quick but still overshoots a little.  

<iframe width="420" height="315" src="https://www.youtube.com/embed/L9Z9cazYddM"></iframe>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle3.png?raw=true" alt="Trial 3 angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>
<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed3.png?raw=true" alt="Trial 3 speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

#### Final Tuning  
I kept adjusting parameters (Kp, Ki, Kd), pausing whenever oscillations or instability appeared. For rotation control, moderate speeds were needed due to gyroscope limits. My target was quick response with minimal overshoot.  

Final values: **Kp = 20, Ki = 0.004, Kd = 35**.  

<iframe width="420" height="315" src="https://www.youtube.com/embed/eIzzpz8a5vg"></iframe>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6angle4.png?raw=true" alt="Final angle" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>
<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/6speed4.png?raw=true" alt="Final speed" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

<div class="nav-buttons" style="display:flex; justify-content:space-between; gap:12px; margin-top:2em;">
  <a href="./Lab5.html" class="nav-btn">← Prev Lab</a>
  <a href="./Lab7.html" class="nav-btn">Next Lab →</a>
</div>
