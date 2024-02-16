# ECE4160 Fast Robots
## Lab 1: The Artemis and Bluetooth

### Part 1) Objective
The objective of this section of the lab is to familiarize myself with the Arduino IDE and the Artemis board. After Completing this lab, I will be able to program the board, utilizing the board LED, managing serial communications over USB, and operating the onboard temperature sensor and the microphone.

#### Prelab
I Installed the Arduino IDE on my computer and made sure that I had the latest versions of ArduinoIDE and Sparkfun Appollo3 support software. 

#### Blink it up
To evaluate the functionality of the LED on the Artemis Board, the "Blink it Up" sample code was employed. This script was designed to activate the LED for a set duration, followed by a phase where the LED is deactivated for another set period. As demonstrated in the video, this resulted in the LED being turned on for 2 seconds and then turned off for 1 second.
<iframe width="420" height="315"
src="https://www.youtube.com/embed/J3cUdux_d7M">
</iframe>

#### Serial Example
For this example, the goal was to evaluate the performance of the serial monitor output. During this test, the Arduino was programmed to transmit a numerical sequence to the serial monitor. Additionally, it was set up to enable user interaction, where the user could input data into the serial monitor. The Artemis Board was then tasked with reading this input and displaying it back onto the serial monitor. The process and its results are illustrated in the image below.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/serialExample.png?raw=true)

#### Analogread Example
In the third experiment, the focus was on the temperature sensor. The temperature readings were relayed to the serial monitor in millidegrees Celsius. To verify the accuracy, I quickly placed my hand over the Artemis board, which resulted in a slight temperature rise, as indicated by the readings changing from 31.95 to 31.99 degrees Celsius.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/analog.png?raw=true)

##### Microphone Output Example
In this example, we wanted ot find the loudest frequency using the microphone. The video below show the results. 
<iframe width="420" height="315"
src="https://www.youtube.com/embed/YdRLgCws2fE">
</iframe>

### Part 2) Objective
The objective of this laboratory was to establish a Bluetooth connection between the Artemis board and my laptop. The task involved integrating Python code with the Artemis board through Arduino programming. The purpose of this setup was to facilitate the transmission of data via Bluetooth, which will be utilized in upcoming lab exercises.

#### Prelab
The bluetooth software used in this course was compatible with my mac. So, it was easy to set up a virtual environment. I installed Python and Jupyter Notebook within this environment, enabling the Artemis board to connect easily to the Python code on my Mac.

#### Configurations
Next, I connected the Artemis Board to the Python code. This was achieved by uploading the provided ble_arduino.ino file to the Artemis, which resulted in the MAC address being updated to c0:89:25:6d:b0:4b, as depicted in the figure below.
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/advert1.png?raw=true)

I also modified the MAC address in the Python script within the connection.yaml file, as shown below. This modification was important for enabling the Python code to detect and establish a connection with the Artemis. The second step involved the use of a unique UUID address. Given that multiple Artemis boards in the course had identical MAC addresses, it was possible for the Python code to inadvertently connect to the wrong board. Therefore, I generated a unique UUID in Python, which I then incorporated into the ble_service field in the connection.yaml file and the BLE_UUID_TEST_SERVICE in the Arduino code.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/jupuuid.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/arduuid.png?raw=true)

#### Echo
To verify the connection between the Artemis board and the computer, I executed some commands, starting with the ECHO command. This command involved sending a string of characters from the Python code to the Artemis board, which then relayed the same string back to the Python code. To facilitate this, I integrated the ECHO command into the Arduino code within the ble_arduino.ino file, where 'echo' was appended to the array of command types. The code for ECHO is shown below

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/echoarduino.png?raw=true)

The following command is used in Python:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/echojup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/advert.png?raw=true)

#### GET_TIME_MILLIS
The next command focused on obtaining the current time from the Artemis board. This required utilizing the millis() function from the Arduino library. The value obtained from millis() was then converted into a double data type, and then formatted into a string. This string representation of the current time was then transmitted to the Python script. The relevant segment of the Arduino code is below:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/getmillisarduino.png?raw=true)

And below is the command in Python.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/getmillisjup.png?raw=true)

#### Notification Handler
To fix automatic data acquisition without the need for manual requests, a notification handler was implemented in Python. This feature is designed to automatically receive data from the board. The incorporation of a message array in this setup will be beneficial for future labs, as it allows for the reception of multiple string arrays autonomously, without manual requests. The Python code is provided below:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/nothandler2.png?raw=true)

#### Current time in milliseconds 
I created a loop on the Artemis board to capture the current time in milliseconds at regular intervals and send these timestamps to my laptop. The laptop processed the incoming data. By collecting  the timestamps for a set duration, the effective data transfer rate was also calculated.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/nothandler1.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/transferrate.png?raw=true)

