## LAB 4: Motors and open Loop Control

### Lab Objective

The goal of this lab was to transition the control of our car from manual to open loop. This involved first connecting the two motor drivers to the Artemis board and then programming the car to perform a sequence of movems.

### Prelab

First checked out sheets, link them

Two dual motor drivers were used to connect the motors to the Artemis and the pins had to be pwm capable in order to get input to them. The first motor was connected to pins 7 and 9 and the second one to pins 11 and 12. To integrate the motor drivers, the motor inputs and outputs were configured in parallel due to the utilization of only two inputs, despite four being available. 

Therefore, I soldered the driver's inputs and outputs together. We also had to decide on the power connection strategy for the motor drivers, given the battery only having two leads. The resolution solution was to run a wire from the VCC pad of one motor driver, soldering it in place, and then connecting a smaller wire from the VCC pad of the second driver to the main wire. The same strategy was applied for ground. See my schematic below. 

pic of schematic

We are using two 850mAh batteries, enabling us to power the Artemis and the motors independently to avoid transient effects. This is important because the changes in current drawn by the motors could potentially power off the Artemis, leading to a reset of the code when power is restored. I soldered one of the motor drivers according to the previously explained diagram. Finally, one of the input terminals of this motor driver was connected to an oscilloscope, along with its VCC and ground.

pic of connection to oscilliscope

### Power supply 

Given that the battery is supplying power to the motors at 3.7V, the power supply voltage was set to a range between 3.7V and 4.5V. To ensure there was no voltage drop when the motor was running, the current compliance of the power supply was adjusted. This adjustment resulted in a stable current compliance setting of approximately 2A.

### Testing motor drivers

The image below shows the oscilloscope readings when one of the motor driver inputs receives power. The is 3.15V, which is close to the expected 3.3V output from the GPIO pin. Additionally, the duty cycle has a ratio of approximately 195/256, indicating that the waveform remains mostly high throughout its period. The blue curve represents the positive output voltage to the motor while the yellow curve represents the negative output voltage to the motor. As expected, the yellow curve should be 0, while the blue curve shows the voltage values. 

pic of osci


In the next step, the dual motor driver was connected to one of the motors. The video below shows the motors being powered by the power supply. The wheels are programmed to rotate in alternating directions for 1 second each. The same thing was done for both sides independently.

video of spinning one side

The code below was used. 

code snippet for your analogWrite code that tests the motor drivers, left and right

Next, I connected the battery and executed the code for both motor drivers. I created a function to help with this. See below. 

pic of change speed func

I used this function to program both wheels to spin in both directions independently and together. See my code and the video of the wheels below. 

code of both wheels 

video of both wheels

### Assembly
Picture of all the components secured in the car
Consider labeling your picture if you can’t see all the components

### Lower limit PWM value

when doing control theory later on, the output values to the motors should not be below this lower pwm threshold.

The motor’s were then tested to understand its range. To do this, the lower limit value of the robot was tested. This was done by changing the PWM value until the wheels were able to start moving from a stationary position. This was done for both sides of the robot, with the left and right wheels having a lower limit value of 89 and 96, respectively:

The motors were tested to determine their operational range, focusing on finding the minimum pwm threshold required to initiate motion from a standstill. This is important as when doing control theory later, the output values to the motors should not be below this pwm threshold. This involved gradually adjusting the PWM values until the wheels started moving. This procedure showed that the wheels have minimum activation PWM values of 31.

The videos demonstrate that at a PWM value of 31, the car's wheels begin to beat friction, but they fully beat the kinetic friction at 35. Therefore, a PWM setting of 31 is sufficient to beat dynamic friction, while 35 is effective for overcoming static friction.

video of spinnin slow in air

video of beating fric on ground




Calibration demonstration (discussion, video, code, pictures as needed)
Open loop code and video


