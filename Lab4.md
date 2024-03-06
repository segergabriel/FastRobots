## LAB 4: Motors and Open Loop Control

### Lab Objective

The goal of this lab is to transition the control of our car from manual to open loop. This involved connecting the two motor drivers to the Artemis board and programming the car to perform a sequence of moves.

### Prelab

First I read throught the datasheet to understand the motors that we are working with. One can see it here:
https://www.ti.com/lit/ds/symlink/drv8833.pdf?HQS=dis-dk-null-digikeymode-dsf-pf-null-wwe&ts=1709691013509&ref_url=https%253A%252F%252Ffastrobotscornell.github.io%252F

Two dual motor drivers were then used to connect the motors to the Artemis and the pins had to be pwm capable in order to get input to them. The first motor was connected to pins 7 and 9 and the second one to pins 11 and 12. To integrate the motor drivers, the motor inputs and outputs were configured in parallel due to the utilization of only two inputs, despite four being available. 

Therefore, I soldered the driver's inputs and outputs together. We also had to decide on the power connection strategy for the motor drivers, given that the battery only have two leads. The solution was to run a wire from the VCC pad of one motor driver, soldering it in place, and then connecting a smaller wire from the VCC pad of the second driver to the main wire. The same strategy was applied for ground. See my schematic below. 

pic of schematic, do this
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/serialExample.png?raw=true)

We are using two 850mAh batteries, enabling us to power the Artemis and the motors independently to avoid transient effects. This is important because the changes in current drawn by the motors could potentially power off the Artemis, leading to a reset of the code when power is restored. I soldered one of the motor drivers according to the schematic above. Finally, one of the input terminals of this motor driver was connected to an oscilloscope, along with its VCC and ground.

pic of connection to oscilliscope, do this

### Power supply 

Given that the battery is supplying power to the motors at 3.7V, the power supply voltage was set to a range between 3.7V and 4.5V. To ensure there was no voltage drop when the motor was running, the current compliance of the power supply was adjusted. This adjustment resulted in a stable current compliance setting of approximately 2A.

### Testing motor drivers

The image below shows the oscilloscope readings when one of the motor driver inputs receives power. The is 3.15V, which is close to the expected 3.3V output from the GPIO pin. Additionally, the duty cycle has a ratio of approximately 195/256, indicating that the waveform remains mostly high throughout its period. The blue curve represents the positive output voltage to the motor while the yellow curve represents the negative output voltage to the motor. As expected, the yellow curve should be 0, while the blue curve shows the voltage values. 

pic of osci, do this


In the next step, the dual motor driver was connected to one of the motors. The video below shows the motors being powered by the power supply while rotating in alternating directions for 1 second each. The same thing was done for both sides of the car independently.

video of spinning one side

<iframe width="420" height="315"
src="https://www.youtube.com/embed/2yOHqZwpEz0">
</iframe>

The code below was used.ex
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/serialExample.png?raw=true)

code snippet for your analogWrite code that tests the motor drivers, left and right

Next, I connected the battery and executed the code for both motor drivers. I created a function to help with this. See below. 

pic of change speed func

I used this function to program both wheels to spin in both directions independently and together. See my code and the video of the wheels below. 

code of both wheels 

video of both wheels
<iframe width="420" height="315"
src="https://youtu.be/embed/q_gHfBxJ-Ms">
</iframe>

### Assembly
Picture of all the components secured in the car
Consider labeling your picture if you can’t see all the components

### Lower limit PWM value

when doing control theory later on, the output values to the motors should not be below this lower pwm threshold.

The motor’s were then tested to understand its range. To do this, the lower limit value of the robot was tested. This was done by changing the PWM value until the wheels were able to start moving from a stationary position. This was done for both sides of the robot, with the left and right wheels having a lower limit value of 89 and 96, respectively:

The motors were tested to determine their operational range, focusing on finding the minimum pwm threshold required to initiate motion from a standstill. This is important as when doing control theory later, the output values to the motors should not be below this pwm threshold. This involved gradually adjusting the PWM values until the wheels started moving. This procedure showed that the wheels have minimum activation PWM values of 31.

The videos demonstrate that at a PWM value of 31, the car's wheels begin to beat friction, but they fully beat the kinetic friction at 35. Therefore, a PWM setting of 31 is sufficient to beat dynamic friction, while 35 is effective for overcoming static friction.

video of not beating fric
<iframe width="420" height="315"
src="https://www.youtube.com/embed/fN-5_G622G8">
</iframe>


video of beating fric on ground

<iframe width="420" height="315"
src="https://www.youtube.com/embed/PyoF2chNE4k">
</iframe>


### Calibration 

Due to the motors producing different torque outputs, a calibration factor was necessary to ensure that both sides of the car moved equally, allowing the robot to move in a straight line.

Here is a video of the car attempting to move in a straight before any calibrations.

<iframe width="420" height="315"
src="https://www.youtube.com/embed/S-Qjl9JszjQ">
</iframe>

Finding the calibration factor involved setting the upper PWM limit to 255 and test the effective range above the lower limits to identify usable PWM values. The ratio between the ranges for both sides was calculated and applied during testing. The right wheels were set to a pwm value of 100, and calculations suggested the left wheels should be set to 110 to maintain a straight movement. However, after playing around with some values during physical testing, I realized that the left wheels required a pwm setting of 108 to ensure that the robot moved straight and not veering left. So, the calibration factor for the left wheel was adjusted to 1.08 and this factor was added to change_speed().

This videos show the result as the robot moves in a straight line for 6 feet.

<iframe width="420" height="315"
src="https://www.youtube.com/embed/PSpE31sPj2U">
</iframe>

### Open loop Control

Our last task was to execute an open loop control stunt. My stunt shows the robot's ability to move straight, veer left, veer right, spin. This sequence is open loop control, as it's preprogrammed commands without receiving any feedback from the system.

Here is the code for this control,

Here is a video

<iframe width="420" height="315"
src="https://www.youtube.com/embed/watch?v=PyoF2chNE4k">
</iframe>

