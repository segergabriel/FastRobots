## Lab 2: IMU

### Objective
The goal of this lab is to configure the IMU sensor on our robot and accurately capture its data.  

### Setup
The first thing we had to do was connect the IMU to the Artemis, as shown below.  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2board.png?raw=true" alt="IMU connected to Artemis" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

The software configuration for the IMU required the installation of the **ICM_20948** library within the Arduino IDE, which allowed easy communication between the IMU and the Artemis platform. One important variable within the sample code is **AD0_VAL**, which represents the least significant bit of the IMU's I2C address. This value should be set to **0 only if the ADR jumper is open**. Since the ADR jumper is **closed** for us, **AD0_VAL is set to 1**.  

---

### Accelerometer
When printing the output, the accelerometer data presents the acceleration measurements for each coordinate axis. The IMU's accelerometer captures these accelerations in micro-G units. To determine the angles relative to the x-axis (roll) and y-axis (pitch), the arctangent function is utilized:

**Roll = atan2(a_x, a_z)**  
**Pitch = atan2(a_y, a_z)**  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2acccode.png?raw=true" alt="Accelerometer code snippet" style="max-width:480px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Employing these equations, the values for pitch and roll were computed at each interval and graphically represented using the serial plotter. It was observed that upon rotating the pitch or roll to 90 degrees, the other one became noisy. For instance, during the initial rotation where the pitch approached 90 degrees, the roll measurement saw lots of noise and did not remain at 0.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2accflat.png?raw=true" alt="Accelerometer output flat" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2accnoflat.png?raw=true" alt="Accelerometer output not flat" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2accoutflat.png?raw=true" alt="Plot: flat" style="max-width:360px; width:100%; border-radius:8px; margin:1em auto; display:block;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2accout90.png?raw=true" alt="Plot: ~90 degrees" style="max-width:360px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

The accelerometer has high accuracy when coupled with a low-pass filter, although it displays a slight deviation from the mean. Below you can see expected versus actual values for pitch and roll.  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2exppitch.png?raw=true" alt="Expected vs actual pitch" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2exproll.png?raw=true" alt="Expected vs actual roll" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Below is the collected data and FFT with no large noise source around.  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2fft.png?raw=true" alt="FFT of accelerometer data" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Below is the code that was used for the low-pass filter.  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2lpfcode.png?raw=true" alt="Low-pass filter code" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

I believe this is due to the low magnitude of noise, since the IMU already has a low-pass filter according to its datasheet.

---

### Gyroscope
The gyroscope uses the change in angle around each axis with this equation:

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2gyroeq.png?raw=true" alt="Gyroscope equation" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Here you can see the code that was used:  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2gyrocode.png?raw=true" alt="Gyroscope code snippet" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

To minimize noise in the accelerometer data, it was necessary to incorporate gyroscope readings. However, as evident from the graph below, there is a noticeable drift in the gyroscope data over time, with the delay being set to 10 seconds.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2gyrodrift.png?raw=true" alt="Gyro drift over time" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

Here are the outputs from the serial plotter when it's held flat and when lifted:  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2gyroout2.png?raw=true" alt="Serial plotter output flat" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2gyroplot.png?raw=true" alt="Serial plotter output lifted" style="max-width:700px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

### Complementary Filter
To address the issue of noise and drift from the accelerometer and the gyroscope, a complementary filter was implemented. This is demonstrated in the code below. This filter uses a weighting function, defined by the variable **alpha**, to mitigate noise from the accelerometer and drifting from the gyroscope. The optimal value of **alpha** was established through a series of tests and was determined to be **0.1**.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2comp.png?raw=true" alt="Complementary filter code snippet" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

---

### Sample Data
After eliminating all delay and print statements, and by storing time, accelerometer, and gyroscope data, the sampling rate became very fast. The average was around **4 ms**. This could result in an excessive volume of data collected in a brief duration. Such a large dataset could quickly consume the Artemis' storage capacity and make it challenging to analyze the robot's long-term behavior.  

To manage this, the data was stored in different string arrays before being transmitted to my laptop. Note that the IMU values are `int16_t`, and **100 values for each array** was enough. This was about **~10 KB**, and with the **384 KB** internal data, the Artemis has plenty of memory for data collection. See my implementation below:  

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/2sample.png?raw=true" alt="Sampling implementation code" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

To enable data transfer, I combined two files by creating a header file for the IMU sensor and including it in the BLE file, where I then executed the program.
