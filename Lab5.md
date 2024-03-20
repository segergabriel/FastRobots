## LAB 5: Linear PID control and Linear interpolation

### Lab Objective

The goal for this lab was to implement a PID control on the robot. One of the ToF sensors was used to simulate a simple form of control.

### Prelab

#### Sending and receiving data over Bluetooth

To send and receive data, I used the same "SEND_DATA"command as in previous labs, with some slight modifications in order to send over the current time, TOF sensor data and the speed of the motors over bluetooth. Below are the buffers that my values were stored in. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5buffers.png?raw=true)

One can see that it stores data as long as it haven't reached the maximum data size. This process occurs within a time loop, ensuring that whenever data becomes available, it's captured in an array, timestamped, and then processed through PID control. At the end of the loop, the arrays are sent to the host computer similar previous labs. In Python, this data is then organized into a dictionary in the same way as before. In the lab task I'll explain in more detail on how commands and cases were used. 

### Lab Tasks

#### Range and Sampling Time Discussion

I gathered all the sensor data at once, and by tracking the interval between each measurement, I determined the average sampling period to be around 98ms. See the image below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5freqout1.png?raw=true)

This equates to a sampling frequency of about 10Hz, which limis the loop's execution speed to a maximum of 10Hz. Later, I'll have to separate these measurements to enhance the IMU measurements' sampling rate. From our findings in Lab 3, we know that the TOF sensor's effective range is up to 4m, indicating that the maximum potential error from the TOF sensor could reach up to 4000mm. Additionally, it needed to find the robot's top speed. For this, I analyzed one of the trials by calculating the slope between two points.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5motorfig.png?raw=true)

It resulted in a calculated speed of 2.1 m/s. This value is on the higher side considering that trials initiated closer to the wall yielded speeds much slower. In previous labs, I also identified that the deadband corresponds to a PWM value of 35, which was an important factor to consider.

### PID Discussion 

A PID controller takes in the error of the desired value and the current value, and uses a proportional, integral, and derivative controller to change the control input of the robot to the desired value. Below is the formula.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5formula.png?raw=true)

The first parameter to find was Kp. Given that proportional control involves the multiplication of the error by a constant to estimate a PWM value, the equation for the maximum PWM value can be set as follows: 
Maximum PWM value = (maximum error) * Kp. 
Kp is also approximately equal to the ratio of the maximum PWM value to the maximum error, calculated as 255/4000, which gives us approximately 0.06. This suggested that the magnitude of Kp should be around this value.

For Ki, considering it multiplies the cumulative error which increases with each sampling period being around 98ms. If Ki were to mirror the influence of Kp, then Ki would equal Kp/sampling frequency, which is 0.006 when considering a 10Hz frequency. To prevent the integral sum from becoming too large and since the robot starts far from the wall, the error accumulation was capped at approximately 100mm.

Kd relates to the rate of error change, acting as the derivative of the position. Given the robot's maximum speed of 2.1m/s, Kd can be estimated by dividing the maximum PWM by the maximum rate of change in error, which is (255 * 0.1s) / 2.1m/s, resulting in approximately 12. Kd's impact is meant to be ssmaller than Kp's so it was therefore adjusted to about 0.1 to start.

These values will be adjusted for my trials as one can see in the results section. Theoretically, as one increases the proportional gain, the system becomes faster, but the system might become unstable. The integral term reduces the steady state error, but can increase overshoot but this will be tweaked to achieve a minimal steady state error. Finally, increasing the derivative term decreases overshoot and yields higher gain with stability but causes the system to be highly sensitive to noise. This will be considered when creating my system. 

### PID Implementation

A function pid() was created to implement the PID controller. This is shown below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5pidcode.png?raw=true)

I takes Kp, Ki and Kd as inputs and then do calculations based on these combined with the sensor reading to determine pwm value of the motors. 

