## Lab 6: Orientation Control

### Lab Objective

In this lab, we continue our exploration of PID control, shifting our focus to implementing an orientation PID using the IMU. Previously,
we applied PID control to manage wall distance through TOF sensors. This time, the objective is to control the yaw orientation of the robot. 

### Lab Tasks

#### Bluetooth
Bluetooth communication was set up in the same way as Lab 5. The only thing that was added was an additional buffer in order to track the speed of both wheels, which was needed to graph motor offsets. 

#### PID Discussion 


Graphs, code, videos, images, discussion of reaching task goal
Graph data should at least include theta vs time (you can also consider angular velocity, motor input, etc)

#### PID Implementation

The first step is to implement the PID controller to control the orientation of the robot while it is stationary. The input to the PID controller is the yaw of the robot, 
which will be integrated from the gyroscope readings. The set point would be the orientation of the robot at start up, and the output is half the difference between the two wheel PWM inputs.

I created my own PID control function, which is very similiar to lab 5. It operates by inputting the computed "duty_cycle" into the "difference" variable that will be past along to writeDifferential(), which adjust the speed of the wheels. To calculate the error, it uses the global variable curretn_orientation, which I will explain later, below is my implementation. 

pic of orientpid

I created updateOrieantion() which updates the global variable "current_orientation." It does this be integrating angular velocity to get change in orientation. Using the following formula: Angle θ = gyrz * elapsedTime. It's called right before the pid function in my loop, making sure it's always updated when a new reading is ready. See the function below. 

pic of updateorient

Finally, writeDifferential() was created which would adjust the speed of the motors. As mentioned earlier, it takes "duty_cycle" as an input and then determine the speed of all motors. I also used the contrain function here so, the values are bounded. This is shown below. 


image of writediff

Similiar to lab 5, a if statement in my main loop was established to monitor the IMU for new data availability. When the flag is high and new data was detected, updateorient and pid was called. 

pic of main loop

### Input Signal and Derivative term

Integrating gyroscope readings, as I did in my implementation, is important for accurately determining the robot's orientation. It converts angular velocity measured by the gyroscope into the rotation angle over time and this is important for effective navigation and stability.

However, it also introduces challenges like drift and noise accumulation over time. Addressing these might require filtering such as using a Kalman filter to smooth out noise, or increasing the sampling rate for better accuracy. Combining gyroscope data with other sensors like accelerometers, can also provide better estimates. I did not have any extreme bias for my sensors so that is not a problem for now. 

Integrating a gyroscope's output to estimate orientation and and then differentiating this signal for control purposes can introduce challenges, particularly in terms of noise amplification and derivative kick. Differentiating a signal that has been integrated from a noisy source like a gyroscope, can amplify the noise, leading to unpredicatable control signals. So, we might have to use low-pass filters, to smooth out the noise. I'm not doing using this for this lab but it's something to be aware of in the future. 

Derivative kick happens when a setpoint changes quickly and causes a large spike in the output. This is a issue when we differentiate the error signal. We can solve this by implementing derivative on measurement rather than on the error, which avoids spikes due to setpoint changes. We can also gradually adjusting setpoints to minimize sudden jumps. I tried to adjust the setpoints for my systems, but the results got worse then before so, for now I keep the setpoint the same.  

### Results

I started by only focusing on the Kp term and increased it from a low value up to 20. At this value, the system responded quickly but seemed to overshoot a bit.  



Video 1 of kp
graph, set point angle and motor offsets

For my second trial I added the Ki term which was set to 0.1 while Kp was kept the same. The car responds quicker but overshoots more, which is expected as the integral term increases overshoot.

video 2 kp and ki
graph

For the third trial I added the Kd term which was set to 0.1 while the other two were kept the same. The car still is now pretty stable and responds quickly but overshoots a little bit. 

video 3 kd too
graph

I kept adjusting the parameters by progressively increasing kp, ki, and kd, each time pausing the adjustment when I observed oscillations or undesirable behavior. Also not that for managing rotation, it was important to maintain moderate speeds due to the gyroscope's limitation on the maximum rate of angle change it could accurately measure per reading. Below is my final result.

vid 4 with all working and tweaked
graphs
