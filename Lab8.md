## Lab 8: Stunts

### Lab Objective
The purpose of this lab is to combine the work done in previous labs to control the robot in a high-speed stunt. For the controlled stunt, I chose to do an orientation task which involves turning the car quickly and driving back to where it came from.

### Task B: Orientation Control
For this lab, I was tasked with turning the robot at a specified distance from the wall. More specifically, the goal was to drive the robot forward as fast as possible starting at a distance of less than 4 m from the wall. Then, at a distance of 3 feet, the robot will turn 180 degrees and head back to the starting line.  

There are two key components in successfully completing this lab:  
1. Quickly and correctly estimating the distance from the wall such that the robot initiates the turn on time.  
2. Making the 180-degree turn correctly and quickly.  

I am not using the Kalman Filter to estimate the distance, but I am using linear interpolation which helps the robot estimate the distance to the wall quicker than if I only used the ToF sensor readings.

---

### Implementation
The task is divided into four different sections:

1. Drive forward quickly until the robot is 3 feet from the wall.  
2. Quickly turn the car 180 degrees.  
3. Drive back to the starting line.  
4. Send data over Bluetooth.  

To accomplish this, a new case `"STUNT"` was created:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/8stunt.png?raw=true" alt="Stunt case" style="max-width:480px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Below is the logic of my main loop as well as the `doStunt` function. These were the two main components of the implementation. Additional functions worked as helper functions. Notice that `doStunt` is called right away from the loop. When the desired position is reached, it calls the reverse function, which activates my orientation PID. Finally, the car accelerates back from where it came from.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/8loop.png?raw=true" alt="Main loop" style="max-width:460px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/8dostunt.png?raw=true" alt="doStunt function" style="max-width:460px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Two main challenges arose when completing this lab.  
- The first was setting flags at the right time and making sure different functions and cases didn't block each other. For example, when I tried to turn the car, I had some issues. Most of these were solved by creating a `"STUNT_RUN"` flag and setting it to false when I needed to use orientation control.  
- The second issue was getting the car to turn exactly 180 degrees, and to do so quickly. Because I wanted minimal delay, the car spun slightly after it was supposed to be done turning. This was solved by testing the right delay times and having it stop turning a little before reaching 180 degrees.  

---

### Results
The videos below show that my stunt is working well. The robot travels fast, initiates the turn at the right distance, and then quickly drives back to the starting line.  

<div style="text-align:center;">
  <iframe width="420" height="315" src="https://www.youtube.com/embed/aCwjjnbXuHA" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <iframe width="420" height="315" src="https://www.youtube.com/embed/k-J3iqWiaH4" frameborder="0" allowfullscreen></iframe>
</div>

<div style="text-align:center;">
  <iframe width="420" height="315" src="https://www.youtube.com/embed/yXWx4O4lnY4" frameborder="0" allowfullscreen></iframe>
</div>

And here is the collected data of the change in angle over the run:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/8angle1.png?raw=true" alt="Angle graph 1" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/8angle2.png?raw=true" alt="Angle graph 2" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/8angle3.png?raw=true" alt="Angle graph 3" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

<div class="nav-buttons" style="display:flex; justify-content:space-between; gap:12px; margin-top:2em;">
  <a href="./Lab7.html" class="nav-btn">← Prev Lab</a>
  <a href="./Lab9.html" class="nav-btn">Next Lab →</a>
</div>
