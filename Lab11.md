## Lab 11: Localization (Real)

### Lab Objective

In Lab 10, I demonstrated the effectiveness of the Bayes filter in simulation. In this lab, I will perform localization with the Bayes filter on the actual robot. Only the *update step* of the Bayes filter will be used, as the motion is very noisy and therefore the prediction step will not be helpful.

### Bayes Filter Simulation

Three files were provided to us to help accomplish this lab: [lab11_sim.ipynb](https://github.com/CEI-lab/FastRobots-lab11/blob/main/lab11_sim.ipynb), [lab11_real.ipynb](https://github.com/CEI-lab/FastRobots-lab11/blob/main/lab11_real.ipynb), and [localization_extras.py](https://github.com/CEI-lab/FastRobots-lab11/blob/main/localization_extras.py).  
- The `lab11_sim.ipynb` file is a Jupyter notebook that provides skeleton code to integrate the real robot with the localization code.  
- The `lab11_real.ipynb` file is a Jupyter notebook that demonstrates the Bayes filter implementation on the virtual robot.  
- `localization_extras.py` provides a fully functional localization module that works on the virtual robot.  

Our first task was to test localization in simulation again, but this time with the code given to us instead of our own implementation. I did this by running the simulation notebook, and the result is shown below:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/10sim.png?raw=true" alt="Simulation result" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### Bayes Filter (Real)

We were given an optimized Bayes filter, which was used in Python for the localization process. The primary objective was to enable the robot to rotate precisely on its axis and gather ToF readings. This task is similar to Lab 9, where the focus was to map the environment. Because the PID controller was already optimized for rotating and gathering readings, I reused that code.

We had to implement this within the *perform_observation_loop()* function, which is part of the *RealRobot()* class. When this function is called, the robot rotates a full 360°, outputting a NumPy array of ToF distance values and angular position values. I modified my notification handler from previous labs and used that to interpret the Bluetooth values. Note that I also used the *asyncio* function so that the script waits for the robot to finish its spin before proceeding.  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11code.png?raw=true" alt="Code implementation" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11not.png?raw=true" alt="Notification handler" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

This collects 18 ToF sensor readings, beginning with the robot at 0 degrees, and rotating in increments of 20 degrees to 340 degrees. In Lab 9, I collected as many data points as possible, but the `world.yaml` helper file expects 18 data points, so this part was modified for this task. By rotating the robot exactly 20° and stopping it to take a distance measurement before rotating again, the data collection was very precise, which is important for accurate localization.

### Results

My results indicate that my robot has a very high confidence in its updated position, as the probability that the robot is at its belief is nearly 1 for all locations. The predicted locations from the update step also match well with the actual ground truth of the robot. All of the localization results accurately determined the *x* coordinate. However, some of the locations were off by one foot in the *y* direction. The blue point represents the belief of the robot and the red point represents the ground truth. Also note that the robot thinks it is offset by around 10°, which is accurate since my robot still rotates about 10° too much. My intention is to fix this before Lab 12 in order to successfully navigate through the map.

**Location: (-3, -2)**  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot1.png?raw=true" alt="Location -3,-2 photo" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data1.png?raw=true" alt="Location -3,-2 data" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

**Location: (5, -3)**  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot2.png?raw=true" alt="Location 5,-3 photo" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data2.png?raw=true" alt="Location 5,-3 data" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

**Location: (5, 3)**  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot3.png?raw=true" alt="Location 5,3 photo" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data3.png?raw=true" alt="Location 5,3 data" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

**Location: (0, 3)**  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11spot4.png?raw=true" alt="Location 0,3 photo" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11data4.png?raw=true" alt="Location 0,3 data" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

**Location: (0, 0)**  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11orig.png?raw=true" alt="Location 0,0 photo" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/11dataori.png?raw=true" alt="Location 0,0 data" style="max-width:500px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---
<div class="nav-buttons" style="display:flex; justify-content:space-between; gap:12px; margin-top:2em;">
  <a href="./Lab10.html" class="nav-btn">← Prev Lab</a>
  <a href="./Lab12.html" class="nav-btn">Next Lab →</a>
</div>
---
