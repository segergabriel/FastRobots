## LAB 4: Motors and Open Loop Control

### Lab Objective
The goal of this lab is to transition the control of our car from manual to open loop. This involved connecting the two motor drivers to the Artemis board and programming the car to perform a sequence of moves.

### Prelab
First I read through the datasheet to understand the motors that we are working with. One can see it here: [Datasheet](https://www.ti.com/general/docs/suppproductinfo.tsp?distId=10&gotoUrl=https%3A%2F%2Fwww.ti.com%2Flit%2Fgpn%2Fdrv8833)

Two dual motor drivers were then used to connect the motors to the Artemis and the pins had to be PWM-capable in order to get input to them. The first motor was connected to pins 7 and 9 and the second one to pins 11 and 12. To integrate the motor drivers, the motor inputs and outputs were configured in parallel due to the utilization of only two inputs, despite four being available.

Therefore, I soldered the driver's inputs and outputs together. We also had to decide on the power connection strategy for the motor drivers, given that the battery only has two leads. The solution was to run a wire from the VCC pad of one motor driver, soldering it in place, and then connecting a smaller wire from the VCC pad of the second driver to the main wire. The same strategy was applied for ground. See my schematic below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/IMG_6939.jpeg?raw=true" alt="Motor driver power wiring schematic" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

We are using two 850mAh batteries, enabling us to power the Artemis and the motors independently to avoid transient effects. This is important because the changes in current drawn by the motors could potentially power off the Artemis, leading to a reset of the code when power is restored. I soldered one of the motor drivers according to the schematic above. Finally, one of the input terminals of this motor driver was connected to an oscilloscope, along with its VCC and ground. See my connection below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/4osci.png?raw=true" alt="Oscilloscope probe connections to motor driver input" style="max-width:420px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### Power supply
Given that the battery is supplying power to the motors at 3.7V, the power supply voltage was set to a range between 3.7V and 4.5V. To ensure there was no voltage drop when the motor was running, the current compliance of the power supply was adjusted. This resulted in a stable current compliance setting of approximately 2A.

### Testing motor drivers
The image below shows the oscilloscope readings when one of the motor driver inputs receives power. The voltage is 3.15V, which is close to the expected 3.3V output from the GPIO pin. Additionally, the duty cycle has a ratio of approximately 195/256, indicating that the waveform remains mostly high throughout its period. The blue curve represents the positive output voltage to the motor while the yellow curve represents the negative output voltage to the motor. As expected, the yellow curve should be 0, while the blue curve shows the voltage values.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/4osciout.png?raw=true" alt="Oscilloscope output of motor driver signals" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

In the next step, one dual motor driver was connected to one of the motors. The video below shows the motors being powered by the power supply while rotating in alternating directions for 1 second each. The same thing was done for both sides of the car independently. For this step and for the rest of the lab, we used the `analogWrite` function to set the PWM values. The higher the value, the faster the wheels will spin and therefore, the faster the car will move.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/2yOHqZwpEz0" frameborder="0" allowfullscreen></iframe>
</div>

The code that was used for this task is shown below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/4onewheel.png?raw=true" alt="Code used to run a single wheel forward/backward" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Next, I connected the battery and executed the code for both motor drivers at the same time. I created a function, `new_speed`, to help with this. See my implementation and the video of both wheels spinning below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/4newspeed.png?raw=true" alt="new_speed function implementation" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/9mbQQSKdTAk" frameborder="0" allowfullscreen></iframe>
</div>

### Assembly
The picture below demonstrates my current assembly. Most sensors are put in place, but for this lab I'm keeping the motor drivers and the Artemis free in case I need to move them around or adjust them. I'm planning to attach them to the car using double-sided tape after the completion of this lab.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/IMG_6938.jpeg?raw=true" alt="Current robot assembly layout" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### Lower limit PWM value
The motors were then tested to understand their range. To do this, the lower limit value of the robot was tested by changing the PWM value until the wheels were able to start moving from a stationary position. This was done for both sides of the robot, with the left and right wheels having a lower limit value of 89 and 96, respectively.

The motors were also tested to determine their operational range, focusing on finding the minimum PWM threshold required to initiate motion from a standstill. This is important because when doing control later, the output values to the motors should not be below this PWM threshold. This involved gradually adjusting the PWM values until the wheels started moving. This procedure showed that the wheels have minimum activation PWM values of 31.

The videos demonstrate that at a PWM value of 31, the car's wheels begin to spin when held up in the air but do not beat friction on the ground. They fully beat the kinetic friction at 35 as the car starts moving. Therefore, a PWM setting of 31 is sufficient to beat dynamic friction, while 35 is effective for overcoming static friction.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/fN-5_G622G8" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/PyoF2chNE4k" frameborder="0" allowfullscreen></iframe>
</div>

### Calibration
Due to the motors producing different torque outputs, a calibration factor was necessary to ensure that both sides of the car moved equally, allowing the robot to move in a straight line.

Here is a video of the car attempting to move in a straight line before any calibrations.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/S-Qjl9JszjQ" frameborder="0" allowfullscreen></iframe>
</div>

Finding the calibration factor involved setting the upper PWM limit to 255 and testing the effective range above the lower limits to identify usable PWM values. The ratio between the ranges for both sides was calculated and applied during testing. The right wheels were set to a PWM value of 100, and calculations suggested the left wheels should be set to 110 to maintain straight movement. However, after testing, I realized that the left wheels required a PWM setting of 108 to ensure that the robot moved straight and did not veer left. So, the calibration factor for the left wheel was adjusted to **1.08**, and this factor was added to `change_speed()`.

These videos show the result as the robot moves in a straight line for 6 feet.

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/PSpE31sPj2U" frameborder="0" allowfullscreen></iframe>
</div>

### Open loop Control
Our last task was to execute an open loop control stunt. My stunt shows the robot's ability to move straight, back up, veer left, veer right and then spin. This sequence is open loop control, as it's preprogrammed commands without receiving any feedback from the system.

Here is the code for these commands and the video that displays the car executing these moves.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/4stunt.png?raw=true" alt="Open loop stunt command sequence code" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/3ld7t3fYGeA" frameborder="0" allowfullscreen></iframe>
</div>

