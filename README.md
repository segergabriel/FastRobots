# ECE4160 Fast Robots
## Lab 1: The Artemis and Bluetooth

### Part 1) Objective
The objective of this section of the lab is to familiarize myself with the Arduino IDE and the Artemis board. After Completing this lab, I will be able to program the board, utilizing the board LED, managing serial communications over USB, and operating the onboard temperature sensor and Pulse Density Microphone.

#### Prelab
I Installed the Arduino IDE on my computer and made sure that I had the latest versions of ArduinoIDE and Sparkfun Appollo3 support software. 

#### Blink it up
To evaluate the functionality of the LED on the Artemis Board, the "Blink it Up" sample code was employed. This script was designed to activate the LED for a predetermined duration, followed by a phase where the LED is deactivated for another set period. As demonstrated in the accompanying video, this resulted in the LED being illuminated for 2 seconds and then extinguished for 1 second.
<iframe width="420" height="315"
src="https://www.youtube.com/embed/J3cUdux_d7M">
</iframe>

#### Serial Example
For this example, the goal was to evaluate the performance of the serial monitor output. During this test, the Arduino was programmed to transmit a numerical sequence to the serial monitor. Additionally, it was set up to enable user interaction, where the user could input data into the serial monitor. The Artemis Board was then tasked with reading this input and displaying it back onto the serial monitor. The process and its results are illustrated in the photograph provided below.
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
The objective of this laboratory was to establish a Bluetooth link between the Artemis board and a computer. The task involved integrating Python code with the Artemis board through Arduino programming. The purpose of this setup was to facilitate the transmission of data via Bluetooth, which will be utilized in upcoming lab exercises.

#### Prelab
The bluetooth software used in this course was compatible with my mac. So, it was easy to set up a virtual environment. I installed Python and Jupyter Notebook within this environment, enabling the Artemis board to connect seamlessly to the Python code on my Mac.

#### Configurations
Next, I connected the Artemis Board to the Python code. This was achieved by uploading the provided ble_arduino.ino file to the Artemis, which resulted in the MAC address being updated to c0:89:25:6d:b0:4b, as depicted in the figure below.
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/advert1.png?raw=true)

I also modified the MAC address in the Python script within the connection.yaml file, as shown below. This modification was crucial for enabling the Python code to detect and establish a Bluetooth connection with the Artemis. The second critical step for a successful connection involved the use of a unique UUID address. Given that multiple Artemis boards in the course had identical MAC addresses, it was possible for the Python code to inadvertently connect to the wrong board. To circumvent this, I generated a unique UUID in Python, which I then incorporated into the ble_service field in the connection.yaml file and the BLE_UUID_TEST_SERVICE in the Arduino code, ensuring a distinct and secure connection to my Artemis board.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/jupuuid.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/arduuid.png?raw=true)

#### Echo
To verify the connection between the Artemis board and the computer, I executed several commands, starting with the ECHO command. This command involved sending a string of characters from the Python code to the Artemis board, which then relayed the same string back to the Python code. To facilitate this, I integrated the ECHO command into the Arduino code within the ble_arduino.ino file, where 'echo' was appended to the array of command types. The code for ECHO is shown below
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/echoarduino.png?raw=true)
The following command is used in Python:
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/echojup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/advert.png?raw=true)

#### GET_TIME_MILLIS
The subsequent command focused on obtaining the current time from the Artemis board. This required utilizing the millis() function from the Arduino library. The value obtained from millis() was then converted into a double data type, and subsequently formatted into a string. This string representation of the current time was then transmitted to the Python script. The relevant segment of the Arduino code is as follows:

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/getmillisarduino.png?raw=true)
And below is the command in Python.
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/getmillisjup.png?raw=true)

#### NOTIFICATION HANDLER
To facilitate automatic data acquisition without the need for manual requests, a notification handler was implemented in Python. This feature is designed to automatically receive data from the Artemis board. The incorporation of a message array in this setup is particularly beneficial for future applications, as it allows for the reception of multiple string arrays autonomously, without manual intervention. The Python implementation details are provided below:


#### Current time in milliseconds 
I created a loop on the Artemis board to capture the current time in milliseconds at regular intervals and send these timestamps to my laptop. The laptop processed the incoming data. By collecting  the timestamps for a set duration, the effective data transfer rate was determined.
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/nothandler1.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/transferrate.png?raw=true)

#### Send Time
To efficiently collect and transmit timestamp data from the Artemis board to a computer,a global array was implemented. When receiving a SEND_TIME_DATA command, the Artemis board transmitts the stored timestamps to the computer, where they are received and stored in a Python list for subsequent processing. This system optimized data transmission by bundling multiple timestamps in a single BLE packet, minimizing transmission overhead and managing memory use effectively on the Artemis board. Below is the use code.
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/sendTime.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/sendTimejup.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/recSendData.png?raw=true)

store time stamps. Now create an array that can store time stamps. This array should be defined globally so that other functions can access it if need be. In the loop, rather than send each time stamp, place each time stamp into the array. (Note: you’ll need some extra logic to determine when your array is full so you don’t “over fill” the array.) Then add a command SEND_TIME_DATA which loops the array and sends each data point as a string to your laptop to be processed. (You can store these values in a list in python to determine if all the data was sent over.)

pic of jupyter and arduino 

### GET_TEMP_READINGS


### LIMITATIONS
The Artemis Board has a maximum storage of 384 kB of RAM. If we were to sample 16-bit values at 150 Hz every 5 seconds, we would be able to create 256 values before the storage on the Artemis board would run out. This means that we will need to send data in groups to Python in order to optimize the speed of data acquisition from the Artemis board.
rewrite

### EFFECTIVE RATE AND OVERHEAD
The data rate with 5-byte replies is 32.4 bytes/s, whereas with 120-byte replies, the data rate is 410 bytes/s. Through testing multiple byte replies in intervals of 10, it can be seen that the data rate increases with the amount of bytes sent through the graph below. This trend shows that the larger replies help reduce overhead. However, if the size is too large, the data rate will break the trend and decrease, as seen comparing byte replies with greater than 100 bytes.
