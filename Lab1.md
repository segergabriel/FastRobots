## Lab 1: The Artemis and Bluetooth

### Part 1) Objective
The objective of this section of the lab is to familiarize myself with the Arduino IDE and the Artemis board. After Completing this lab, I will be able to program the board, utilizing the board LED, managing serial communications over USB, and operating the onboard temperature sensor and the microphone.

### Prelab
I Installed the Arduino IDE on my computer and made sure that I had the latest versions of ArduinoIDE and Sparkfun Appollo3 support software. 

### Blink it up
To evaluate the functionality of the LED on the Artemis Board, the "Blink it Up" sample code was employed. This script was designed to activate the LED for a set duration, followed by a phase where the LED is deactivated for another set period. As demonstrated in the video, this resulted in the LED being turned on for 2 seconds and then turned off for 1 second.
<iframe width="420" height="315"
src="https://www.youtube.com/embed/J3cUdux_d7M">
</iframe>

### Serial Example
For this example, the goal was to evaluate the performance of the serial monitor output. During this test, the Arduino was programmed to transmit a numerical sequence to the serial monitor. Additionally, it was set up to enable user interaction, where the user could input data into the serial monitor. The Artemis Board was then tasked with reading this input and displaying it back onto the serial monitor. The process and its results are illustrated in the image below.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/serialExample.png?raw=true" width="500" height="300">

### Analogread Example
In the third experiment, the focus was on the temperature sensor. The temperature readings were relayed to the serial monitor in millidegrees Celsius. To verify the accuracy, I quickly placed my hand over the Artemis board, which resulted in a slight temperature rise, as indicated by the readings changing from 31.95 to 31.99 degrees Celsius.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/analog.png?raw=true)

### Microphone Output Example
In this example, we wanted ot find the loudest frequency using the microphone. The video below show the results. 
<iframe width="420" height="315"
src="https://www.youtube.com/embed/YdRLgCws2fE">
</iframe>

### Part 2) Objective
The objective of this laboratory was to establish a Bluetooth connection between the Artemis board and my laptop. The task involved integrating Python code with the Artemis board through Arduino programming. The purpose of this setup was to facilitate the transmission of data via Bluetooth, which will be utilized in upcoming lab exercises.

### Prelab
The bluetooth software used in this course was compatible with my mac. So, it was easy to set up a virtual environment. I installed Python and Jupyter Notebook within this environment, enabling the Artemis board to connect easily to the Python code on my Mac.

### Configurations
Next, I connected the Artemis Board to the Python code. This was achieved by uploading the provided ble_arduino.ino file to the Artemis, which resulted in the MAC address being updated to c0:89:25:6d:b0:4b, as depicted in the figure below.
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/advert1.png?raw=true)

I also modified the MAC address in the Python script within the connection.yaml file, as shown below. This modification was important for enabling the Python code to detect and establish a connection with the Artemis. The second step involved the use of a unique UUID address. Given that multiple Artemis boards in the course had identical MAC addresses, it was possible for the Python code to inadvertently connect to the wrong board. Therefore, I generated a unique UUID in Python, which I then incorporated into the ble_service field in the connection.yaml file and the BLE_UUID_TEST_SERVICE in the Arduino code.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/jupuuid.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/arduuid.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/jupuuid.png?raw=true" width="500" height="250">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/arduuid.png?raw=true" width="500" height="250">

### Echo
To verify the connection between the Artemis board and the computer, I executed some commands, starting with the ECHO command. This command involved sending a string of characters from the Python code to the Artemis board, which then relayed the same string back to the Python code. To facilitate this, I integrated the ECHO command into the Arduino code within the ble_arduino.ino file, where 'echo' was appended to the array of command types. The code for ECHO is shown below

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/echoarduino.png?raw=true" width="500" height="300">

The following command is used in Python:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/echojup.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/advert.png?raw=true" width="400" height="100">

### GET_TIME_MILLIS
The next command focused on obtaining the current time from the Artemis board. This required utilizing the millis() function from the Arduino library. The value obtained from millis() was then converted into a double data type, and then formatted into a string. This string representation of the current time was then transmitted to the Python script. The relevant segment of the Arduino code is below:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/getmillisarduino.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/getmillisarduino.png?raw=true" width="500" height="300">

And below is the command in Python.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/getmillisjup.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/getmillisjup.png?raw=true" width="500" height="300">

### Notification Handler
To fix automatic data acquisition without the need for manual requests, a notification handler was implemented in Python. This feature is designed to automatically receive data from the board. The incorporation of a message array in this setup will be beneficial for future labs, as it allows for the reception of multiple string arrays autonomously, without manual requests. The Python code is provided below:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/nothandler2.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/nothandler2.png?raw=true" width="500" height="300">

### Current time in milliseconds 
I created a loop on the Artemis board to capture the current time in milliseconds at regular intervals and send these timestamps to my laptop. The laptop processed the incoming data. By collecting  the timestamps for a set duration, the effective data transfer rate was also calculated.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/nothandler1.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/transferrate.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/nothandler1.png?raw=true" width="500" height="300">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/transferrate.png?raw=true" width="500" height="300">

### Send Time
To collect and transmit timestamp data from the Artemis board to my laptop, a global array was created. When receiving a SEND_TIME_DATA command, the Artemis board transmitts the stored timestamps to the computer, where they are received and stored in a Python list for processing. This system optimized data transmission by bundling multiple timestamps in a single BLE packet, minimizing transmission overhead. Below is the use code.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/sendTime.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/sendTimejup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/recSendData.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/sendTime.png?raw=true" width="500" height="300">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/sendTimejup.png?raw=true" width="500" height="300">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/recSendData.png?raw=true" width="500" height="300">

### Get Temperature
For this task, an additional array was created with the timestamp array on the Artemis board to hold temperature readings, correspondending between the timestamps and the temperature data. Upon the GET_TEMP_READINGS command, the board transmitted each paired timestamp and temperature reading to my laptop. The notification handler received this data, parsing and allocating the readings into two separate lists for timestamps and temperatures. This approach facilitated synchronized data collection and provided a structured dataset.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/tempArd.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/tempJup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/tempRes.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/tempArd.png?raw=true" width="500" height="300">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/tempJup.png?raw=true" width="500" height="300">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/tempRes.png?raw=true" width="500" height="300">

### Discussion
The two methods handle data collection and transmission in different ways. The first method captures the current time at set intervals and immediately sending this information. This approach is advantageous for real-time monitoring as it allows immediate action based on the received data. However, it can hinder the BLE connection due to the constant data flow and may lead to inefficiencies in bandwidth usage due to the potential overhead of sending small packets of data frequently.

The second method stores temperature readings alongside timestamps in a second array, with data transmission initiated by a specific command. This method is more data-efficient, as it sends larger packets of information, reducing the relative overhead of BLE transmissions. The downside is the potential delay in data availability on the receiving end, and the increased use of memory for storing data before sending. This approach is better suited for scenarios where data can be reviewed rather than requiring immediate action.

The speed is constrained by the rate at which the Artemis board can sample the temperature sensor and store the data. With 384 kB of RAM, the board can theoretically store a large volume of data points. If we consider each data point to comprise a timestamp and temperature reading, both stored as 32-bit values, each pair would occupy 8 bytes. Therefore, the board could store up to 48,000 such pairs before running out of memory. However, the actual amount of data that can be stored will be less. If we instead were to sample 16-bit values at 150 Hz every 5 seconds, we would be able to create 256 values before the storage on the Artemis board would run out.

The data rate with 5-byte replies is 32.4 bytes/s, whereas with 120-byte replies, the data rate is 410 bytes/s.
