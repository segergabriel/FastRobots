### Objective
The goal of this lab is to configure and be able to use an IMU sensor on our robot. It involved configuring the IMU sensor and accurately capturing its data. 

### Setup

The first thing we had to do was to connect the IMU to the artemis, this is shown below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2board.png?raw=true)

The software configuration for the IMU required the installation of the ICM_20948 library within the Arduino IDE, which allowed easy communication between the IMU on the Artemis platform. One important variable within the sample code is AD0_VAL, which represents the least significant bit of the IMU's I2C address. This value should be set to 0 only if the ADR jumper is open. Since the ADR jumper is closed for us, the AD0_VAL is set to 0. 

### Accelerometer
When printing the output, the accelerometer data presents the acceleration measurements for each coordinate axis. The IMU's accelerometer captures these accelerations in micro-G units. To determine the angles relative to the x-axis (roll) and y-axis (pitch), the arctangent function is utilized. The mathematical expressions are shown below.

Roll = atan2(a_x, a_z)

Pitch = atan2(a_y, a_z)

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2acccode.png?raw=true)

Employing these equations, the values for pitch and roll were computed at each interval and graphically represented using the serial plotter. It was observed that upon rotating the pitch or roll to 90 degrees, the other one became noisy. For instance, during the initial rotation where the pitch approached 90 degrees, the roll measurement saw lots of noise and did not remain at 0.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2accflat.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2accnoflat.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2accoutflat.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2accout90.png?raw=true)

The accelerometer has high accuracy when coupled with a low pass filter, although it displays a slight deviation from the mean. Below you can see expected versus actual values for pitch and roll.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2exppitch.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2exproll.png?raw=true)

Below is the collected data and FFT with no large noise source around.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2fft.png?raw=true)

Below is the code that was used for the low-pass filter. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2lpfcode.png?raw=true)

I believe this is due to the low magnitude of noise since the IMU already has a low pass filter according to its datasheet

### Gyroscope
The gyroscope uses the change in angle around each axis with this equation.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroeq.png?raw=true)

And here you can see the code that was used. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyrocode.png?raw=true)

To minimize noise in the accelerometer data, it was necessary to incorporate gyroscope readings. However, As evident from the graph below, there is a noticeable drift in the gyroscope data over time, with the delay being set to 10 seconds.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyrodrift.png?raw=true)

Here is the outputs from the serial plotter when it's held flat and when lifted. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroout2.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroplot.png?raw=true)


### Complimentary Filter
To address the issue of noise and drift from the accelerometer and the gyroscope, a complimentary filter was implemented. This is demonstrated in the code below. This filter uses a weighting function, defined by the variable 'alpha', to mitigate noise from the accelerometer and drifting from the gyroscope. The optimal value of 'alpha' was established through a series of tests and it was determined to be 0.1.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2comp.png?raw=true)

### Sample Data
After eliminating all delay and print statements, and by storing time, accelerometer, and gyroscope data, the sampling rate became very fast. The average was around 4ms. This could result in an excessive volume of data collected in a brief duration. Such a large dataset could quickly consume the Artemis' storage capacity and make it challenging to analyze the robot's long-term behavior. To manage this, the data was stored into different string arrays before being trannsmitted to my laptop. Note that the IMU values are int16_t and 100 values for each array was enough. This was about 10000Kb and with the 384kB internal data, the artemis has plenty of memory for data collection. See my implementation below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2sample.png?raw=true)
 
In addition to the data management, I had to combine two files in order to be able to transfer data. I created a header file for the IMU sensor file and included that in the ble file, where I then ran my the program. 
