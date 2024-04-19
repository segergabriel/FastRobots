### Mapping

### Lab Objective

The objective of this lab is to construct a two-dimensional map of a static environment. The "environment" in question is arranged in the lab's front room using cardboard boxes and wooden boards. The process involves manually positioning a robot at various predetermined locations, where it will perform a full rotation to collect distance measurements using a Time-of-Flight sensor. These measurements will be integrated to form a comprehensive map of the area. The final output will be a line-based map, which will be utilized in later experiments. 

### Overview 

The robot is already equipped with ToF sensors, an IMU, and motor drivers. So, from a hardware perspective, the robot is ready to execute all the required tasks for this lab. The work for this lab therefore involves software programming. The first task involves programming the robot to rotate in place with precise and small increments. Then, the robot will  record data from the ToF sensor along with the gyroscope's orientation at each of these increments. 

### Implementation

Because I have already implemented orientation control, controlling the robot's rotation through PID control simply involves gradually increasing the setpoint. Below is a code snippet that demonstrates how this is achieved.

pic of ovrview code, loop case and turn 

The video below shows that my car is incrementing precisely and with good speed. It is also turning in place without much shifting and minimal oscillation or overshoot. So, my PID control and implementation is working well.

video of car turning 

I am using similiar PID parameters as in lab 6 and 8. kp and kd have lower values as robot is supposed to turn much slower than that in the other labs. They are as follows:

kp = 20;
ki = 0.002;
kd = 10;

### Setup and Data Collection

The physical setup of the "room" is shown below. I labeled the map to indicate the position and coordinates of each spot. 

pic of room with labels

The map is based on a grid, with each square tile being one grid cell. The robot will be collecting data in 5 designated spots, which are (in feet): (5,-3), (5,3), (-3,-2), and (0,3) from the origin. The picture below shows one such spot with the coordinate.

pic of one spot

In order to collect data the robot takes a ToF measurement when the robot is at a set point as well as every time orientPid() is called. It also collects the current orientation from the IMU. It repeats this until it has turned over 360 degrees. Finally, I send the data over Bluetooth in the same way as previous labs. 

### Plotting Data

As I am using orientation PID control, I will need to trust the orientation from the integrated gyroscope values. Since the robot performs rotations in place and measures distances to walls, it is beneficial to do quick visual checks using polar plots. These plots provide a clear visual representation of the measurements taken at each designated spot. Below is the polar plot images for each location.

pic of each polar plot

### Merging Data

The collected data comprises the orientation and the distance measurements, which are represented in polar coordinates (rho, phi). To convert the data into a global frame, this data needs to be converted into Cartesian coordinates. The following code snippet handles this.

pic of conversion

Below are the cartesian plots for each spot. 

pic of cartesian cords

And finally I merged them together which created the following map: maybe talk ab how this was done

pic of merged map

### Line Based Map

In order to use this data in the simulator later, we needed to construct a line-based map. This was done by manually estimate the walls and obstacles based on our plots and drawing lines on the merged map. See this map below.

pic of line based

Finally,two lists containing the end points of these lines were saved. See these below. 






