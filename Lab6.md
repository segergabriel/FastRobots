## Lab 6: Orientation Control

### Lab Objective

In this lab, we continue our exploration of PID control, shifting our focus to implementing an orientation PID using the IMU. Previously,
we applied PID control to manage wall distance through TOF sensors. This time, the objective is to control the yaw orientation of the robot. 

### Lab Tasks

#### Bluetooth
Bluetooth communication was set up in the same way as Lab 5. The only thing that was added was an additional buffer, in order to track the speed of both wheels. 

#### PID Discussion 


Graphs, code, videos, images, discussion of reaching task goal
Graph data should at least include theta vs time (you can also consider angular velocity, motor input, etc)

#### PID Implementation

The first step would be to implement the PID controller to control the orientation of the robot while it is stationary. The input to the PID controller is the yaw of the robot, 
which will be integrated over the gyroscope. The set point would be the orientation at start up, and the output is half the difference between the two wheel PWM inputs. See my implementation below.

image of writediff

Created a update orieantion function that is called right before the pid function. this does this...update global variable orientioan see pic below

pic of updateorient

I created my own PID control, which is very similiar to lab 5. It operates by inputting the computed "duty_cycle" into the "difference" variable. this is shown below.

pic of orientpid

Angle θ = gyrz * elapsedTime

Similiar to lab 5, a loop was established to monitor the IMU for new data availability. When new data was detected, updateorient and pid was called. 

pic of main loop

### integration anad other stuff


Integrating gyroscope readings, as done in the updateOrientation function, is crucial for accurately determining a robot's orientation. This technique converts angular velocity measured by the gyroscope into the robot's cumulative rotation angle over time, providing a clear picture of its directional stance. Precise orientation is essential for effective navigation and stability, enabling the robot to perform tasks with higher accuracy and efficiency. The updateOrientation function showcases how this integration process is implemented, illustrating its significance in enhancing robotic control and operational capabilities.

Integrating gyroscope data to estimate a robot's orientation is crucial for precise movement and navigation, but it introduces challenges like drift and noise accumulation over time. Addressing these requires strategies like calibration to correct sensor bias, filtering (e.g., using Kalman filters) to smooth out noise, and increasing the sampling rate for better accuracy. Implementing sensor fusion, combining gyroscope data with other sensors like accelerometers, can provide more robust orientation estimates. Techniques like zero-rate updates, where the orientation is corrected during known stationary phases, also help minimize errors. These methods collectively enhance the accuracy of digital integration, making it a powerful tool for robotic orientation control.

no bias in sensors


Derivative Term

Integrating a gyroscope's output to estimate orientation and subsequently differentiating this signal for control purposes is a common practice in robotics and control systems. However, this method introduces challenges, particularly in terms of noise amplification and derivative kick. Differentiating a signal, especially one that has been integrated from a noisy source like a gyroscope, can significantly amplify the noise, leading to erratic control signals. This requires careful attention to signal processing techniques, such as the application of low-pass filters, to smooth out the noise while preserving the integrity of the control signal.

Moreover, derivative kick, a phenomenon that occurs when the setpoint changes abruptly, causing a large spike in the controller's output, can destabilize the system momentarily. This issue is particularly pronounced in PID controllers that differentiate the error signal. Strategies to mitigate this effect include implementing derivative on measurement rather than on error, which avoids spikes due to setpoint changes, and gradually adjusting setpoints to minimize sudden jumps. Understanding and addressing these challenges is crucial for developing stable and responsive control systems in robotics and automation.


### Results

I adjusted the parameters by progressively increasing k_p, k_i, and then k_d, each time pausing the adjustment when I observed the onset of oscillations or undesirable behavior. For managing rotation, it was crucial to maintain moderate speeds due to the gyroscope's limitation on the maximum rate of angle change it could accurately measure per reading.


Video 1 of kp
graph, set point angle and motor offsets

video 2 kp and ki
graph

video 3 kd too
graph

