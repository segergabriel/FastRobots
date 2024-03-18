## LAB 5: Linear PID control and Linear interpolation

### Lab Objective

In this lab, PID control was implemented on the robot, using one of the ToF sensors to simulate the simplest form of control.

### Prelab

#### Sending and receiving data over Bluetooth

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5buffers.png?raw=true)
pics of buffers

pics of case

pics of jupyter, used same as before but added things

Consider adding code snippets as necessary to showcase how you implemented this on Arduino and Python




### Lab Tasks


### Range/Sampling time discussion

98 ms /s

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5freqout1.png?raw=true)

pic of motor vs distance

code output of time between loop

write about thism calculations too

### PID Discussion 

kp first, go over all values

### PID Implementation

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5pidcode.png?raw=true)
code 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5setpid.png?raw=true)
cases, set pid and start
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5startpid.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5startpidloop.png?raw=true)

 called in jupyter jupyter same as before, 
 ![advert](https://github.com/segergabriel/FastRobots/blob/main/images/5jupcall.png?raw=true)

### Results

Videos and explaining them

grpahs distance vs time



Graph data should include Tof vs time and Motor input vs time (and whatever helps with debugging)
also all tips

LOG DATA: If you don’t want to repeat work during Lab 7, be sure to log all data (time stamped sensor values and motor outputs), as well as setup variables from at least one successful run. Even if you are doing orientation control, be sure to log ToF data as you speed towards the wall as well.
Lectures: Brush up on your PID control skills by checking out Lectures 7 and 8.
PID library: There exists an Arduino PID library. You are welcome to use this library if you prefer, but we will only offer limited TA support if you run into issues. Implementing a basic PID controller from scratch is easy (<10 lines of code), and will give you more freedom in dealing with noise, wind-up, and system non-linearities.
Start simple: E.g. with a proportional controller running at low speeds and a generous setpoint, then you can work your way up to faster speeds, more advanced control, and more difficult setpoints if you have time.
Documentation: Please clearly document the maximum linear or angular speed you are able to achieve (you can use your sensors to compute this). To demonstrate reliability, please upload videos of at least three repeated (and hopefully successfull) experiments.
Frequency: Fast loop times means everything to a good controller. Be sure to include a discussion of sensor sampling rate and how this affects the timing of your control loop. Avoid using blocking statements when you can (e.g. delay() or while(sensor not ready) ){wait} ). Also, remember that any serial.print/BLE sending that occurs during execution may slow down your loop time considerably.
Deadband: From Lab 5, you should have found the deadband of your motor (the region below which the power to the motors does not overcome the static friction in your system). Consider writing a scaling function that converts the output from your PID controller to an output for which the motors can actually react.
Wind up: If you include an integrator, consider whether you need to worry about integrator wind-up.
Derivative LPF: If you include a derivative, consider whether it is necessary to include a low pass filter in the derivative branch.
Derivative kick: Consider whether the derivative kick can cause any issues, given the task you choose. Here is a great overview on how to eliminate derivative kick: http://brettbeauregard.com/blog/2011/04/improving-the-beginner’s-pid-derivative-kick/
Anything goes: The goal is a working system. When you have a reasonable control setup working, you should feel free to add any “hacks” that will improve your robot performance in a reliable way. If you don’t have time to implement them, discussing what you imagine would help can still get you grading points.
Motor drivers: Recall Lecture 6 on Actuators and that the motor drivers have both coasting and active breaking modes. These might come in handy.

### Extrapolation

writee about this

pics of code

videos of results


