## Lab 6: Orientation Control

### Lab Objective

In this lab, we continue our exploration of PID control, shifting our focus to implementing an orientation PID using the IMU. Previously,
we applied PID control to manage wall distance through TOF sensors. This time, the objective is to control the yaw orientation of the robot. 

Bluetooth same as lab 5





### Lab Tasks
### P/I/D Discussion 
Graphs, code, videos, images, discussion of reaching task goal
Graph data should at least include theta vs time (you can also consider angular velocity, motor input, etc)

### Lab Procedure

The first step would be to implement the PID controller to control the orientation of the robot while it is stationary. The input to the PID controller is the yaw of the robot, 
which will be integrated over the gyroscope. The set point would be the orientation at start up, and the output is half the difference between the two wheel PWM inputs.See my implmenetation below.

image of writediff

I created my own PID control, which is similiar to lab 5, this is shown below.

pic of orientpid


You should integrate your gyroscope to get an estimate for the orientation of the robot.
Are there any problems that digital integration might lead to over time? Are there ways to minimize these problems?
Does your sensor have any bias, and are there ways to fix this?

Derivative Term

Does it make sense to take the derivative of a signal that is the integral of another signal.
Think about derivative kick. Does changing your setpoint while the robot is running cause problems with your implementation of the PID controller?


### Results

Video 1 of kp
graph, set point angle and motor offsets

video 2 kp and ki
graph

video 3 kd too
graph

