## Lab 12: Planning and Execution

### Lab Objective

After many hours of dedicated work in this course, the final lab (Lab 12) involves navigating the robot through a maze. The goal of this lab is to reach all nine waypoints using any effective method. The first method I chose was localization and PID control. This would allow the robot to function autonomously and adjust its position. As the reader will see, I had to make some adjustments to my initial plan. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/IMG_7166.jpeg?raw=true" width="500" height="400">

### Planning and Design

As the car navigates the maze, it must perform three different actions. The first task is to localize the position of the robot. This is identical to the localization process that I created in Lab 11. For this task, the car roatates slowly in a 360-degree circle and takes 18 readings. As it rotates, the car sends a series of distance measurements over Bluetooth. These measurements tells us about the robots belief of the postition, which we will use in the next steps. 

After we have localized the position, the task is to turn the car to the right angle. I'm using my orient PID control to turn the car to the desired value by tracking its angular position by integrating the angular velocity from the gyroscope and stop once the 
desired angle is reached. Exactly how this value is calculated is shown below. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12angle.png?raw=true" width="500" height="40">


After we succesfully turned the car so it faces the next waypoint, the next task is to move it forward to the waypoint. 

To move the car, a function was created to stop the car after it has traveled the desired distance. So, a setpoint was calculated by subtracting the desired travel distance from the current frontal ToF sensor reading. When the new ToF sensor reading is less or equal to the initial reading, it indicates that the car has reached the desired postition. After determining the setpoint, the car behaves similar as in Lab 7, using a PID controller to reach the setpoint. Below I show how the desired distance is calculated depending on the values transfered from the localization step. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12des.png?raw=true" width="650" height="30">

And below the function nextPoint() is demonstrated, which handles most of the logic for these three tasks. It's also important to note that much of my design relies on previous implementations of PID controllers and functions from previous labs. However, these flags and functions are activated or called from this function. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12next.png?raw=true" width="500" height="300">

Below I also included some logic from the main loop. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12pid.png?raw=true" width="300" height="100">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12orient.png?raw=true" width="500" height="500">

### Jupyter Planning

I used my implementation from lab 11 and added logic to it to autonomously navigate the robot through the waypoints. My added Jupyter code is heavily influenced by our TA Rafi, but with some adjustments to fit the rest of my code. First, I defined an array of our waypoints, which are converted to meters. In the loop, the robot iterates through each waypoint, initializing its belief state and performing the rotation. The robot then updates its belief state based on the data. After that, I calculate the distance between the robot's current belief position and the goal waypoint, and if the robot is within a threshold distance of 0.31 meters, it updates the ground truth position and send a command to move to the next waypoint. This is handled via Bluetooth again and for this case I created a NEXT_POINT case. See my full implementation below. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12jup.png?raw=true" width="600" height="300">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12jup2.png?raw=true" width="630" height="500">

### Problems

I faced a lot of small problems while working on this lab, like most other students. One example was the handling of flags and not running multiple PIDs at once. A second issue was the handling of the transfer of data. My issue here was first connected to using the belief of the robot, but this was solved by editing some of the files that were given to us and returning the belief. I also got some unreliable data for some points which confused the robots belief of its position. However, there was one issue I spent hours on and unfortunately I ran out of time and couldn't fix it. It had to do with how the values I sent over bluetooth was stored in my arduino code, currentY and goalY for example. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/12curY.png?raw=true" width="250" height="70">

As shown, the right values were transfered and could succesfully be used to calculate the angle the car should turn. The problem was that these values reset to zero after they were used in "step1" of nextPoint(), so when the desired position to travel were calculuted it always came out to zero as well. After getting helped and trying to debug this problem without any clear solution, I decided to hardcode the distance the car should travel for each waypoint. It worked pretty well but it was frustrating as
I really wanted my robot to be fully able to navigate through the maze by itself. This also lead to complications for later waypoints. For example, if the localization and turning of the car wasn't exactly correct, it didn't have a chance to fix itself, as my code moved on to driving the car for a set distance in the following step. I also noticed that depending on the battery, the car traveled a different distance. 

### Results

The first video shows my robot succesfully localize itself and turning to the desired angle to point at the next waypoint. However, as one can see, it doesn't move as its desired position outputs zero. Therefore, it moves on and starts the localization process again. I wish I had more time to spend on this to fix this problem.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ztlRd8qHEGc" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

The second video shows one of my runs with a hardcoded distance traveled. One exciting aspect about my result is that my robot is moving, calculating and transfering data fast. 
In the video one can see that the implementation works well until later on when the distance and the localization belief, unfortunately aren't accurate enough for the robot to make it to the last two waypoints. Another potential reason for this is that the gyroscope drifts over time, as mentioned in earlier labs. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/Q9PnoNT76YM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Acknowledgements

I want to thank all the TAs in this course for all their hard work and help during this semester. I want to give extra thanks to Rafi, who was super helpful throughout the course and made sure to have extra lab hours open multiple times a week. 
Finally, I want to thank Jonathan for a great semester and course!




