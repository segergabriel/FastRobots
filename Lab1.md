## Lab 1: The Artemis and Bluetooth

### Part 1) Objective
The objective of this section of the lab is to familiarize myself with the Arduino IDE and the Artemis board. After completing this lab, I will be able to program the board, utilize the board LED, manage serial communications over USB, and operate the onboard temperature sensor and the microphone.

### Prelab
I installed the Arduino IDE on my computer and made sure that I had the latest versions of Arduino IDE and SparkFun Apollo3 support software. 

### Blink it Up
To evaluate the functionality of the LED on the Artemis board, the "Blink it Up" sample code was employed. This script was designed to activate the LED for a set duration, followed by a phase where the LED was deactivated for another set period. As demonstrated in the video, this resulted in the LED being turned on for 2 seconds and then off for 1 second.

<div align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/J3cUdux_d7M" frameborder="0" allowfullscreen></iframe>
</div>

### Serial Example
For this example, the goal was to evaluate the performance of the serial monitor output. During this test, the Arduino was programmed to transmit a numerical sequence to the serial monitor. Additionally, it was set up to enable user interaction, where the user could input data into the serial monitor. The Artemis board was then tasked with reading this input and displaying it back onto the serial monitor.  

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/serialExample.png?raw=true" width="500">
</div>

### AnalogRead Example
In the third experiment, the focus was on the temperature sensor. The temperature readings were relayed to the serial monitor in millidegrees Celsius. To verify the accuracy, I quickly placed my hand over the Artemis board, which resulted in a slight temperature rise, as indicated by the readings changing from 31.95 to 31.99 degrees Celsius.

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/analog.png?raw=true" width="500">
</div>

### Microphone Output Example
In this example, we wanted to find the loudest frequency using the microphone. The video below shows the results.  

<div align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/YdRLgCws2fE" frameborder="0" allowfullscreen></iframe>
</div>

---

### Part 2) Objective
The objective of this laboratory was to establish a Bluetooth connection between the Artemis board and my laptop. The task involved integrating Python code with the Artemis board through Arduino programming. The purpose of this setup was to facilitate the transmission of data via Bluetooth, which will be utilized in upcoming lab exercises.

### Prelab
The Bluetooth software used in this course was compatible with my Mac, so it was easy to set up a virtual environment. I installed Python and Jupyter Notebook within this environment, enabling the Artemis board to connect easily to the Python code on my Mac.

### Configurations
Next, I connected the Artemis board to the Python code. This was achieved by uploading the provided `ble_arduino.ino` file to the Artemis, which resulted in the MAC address being updated to `c0:89:25:6d:b0:4b`, as depicted below:

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/advert1.png?raw=true" width="500">
</div>

I also modified the MAC address in the Python script within the `connection.yaml` file. This modification was important for enabling the Python code to detect and establish a connection with the Artemis. The second step involved the use of a unique UUID address. Given that multiple Artemis boards in the course had identical MAC addresses, it was possible for the Python code to inadvertently connect to the wrong board. Therefore, I generated a unique UUID in Python, which I then incorporated into the `ble_service` field in the `connection.yaml` file and the `BLE_UUID_TEST_SERVICE` in the Arduino code.

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/jupuuid.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/arduuid.png?raw=true" width="500">
</div>

### Echo
To verify the connection between the Artemis board and the computer, I executed some commands, starting with the **ECHO** command. This involved sending a string of characters from the Python code to the Artemis board, which then relayed the same string back to the Python code.  

Arduino code:  
<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/echoarduino.png?raw=true" width="500">
</div>

Python side:  
<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/echojup.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/advert.png?raw=true" width="400">
</div>

### GET_TIME_MILLIS
The next command focused on obtaining the current time from the Artemis board. This required utilizing the `millis()` function from the Arduino library. The value obtained from `millis()` was then converted into a double data type, and formatted into a string. This string representation of the current time was then transmitted to the Python script.  

Arduino code:  
<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/getmillisarduino.png?raw=true" width="500">
</div>

Python code:  
<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/getmillisjup.png?raw=true" width="500">
</div>

### Notification Handler
To enable automatic data acquisition without manual requests, a notification handler was implemented in Python. This feature is designed to automatically receive data from the board. The incorporation of a message array in this setup will be beneficial for future labs, as it allows for the reception of multiple string arrays autonomously.  

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/nothandler2.png?raw=true" width="600">
</div>

### Current Time in Milliseconds 
I created a loop on the Artemis board to capture the current time in milliseconds at regular intervals and send these timestamps to my laptop. The laptop processed the incoming data. By collecting the timestamps for a set duration, the effective data transfer rate was also calculated.

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/nothandler1.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/transferrate.png?raw=true" width="500">
</div>

### Send Time
To collect and transmit timestamp data from the Artemis board to my laptop, a global array was created. When receiving a **SEND_TIME_DATA** command, the Artemis board transmitted the stored timestamps to the computer, where they were received and stored in a Python list for processing. This system optimized data transmission by bundling multiple timestamps in a single BLE packet, minimizing transmission overhead.  

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/sendTime.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/sendTimejup.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/recSendData.png?raw=true" width="500">
</div>

### Get Temperature
For this task, an additional array was created alongside the timestamp array on the Artemis board to hold temperature readings, corresponding between the timestamps and the temperature data. Upon the **GET_TEMP_READINGS** command, the board transmitted each paired timestamp and temperature reading to my laptop. The notification handler received this data, parsing and allocating the readings into two separate lists for timestamps and temperatures.  

<div align="center">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/tempArd.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/tempJup.png?raw=true" width="500">
<img src="https://github.com/segergabriel/FastRobots/blob/main/images/tempRes.png?raw=true" width="500">
</div>

### Discussion
The two methods handle data collection and transmission in different ways.  

The first method captures the current time at set intervals and immediately sends this information. This approach is advantageous for real-time monitoring as it allows immediate action based on the received data. However, it can hinder the BLE connection due to the constant data flow and may lead to inefficiencies in bandwidth usage due to the potential overhead of sending small packets of data frequently.

The second method stores temperature readings alongside timestamps in a second array, with data transmission initiated by a specific command. This method is more data-efficient, as it sends larger packets of information, reducing the relative overhead of BLE transmissions. The downside is the potential delay in data availability on the receiving end, and the increased use of memory for storing data before sending. This approach is better suited for scenarios where data can be reviewed rather than requiring immediate action.

The speed is constrained by the rate at which the Artemis board can sample the temperature sensor and store the data. With 384 kB of RAM, the board can theoretically store a large volume of data points. If we consider each data point to comprise a timestamp and temperature reading, both stored as 32-bit values, each pair would occupy 8 bytes. Therefore, the board could store up to 48,000 such pairs before running out of memory. However, the actual amount of data that can be stored will be less. If we instead were to sample 16-bit values at 150 Hz every 5 seconds, we would be able to create 256 values before the storage on the Artemis board would run out.

The data rate with 5-byte replies is **32.4 bytes/s**, whereas with 120-byte replies, the data rate is **410 bytes/s**.