#### Send Time
To collect and transmit timestamp data from the Artemis board to my laptop, a global array was created. When receiving a SEND_TIME_DATA command, the Artemis board transmitts the stored timestamps to the computer, where they are received and stored in a Python list for processing. This system optimized data transmission by bundling multiple timestamps in a single BLE packet, minimizing transmission overhead. Below is the use code.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/sendTime.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/sendTimejup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/recSendData.png?raw=true)

#### Get Temperature
For this task, an additional array was created with the timestamp array on the Artemis board to hold temperature readings, correspondending between the timestamps and the temperature data. Upon the GET_TEMP_READINGS command, the board transmitted each paired timestamp and temperature reading to my laptop. The notification handler received this data, parsing and allocating the readings into two separate lists for timestamps and temperatures. This approach facilitated synchronized data collection and provided a structured dataset.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/tempArd.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/tempJup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/tempRes.png?raw=true)

#### Discussion
The two methods handle data collection and transmission in different ways. The first method captures the current time at set intervals and immediately sending this information. This approach is advantageous for real-time monitoring as it allows immediate action based on the received data. However, it can hinder the BLE connection due to the constant data flow and may lead to inefficiencies in bandwidth usage due to the potential overhead of sending small packets of data frequently.

The second method stores temperature readings alongside timestamps in a second array, with data transmission initiated by a specific command. This method is more data-efficient, as it sends larger packets of information, reducing the relative overhead of BLE transmissions. The downside is the potential delay in data availability on the receiving end, and the increased use of memory for storing data before sending. This approach is better suited for scenarios where data can be reviewed rather than requiring immediate action.

The speed is constrained by the rate at which the Artemis board can sample the temperature sensor and store the data. With 384 kB of RAM, the board can theoretically store a large volume of data points. If we consider each data point to comprise a timestamp and temperature reading, both stored as 32-bit values, each pair would occupy 8 bytes. Therefore, the board could store up to 48,000 such pairs before running out of memory. However, the actual amount of data that can be stored will be less. If we instead were to sample 16-bit values at 150 Hz every 5 seconds, we would be able to create 256 values before the storage on the Artemis board would run out.

The data rate with 5-byte replies is 32.4 bytes/s, whereas with 120-byte replies, the data rate is 410 bytes/s.

## Lab 2: IMU

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
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2accnoutflat.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2accout90.png?raw=true)

The accelerometer has high accuracy when coupled with a low pass filter, although it displays a slight deviation from the mean. Below you can see expected versus actual values for pitch and roll.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2exppitch.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2exproll.png?raw=true)

Below is the collected data and FFT with no large noise source around.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2fft.png?raw=true)

Below is the code that was used for the low-pass filter. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2lpf.png?raw=true)

I believe this is due to the low magnitude of noise since the IMU already has a low pass filter according to its datasheet

### Gyroscope
The gyroscope uses the change in angle around each axis with this equation.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroeq.png?raw=true)

And here you can see the code that was used. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyrocode.png?raw=true)

To minimize noise in the accelerometer data, it was necessary to incorporate gyroscope readings. However, As evident from the graph below, there is a noticeable drift in the gyroscope data over time, with the delay being set to 10 seconds.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroplot.png?raw=true)

Here is the outputs from the serial plotter when it's position is flat and 90 degrees. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroplot.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2gyroflat.png?raw=true)

### Complimentaray Filter
To address the issue of noise and drift from the accelerometer and the gyroscope, a complimentary filter was implemented. This is demonstrated in the code below. This filter uses a weighting function, defined by the variable 'alpha', to mitigate noise from the accelerometer and drifting from the gyroscope. The optimal value of 'alpha' was established through a series of tests and it was determined to be 0.1.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2comp.png?raw=true)

### Sample Data
After eliminating all delay and print statements, and by storing time, accelerometer, and gyroscope data, the sampling rate became very fast. The average was around 4ms. This could result in an excessive volume of data collected in a brief duration. Such a large dataset could quickly consume the Artemis' storage capacity and make it challenging to analyze the robot's long-term behavior. To manage this, the data was stored into different string arrays before being trannsmitted to my laptop. Note that the IMU values are int16_t and 100 values for each array was enough. This was about 10000Kb and with the 384kB internal data, the artemis has plenty of memory for data collection. See my implementation below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/2sample.png?raw=true)
 
In addition to the data management, I had to combine two files in order to be able to transfer data. I created a header file for the IMU sensor file and included that in the ble file, where I then ran my the program. 

