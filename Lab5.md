## LAB 5: Linear PID Control and Linear Interpolation

### Lab Objective
The goal for this lab was to implement a PID control on the robot. A ToF sensor was used to simulate a simple form of control.

---

### Prelab

#### Sending and Receiving Data over Bluetooth
To send and receive data, I used the same `SEND_DATA` command as in previous labs, with some slight modifications in order to send over the current time, ToF sensor data, and the speed of the motors over Bluetooth. Below are the buffers that my values were stored in:

![Buffers](https://github.com/segergabriel/FastRobots/blob/main/images/5buffers.png?raw=true)

One can see that it stores data as long as it hasn’t reached the maximum data size. This process occurs within a time loop, ensuring that whenever data becomes available, it’s captured in an array, timestamped, and then processed through PID control. At the end of the loop, the arrays are sent to the host computer similar to previous labs. In Python, this data is then organized into a dictionary in the same way as before. In the lab task I’ll explain in more detail how commands and cases were used.  

---

### Lab Tasks

#### Range and Sampling Time Discussion
I gathered all the sensor data at once, and by tracking the interval between each measurement, I determined the average sampling period to be around 98ms. See the image below:

![Frequency Output](https://github.com/segergabriel/FastRobots/blob/main/images/5freqout1.png?raw=true)

This equates to a sampling frequency of about 10Hz, which limits the loop’s execution speed to a maximum of 10Hz. Later, I’ll have to separate these measurements to enhance the IMU measurements’ sampling rate. From our findings in Lab 3, we know that the ToF sensor’s effective range is up to 4m, indicating that the maximum potential error from the ToF sensor could reach up to 4000mm.  

Additionally, I needed to find the robot’s top speed. For this, I analyzed one of the trials by calculating the slope between two points:

![Motor Speed](https://github.com/segergabriel/FastRobots/blob/main/images/5motorfig.png?raw=true)

It resulted in a calculated speed of 2.1 m/s. This value is on the higher side considering that trials initiated closer to the wall yielded much slower speeds. In previous labs, I also identified that the deadband corresponds to a PWM value of 35, which was an important factor to consider.

---

### PID Discussion
A PID controller takes in the error of the desired value and the current value, and uses proportional, integral, and derivative control to change the control input of the robot to the desired value. Below is the formula:

![PID Formula](https://github.com/segergabriel/FastRobots/blob/main/images/5formula.png?raw=true)

The first parameter to find was **Kp**. Given that proportional control involves the multiplication of the error by a constant to estimate a PWM value, the equation for the maximum PWM value can be set as:  

**Maximum PWM value = (maximum error) × Kp**  

Kp is also approximately equal to the ratio of the maximum PWM value to the maximum error, calculated as 255/4000, which gives us approximately 0.06. This suggested that the magnitude of Kp should be around this value.  

For **Ki**, considering it multiplies the cumulative error which increases with each sampling period (around 98ms). If Ki were to mirror the influence of Kp, then Ki would equal Kp/sampling frequency, which is 0.006 when considering a 10Hz frequency. To prevent the integral sum from becoming too large and since the robot starts far from the wall, the error accumulation was capped at approximately 100mm.  

**Kd** relates to the rate of error change, acting as the derivative of the position. Given the robot’s maximum speed of 2.1 m/s, Kd can be estimated by dividing the maximum PWM by the maximum rate of change in error, which is (255 × 0.1s) / 2.1 m/s ≈ 12. Kd’s impact is meant to be smaller than Kp’s, so it was therefore adjusted to about 0.1 to start.  

These values were adjusted during trials as seen in the results section. Theoretically:  
- Increasing the proportional gain makes the system faster, but may cause instability.  
- The integral term reduces steady-state error but can increase overshoot.  
- Increasing the derivative term decreases overshoot and yields higher gain with stability but makes the system highly sensitive to noise.  

---

### PID Implementation
A function `pid()` was created to implement the PID controller:

![PID Code](https://github.com/segergabriel/FastRobots/blob/main/images/5pidcode.png?raw=true)

It takes Kp, Ki, and Kd as inputs and then does calculations based on these combined with the sensor reading to determine the PWM value of the motors.  

I used my deadband value as the `min_threshold` value to ensure the PWM outputs are sufficient to initiate movement of the car, which was set to 35 based on Lab 5. I also had to regulate the accumulator for easier sign reversal of the error. The integral accumulator effectively managed potential windup issues and was set to 100 as previously noted.  

Due to the gradual change in error and the low frequency of data from the ToF sensor, implementing a low-pass filter was deemed unnecessary. Adjustments regarding derivative kick were also not needed as my error derivative is continuous.  

I created the command `SET_PID` to change my PID values over Bluetooth:  

![Set PID](https://github.com/segergabriel/FastRobots/blob/main/images/5setpid.png?raw=true)

I also created the command `START_PID`, which allowed me to start and stop the robot over Bluetooth. This was a big advantage when debugging. It basically sets a flag low or high, and then in my main loop, as long as that flag is high, the PID function is running.  

![Start PID](https://github.com/segergabriel/FastRobots/blob/main/images/5startpid.png?raw=true)  
![Start PID Loop](https://github.com/segergabriel/FastRobots/blob/main/images/5startpidloop.png?raw=true)

The `SEND_DATA` command was used to send over the collected data:  

![Send Data](https://github.com/segergabriel/FastRobots/blob/main/images/5senddata.png?raw=true)

Finally, they are called in Jupyter like this:  

![Jupyter Call](https://github.com/segergabriel/FastRobots/blob/main/images/5jupcall.png?raw=true)

---

### Results
To find the right values, I used the 2nd heuristic approach discussed in lecture. I also had an idea of what range the values should be in from my calculations.  

**Trial 1** – Focused on Kp only, set to 0.06. The car overshot the 300mm mark significantly.  
<iframe width="420" height="315" src="https://www.youtube.com/embed/3vLwYi0CzaI"></iframe>  
![Trial 1](https://github.com/segergabriel/FastRobots/blob/main/images/5trial1.png?raw=true)

**Trial 2** – Added Ki = 0.1 while Kp = 0.08. The car moved fast but hit the wall, as the integral term increased overshoot.  
<iframe width="420" height="315" src="https://www.youtube.com/embed/9bH4EelaNU0"></iframe>  
![Trial 2](https://github.com/segergabriel/FastRobots/blob/main/images/5trial2.png?raw=true)

**Trial 3** – Added Kd = 0.1 while keeping others the same. The car still overshot but didn’t hit the wall.  
<iframe width="420" height="315" src="https://www.youtube.com/embed/lNdR3JWTXPk"></iframe>  
![Trial 3](https://github.com/segergabriel/FastRobots/blob/main/images/5trial3.png?raw=true)

**Trial 4** – Decreased Ki to 0.001 and increased Kd to 2. This gave more stability and less overshoot.  
<iframe width="420" height="315" src="https://www.youtube.com/embed/9bH4EelaNU0"></iframe>

**Trial 5** – Changed Kp back to 0.06. The system was stable but the car moved fairly slow.  
<iframe width="420" height="315" src="https://www.youtube.com/embed/d7FBGt1Q8E8"></iframe>

**Final Trial** – Kp = 0.08, Ki = 0.004, Kd = 0.25. This resulted in a stable system while the car moved faster as well.  
<iframe width="420" height="315" src="https://www.youtube.com/embed/I1Bm2QJEp0I"></iframe>  
![Final Trial](https://github.com/segergabriel/FastRobots/blob/main/images/5finaltrial.png?raw=true)

---

### Extrapolation
Previously I determined that the ToF sensor was returning new data every 98ms. This was also the rate that the PID control loop was running, which is slow. Therefore, we wanted to decouple these two rates.  

I changed my loop to calculate the PID control every loop, even if there was no new data from the ToF sensor. I did this by checking if new data from the ToF sensor was ready. If it was, I updated the variable that the PID controller uses to estimate the motor speed. Otherwise, I recalculated the PID control using the last saved data point. This made my PID controller run much faster than before, executing every 2ms.  

![Frequency 2](https://github.com/segergabriel/FastRobots/blob/main/images/5freq2.png?raw=true)

Finally, I wanted to extrapolate an estimate for the car’s distance to the wall using the last two data readings from the ToF sensor. I calculated the slope from the last two data points and extrapolated the current distance based on the time elapsed since the last reading and the slope. Every time I got a new ToF reading, I used it along with the previous reading to estimate the current distance until a new reading was received. I created an `estimateDistance` function to help with this.  

![PID Extrapolation](https://github.com/segergabriel/FastRobots/blob/main/images/5pidextra.png?raw=true)  
![Extrapolation Loop](https://github.com/segergabriel/FastRobots/blob/main/images/5extraloop.png?raw=true)

Here is the resulting video and figure:  
<iframe width="420" height="315" src="https://www.youtube.com/embed/soxy59opGnA"></iframe>  
![Extrapolation Result](https://github.com/segergabriel/FastRobots/blob/main/images/5extrafig.png?raw=true)

---

<div class="nav-buttons" style="display:flex; justify-content:space-between; gap:12px; margin-top:2em;">
  <a href="./Lab4.html" class="nav-btn">← Prev Lab</a>
  <a href="./Lab6.html" class="nav-btn">Next Lab →</a>
</div>

