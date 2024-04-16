## Lab 8: Stunts

### Lab Objective

The purpose of this lab is to combine the work done in previous labs to control the robot in a high speed stunt. For the the controlled stunt, I chose to do orientation task shich includes turning the car.

### Task B: Orientation Control

For this lab, I was tasked with turning the robot at a specified distance from the wall. More specifically, the goal was to drive the robot forward as fast as possible starting at a distance of less than 4 m from the wall. Then, at a distance of 3 feet the robot will turn 180 degrees and head back to the starting line. There are two key components in successfully completing this lab. First one is to quickly and correctly estimate the distance from the wall such that the robot initiates the turn on time, and secondly to make the 180 degree turn correctly and quickly.

I'm not using the Kalman filter to estimate the distance but I am using linear interpolation which helps the robot to estimate the distance to the waller quicker than if I only used the ToF sensor readings.

### Implementation

The task is divided into four different sections.

1. Drive fast forward until the robot is 3 feet from the wall.
2. Quickly turn the car 180 degrees.
3. Drive back to the starting line.
4. Send data over Bluetooth.

To accomplish this a new case "STUNT" was created, see this below.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/8stunt.png?raw=true)

Below is the logic of my main loop as well as the "doStunt" function. These were the two main components of the implementation. Additional functions worked as helper functions. Notice that "doStunt" is getting called right away from the loop. When the desired position is reached, it calls the reverse funciton which activates my orientation PID. Finally, the car accelerates back from where it came from. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/8loop.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/8dostunt.png?raw=true)

Two main challenges arose when completing this lab. The first one was to set flagsa at the right time and make sure different functions and cases didn't block each other. One example is when I tried to turn the car I had some issues. Most of these were solved by creating a "STUNT_RUN" flag and setting it to false when I needed to use the orientation control. The second issue involved in getting the car to turn exactly 180 degrees and do so fast. Because I wanted minimum delay the car span a little bit even after it was supposed to be done turning. This was solved by testing the right delay times and having it stop turn a little before reaching 180 degrees. 

### Results

The videos below shows that my stunt is working well. The robot travels fast and initiates the turn at the right distance before quickly driving back to the starting line. 

videos

And here is the data for the angles that were collected.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/8angle1.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/8angle2.png?raw=true)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/8angle3.png?raw=true)