I used my deadband value as the 'min_threshold' value to ensure the PWM outputs are sufficient to initiate movement of the car, which was set to 35 based on lab 5. I also had to regulate the accumulator for easier sign reversal of the error. The integral accumulator effectively managed any potential windup issues and was set to 100 as previously noted. Due to the gradual change in error and the low frequency of data from the TOF sensor, implementing a low pass filter was deemed unnecessary. Finally, adjustments regarding derivative kick were not needed as my error derivative is continuous. 

I created command, “SET_PID” to change my pid values over bluetooth as shown below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5setpid.png?raw=true)

I also created a command, “START_PID”, which allowed me to start and stop the robot over bluetooth. This was a big advantage when debugging. It basically sets a flag low or high and then in my main loop, as long as that flag is high, the pid function is running. My implementation is shown below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5startpid.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5startpidloop.png?raw=true)

And here is the "SEND_DATA" command which is used to send over the collected data. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5senddata.png?raw=true)

Finally, they are called in Jupyter like this. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5jupcall.png?raw=true)

### Results

To find the right values, I used the 2nd heuristic approach which was discussed in lecture. I also had an idea of what range the values shoulf be in form my calculations. So, for my first trial I focused on the Kp term and increased it from about 0.006 until the car overshot the 300mm mark. The Kp value for this trial was set to 0.06. As one can see below, the car overshoots a lot.  

<iframe width="420" height="315"
src="https://www.youtube.com/embed/3vLwYi0CzaI">
</iframe>

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5trial1.png?raw=true)

For my second trial I added the Ki term which was set to 0.1 while Kp was 0.08. The car moves fast but hits the wall, which is expected as the integral term increases overshoot.

<iframe width="420" height="315"
src="https://www.youtube.com/embed/9bH4EelaNU0">
</iframe>

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5trial2.png?raw=true)

For the third trial I added the Kd term which was set to 0.1 while the other two were kept the same. The car still overshoots but doesn't hit the wall now. 

<iframe width="420" height="315"
src="https://www.youtube.com/embed/lNdR3JWTXPk">
</iframe>

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5trial3.png?raw=true)

For my fourth trial, I decreased Ki to 0.001 and increased Kd to 2, in order to get more stability and less overshoot. This is shown below. 

<iframe width="420" height="315"
src="https://www.youtube.com/embed/9bH4EelaNU0">
</iframe>

For the fifth trial I changed Kp back to 0.06. This reulted in a stable system but the car was moving a fairly slow. 

<iframe width="420" height="315"
src="https://www.youtube.com/embed/d7FBGt1Q8E8">
</iframe>

For my final trial, I changed Kp to 0.08, Ki to 0.004 and Kd to 0.25. This also resulted in a pretty stable system while the car moved faster as well.

<iframe width="420" height="315"
src="https://www.youtube.com/embed/I1Bm2QJEp0I">
</iframe>

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5finaltrial.png?raw=true)

### Extrapolation

Previously I determined that the ToF sensor is returning data new data every 98 ms. This is also the rate that the PID control loop was running as well, which is slow. Therefore, we wanted to decouple these two rates. 

So, I change my loop to calulate the PID control every loop, even if there is no new data from the ToF sensor. I did this by checking if new data from the ToF sensor is ready. If it was, I updated the variable that PID controller is using to estimate the motor speed. Otherwise I recalculate the PID control using using the last saved datapoint. This made my PID controller run much faster than before, as it was executed every 2ms. See picture below.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5freq2.png?raw=true)

Finally I wanted to extrapolate an estimate for the car’s distance to the wall using the last two datareadings from the ToF sensor. So, I calcuated the slope from the last two datapoint, and extrapolated the current distance based on the amount of time that has passed since the last reading and the slope. Everytime I got a new ToF reading, I used it along with the previous reading to estimate the current distance to the wall until a new reading were recieved. I created a estimateDistance funtion to help with this. See my full implementation below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5pidextra.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5extraloop.png?raw=true)

Here is the resulting video and figure. 

<iframe width="420" height="315"
src="https://www.youtube.com/embed/soxy59opGnA">
</iframe>

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5freq2.png?raw=true)


