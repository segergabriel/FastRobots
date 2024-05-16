## Planning and Execution

### Lab Objective

After many hours of dedicated work in this course, the final lab (Lab 12) involves navigating our robot through a maze. The goal of this lab is to reach all nine waypoints using any effective method. The method I 
chose was localization and PID control. This allowed the robot to function autonomously and adjust its position. As the reader will see, I had to make some adjustments to my initial plan. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/lab11map.jpeg?raw=true" width="500" height="400">

### Planning and Design

As the car navigates the maze, it must perform three different actions. The first task is to localize the position of the robot. This is identical to the localization command I created in Lab 11. In this command, 
the car roatates slowly in a 360-degree circle and takes 18 readings. As it rotates, the car sends a series of distance measurements over Bluetooth. These measurements tells us about the robots belief of the postition, 
which we will use in the next steps. 

After we have localized, the task is to turn the car to the right angle. I'm using my orient PID control to turn the car to the desired value by tracking its angular position by integrating the angular velocity from the gyroscope and stop once the 
desired angle is reached. Exactly how this value is determined is shown below. 

pic of atan

After we succesfully turned the car so it faces the next waypoint, the task is to move it to the waypoint. 

To move the car, a function was created to stop the car after it has traveled the desired distance. So, a setpoint was calculated by subtracting the desired travel distance from the current frontal ToF sensor reading. 
When the new ToF sensor reading is less or equal than the initial reading, it indicates that the car has reached the postition. After determining the setpoint, the car behaves as in Lab 7, using a PID controller 
to reach the setpoint. Below I show how the desired distance is calculated depending on our values from the localization step. 

pic of derired pos

And below the function nextPoint is demonstrated which handles most of the logic for these tasks. It's also important that much of my design relies on previous implementations of PID controls and other 
functions that one can find in previous reports. However, these flags are activated from this function. 

pic of nextpoint func

Below I also included some of my logic from the main loop. 

pics of main 

### Jupyter Planning

We used our implementation from lab 11 and added logic to autonomously navigate the robot through the waypoints. My added Jupyter code is heavily influenced by our TA Rafi, but with some adjustments to fit my implementation. First, I defined 
an array of our waypoints, which are converted to meters. In the loop, the robot iterates through each waypoint, initializing its belief state and performing the rotation. The robot then updates its belief state based on the 
data. After that, I calculate the distance between the robot's current belief position and the goal waypoint, and if the robot is within a threshold distance of 0.31 meters, it updates the ground 
truth position and send a command to move to the next waypoint. This is handles via Bluetooth again and for this case I created a NEXT_POINT case. See my full implementation below. 

### Problems

I faced a lot of small problems while working on this lab, like most other students. Most of them were solved by debuggign or help from my class mates or TAs. One example was the handling of flags and not running multiple PIDs at once. A second
issue was the handling trasnfer of data. My issue here was first connected to using the belief of the robot, but this was solved by editing some of the files that were given to us and returning the belief. After that, I got some unreliable data for some 
points which confused the robots belief of its position. However, there was one issue I spent hours on and unfortunately I ran out of time and couldn't fix it. It had to do with how the values I sent over bluetooth was stored in my arduino code, 
currentY and goalY for example. 

picture of change in curr

As shown, the right values were transfered and could succesfully be used to calculate the angle the car should turn. The problem was that these values reset to zero after they were used in "step1" of nextPoint(), so when the desired position 
to travel were calculuted it always came out to zero as well. After getting helped and trying to debug this problem without any clear solution, I decided to hardcode the distance the car should travel for each waypoint. It worked pretty well but was frustrating as
I really wanted my robot to be fully able to navigate through the maze by itself. This also lead to complications for later waypoints. As if the localization and turning of the car wasn't exactly correct, it didn't have a chance to fix itself, as I in my code
moved on to driving the car for a set distance in the next step. I also noticed that depending on the battery, the car traveled a different amount. 

### Results

The first video shows my robot succesfullt localize itself and turning to the desired angle to point at the next waypoint. However, as one can see it doesn't move as it the desired position outputs zero. Therefore, it moves on and 
starts the localization process again. I wish I had more time to spend on this to fix this problem.

video 1

The second video shows oen of my runs with a hardcoded distance traveled. One exciting aspect about my result is that my robot is moving, calculating and transfering data fast. 
In the video one can see that the implementation works well until later on when the distance and the localization belief, unfortunately aren't accurate enough for the robot to make it 
to the last two waypoints. Another potential reason for this is that the gyroscope drifts over time, as mentioned in earlier labs. 


### Acknowledgements

I want to thank all the TAs in this course for all their hard work and help during this semester. I want to give extra thanks to Rafi, who was super helpful throughout the course and made sure to have extra lab hours multiple times a week. 
Finally, I want to thank Jonathan for a great semester and course!




