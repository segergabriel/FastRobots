## LAB 4: Motors and open Loop Control

### Lab Objective

The goal of this lab was to transition the control of our car from manual to open loop. This involved first connecting the two motor drivers to the Artemis board and then programming the car to perform a sequence of movems.

### Prelab

First checked out sheets, link them

Two dual motor drivers were used to connect the motors to the Artemis and the pins had to be pwm capable in order to get input to them. The first motor was connected to pins 7 and 9 and the second one to pins 11 and 12. To integrate the motor drivers, the motor inputs and outputs were configured in parallel due to the utilization of only two inputs, despite four being available. 

Therefore, I soldered the driver's inputs and outputs together. We also had to decide on the power connection strategy for the motor drivers, given the battery only having two leads. The resolution solution was to run a wire from the VCC pad of one motor driver, soldering it in place, and then connecting a smaller wire from the VCC pad of the second driver to the main wire. The same strategy was applied for ground. See my schematic below. 

pic of schematic

We are using two 850mAh batteries, enabling us to power the Artemis and the motors independently to avoid transient effects. This is important because the changes in current drawn by the motors could potentially power off the Artemis, leading to a reset of the code when power is restored. I soldered one of the motor drivers according to the previously explained diagram. Then, I connected one of the input terminals of this motor driver to an oscilloscope, along with its VCC and ground.

pic of connection to oscilliscope

Power supply 

Given that the battery is supplying power to the motors at 3.7V, the power supply voltage was set to a range between 3.7V and 4.5V. To ensure there was no voltage drop when the motor was running, the current compliance of the power supply was adjusted. This adjustment resulted in a stable current compliance setting of approximately 2A.

The image below shows the oscilloscope readings when one of the motor driver inputs receives power. The is 3.15V, which is similiar to the expected 3.3V output from the GPIO pin. Additionally, the duty cycle has a ratio of approximately 195/256, indicating that the waveform remains mostly high throughout its period.

pic of osci

rewrite, The dual motor drive was then connected to one of the motors on the robot. The video of the motors powered by the power supply is shown below. In this video, the wheels were programmed to spin in either direction for 3 seconds each. 

video of spinning one side


combine with above Pin 12 was set to a duty cycle of 100, and the reading from the oscilloscope is shown below. Here, the blue curve represents the positive output voltage to the motor and the yellow curve denotes the negative output voltage to the motor. Because the code set one pin to 100 and the other to 0, the yellow curve should be essentially 0, while the other showed voltage values. The blue curve is shown as a square wave, which validates the motor driver, as there should be a constant voltage value at each increment in time.
