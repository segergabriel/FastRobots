## Lab 9: Mapping

### Lab Objective

The objective of this lab is to construct a two-dimensional map of a static environment. The "environment" in question is arranged in the lab's front room using cardboard boxes and wooden boards. The process involves manually positioning a robot at various predetermined locations, where it will perform a full rotation to collect distance measurements using a Time-of-Flight sensor. These measurements will be integrated to form a comprehensive map of the area. The final output will be a line-based map, which will be utilized in later experiments. 

### Overview 

The robot is already equipped with ToF sensors, an IMU, and motor drivers. So, from a hardware perspective, the robot is ready to execute all the required tasks for this lab. The work for this lab therefore involves software programming. The first task involves programming the robot to rotate in place with precise and small increments. Then, the robot will  record data from the ToF sensor along with the gyroscope's orientation at each of these increments. 

### Implementation

Because I have already implemented orientation control, controlling the robot's rotation through PID control simply involves gradually increasing the setpoint. To implement this new case was created and some slight modifications were made to the main loop, involving setting flags and calling the turn() function. Below is a code snippet that demonstrates how this is achieved. Note that I increment the angle by 10 degrees. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/9case.png?raw=true" width="530" height="330">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/9turn.png?raw=true" width="530" height="300">

The video below shows that my car is incrementing precisely and with good speed. It is also turning in place without much shifting and minimal oscillation or overshoot. So, my PID control and implementation is working well.

<iframe width="420" height="315"
src="https://www.youtube.com/embed/5bDaBuJj22E">
</iframe>

I am using similiar PID parameters as in lab 6 and 8. kp and kd will have lower values as the robot is supposed to turn much slower than it did in the other labs. The values are as follows:

kp = 20;
ki = 0.002;
kd = 10;

### Setup and Data Collection

The physical setup of the map is shown below.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images//IMG_7134.jpeg?raw=true)

The map is based on a grid, with each square tile being one grid cell. The robot will be collecting data in 5 designated spots, which are in feet: (5,-3), (5,3), (-3,-2), and (0,3) from the origin. The picture below shows one such spot.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/9spot.png?raw=true" width="420" height="300">

In order to collect data the robot takes a ToF measurement when the robot is at a set point as well as every time orientPid() is called. It also collects the current orientation from the IMU and repeats this until it has turned 360 degrees. Finally, I send the data over Bluetooth in the same way as previous labs. 

### Plotting Data and Merging Data

As I am using orientation PID control, I will need to trust the orientation from the integrated gyroscope values. Since the robot performs rotations in place and measures distances to walls, it is beneficial to do quick visual checks using polar plots. These plots provide a clear visual representation of the measurements taken at each designated spot. My car seem to rotate around 10 degrees too much but by looking at the plots I still believe that the map will come out accurate.  

The collected data comprises the orientation and the distance measurements, which are represented in polar coordinates (rho, phi). To convert the data into a global frame, this data first needs to be converted into Cartesian coordinates and then trasnformed accordingly. The following code snippet handles this.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9trans.png?raw=true)

And below is the polar plot images and the transformed plots in order for each location.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9polar1.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9trans1.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9polar2.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9trans2.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9trans3.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9trans4.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9polar5.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9trans5.png?raw=true)

Finally, I merged them together which reulted in the following map. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9map.png?raw=true)

The map looks good and correct, some data points that was collected far away aren't accurate but that is to be expected. 

### Line Based Map

In order to use this data in the simulator later, we needed to construct a line-based map. This was done by manually estimate the walls and obstacles based on our plots and drawing lines on the merged map. See my line based map below.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/9linemap.png?raw=true)

Finally, the list below contains the end points of these lines which were saved for later use.

| Description | Start coordinate (mm) |End coordinate (mm)|
| --- | --- | --- |
| South | (-1600, -1400) |(-200, -1400)|
| | (-200, -1400) |(-200, -1100)|
| | (-200, -1100) | (200, -1400) |
| | (200, -1100) | (200, -1400) |
| | (200, -1400) | (2000, -1400) |
| East | (2000, -1400) | (2000, 1400) |
| North | (-800, 1400) | (2000, 1400) |
| West | (-900, 1400) | (-800, 0) |
| | (-900, 0) | (-1600, 0) |
| | (-1600, 0) | (-1600, -1400) |
| Box | (1400, 0) | (1400, 500) |
| | (1400, 0) | (900, 0) |
| | (900, 0) | (900, 500) |
| | (900, 0) | (1400, 500) |




