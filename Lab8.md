## Lab 8: Stunts

### Lab Objective

The purpose of this lab is to combine the work done in previous labs to control the robot in a high speed stunt. For the the controlled stunt, I chose to do orientation task shich includes turning the car.

### Task B: Orientation Control

For this lab, I was tasked with turning the robot at a specified distance from the wall. More specifically, the goal was to drive the robot forward as fast as possible starting at a distance of less than 4 m from the wall. Then, at a distance of 3 feet the robot will turn 180 degrees and head back to the starting line. There are two key components in successfully completing this lab. First one is to quickly and correctly estimate the distance from the wall such that the robot initiates the turn on time, and secondly to make the 180 degree turn correctly and quickly.

I'm not using the Kalman filter to estimate the distance but I am using linear interpolation which helps the robot to estimate the distance to the waller quicker than if I only used the ToF sensor readings.

### Implementation

The task is divided ito different sections.

1. Drive fast forward until 3 feet from the wall.
2. Quickly turn the car 180 degrees.
3. Drive back to the starting line.
4. Send data.

To accomplish this a new task "STUNT" was created, see below.

pic of Stunt

Flags are still being used 

pic of dostunt
pic of main loop when done

### Results

The videos below shows that my stunt is working well. The robot travels fast and initiates the turn at the right distance before driving back to the starting line. 

videos

And here are the data that was collected. tof and orientation
