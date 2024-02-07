# Lab 1: The Artemis and Bluetooth

##### Part 1) Objective
The purpose of this part of the lab is for you to setup and become familiar with the Arduino IDE and the Artemis board. After this lab, you should be comfortable programming your board, using the board LED, reading/writing serial messages over USB, and using the onboard temperature sensor and Pulse Density Microphone.
rewrite

include picture of board

##### Prelab
Install the Arduino IDE on your computer. Please use the latest versions of ArduinoIDE and Sparkfun Appollo3 support software. Update them if necessary. If you have any issues, please contact the TA team. While we only guarantee TA support on the lab computers this semester, you can likely do all Arduino-related tasks on your own computer which will save everyone time!




EXAMPLE: BLINK IT UP
To test the LED on the Artemis Board, the "Blink it Up" example was used. This code turned the LED on for an specified time, then turned the LED off for a specified time. In the example video below, the LED was on for 2 seconds, then off for 1 second.
Video blinking
"EXAMPLE2_SERIAL"
In the second example, the objective was to test the serial monitor output. In this example, the Arduino sent a counting test to the serial monitor. It then allowed the user to type an input into the serial monitor, and the Artemis Board would read this and output the input into the serial monitor. This can be seen in the photo below.
pic of serial
"EXAMPLE4_ANALOGREAD"
The third test involved using the temperature sensor. The temperature was sent to the serial monitor in 10^-3 degrees Celsius. To validicize the sensor, I covered the Atermis board with my hand, and the temperature increased slightly from 32.9 Celsius to 33.1 Celsius.
pic of an read
"EXAMPLE1_MICROPHONEOUTPUT"
This test involed using the microphone to find the loudest frequency. This is shown in the video below.
pic of mic

##### Part 2) Objective
The objective of this lab was to establish a bluetooth connection between the Artemis board and a computer. This lab involved connecting Python code to the Artemis using Arduino programming. This will then be used to send data via Bluetooth to use in future labs.

##### PRELAB
The bluetooth software used in this course was not compatible with Windows 11. Becuase of this, the setup involved loading Debian using WSL, Windows Subsystem for Linux. This was loaded onto the Windows 11 laptop, and Python using Jupyter Notebook was loaded through the WSL. Using this system, the Artemis board was able to connect to the Python code.
talk about macos here and installations

##### Configurations
Once Debian was loaded onto the laptop, the next step was to connect the Artemis Board to the Python code. The first step in this process was to change the MAC address of the Artemis from the default in the code to the one used in the lab. By uploading the given ble_arduino.ino code file to the Artemis, the MAC address reads c0:07:1c:96:23:44, shown in the figure below.

pic od advertesing
The MAC address was also changed in the Python code in the connection.yaml file shown below. This allows the Python code to read the device via bluetooth and connect to the Artemis. The second step needed to connect the device was to use a unique UUID address. Many of the Artemis boards in the course share the same MAC address, meaning the Python code might connect to different boards. Because of this, a UUID was needed to create a unique connection to the Artmeis board. This was generated in Python and then put in the "ble_service" line in connection.yaml, as well as the "BLE_UUID_TEST_SERVICE" in the Arduino code.
pic of jupyter adresses
pic of artemis adress

##### ECHO COMMAND
In order to test the connection between the Artemis board on the computer, multiple commands were made. The first was the ECHO command. This command sent a character string to the Artemis from the Python code, which then sent the phrase back to the Python code. This command was added to the Arduino code in ble_arduino.ide. Here, echo was added to the list of command types:

pic of ble enum and code for echo
pic of jupyter code

##### GET_TIME_MILLIS
The next command involved receiving the current time from the Artemis board. This meant using the millis() function in the Arduino package, then converting this value to a double, and sending it as a string to Python. The Arduino code is shown here:

pic of jupyter and get milli case

#### NOTIFICATION HANDLER
In order to be able to gather data without manually calling the data, a notification handler was developed to automatically recieve the data in Python. The message array will be useful for future use of the function as we will be able to receive multiple string arrays without calling it manually. This code is shown below:

pic of jupyter code

