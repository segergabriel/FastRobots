## LAB 3: Time-Of-Flight Sensor

### Lab Objective
The goal of this lab was to connect the Artemis board to two VL53L1X Time-of-Flight distance sensors. This involved soldering and connecting the sensors to the Artemis board. Distance sensors will allow our robot to detect and react to obstacles in future tasks, and the faster the robot can sample data, the faster it will be able to drive.

### Prelab
Prior to executing the lab, the VL53L1X’s user manual and datasheet were reviewed. Before starting this lab, several considerations regarding the ToF sensors had to be decided on.

First, when constructing the robot, we will use two VL53L1X Time-of-Flight sensors, which utilize the same I2C address. To fix this, a GPIO pin from the Artemis board was used to deactivate one sensor, allowing for the I2C address of the second sensor to be reconfigured. Following this, the inactive sensor was turned on again, enabling distinct address assignments for both sensors. This method ensured that both ToF sensors could operate at the same time and be read independently.

The positioning of the ToF sensors is important and can influence different characteristics. Given the robot’s forward motion, I decided to position one ToF sensor at the front. Placement of the second sensor could vary, however. If I positioned it at the front too, it would provide better data for frontal obstacles but could limit spatial awareness. Installing a sensor at the back would improve navigational awareness when the robot rotates 180 degrees. However, to have quicker environmental perception without the need for the robot to rotate, I decided to place the second ToF sensor on one of the sides of the robot. This side placement will give the best opportunity for the robot to understand its surroundings as fast as possible, in my opinion.

### Connecting the first ToF sensor
<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/4board.jpeg?raw=true" alt="Wiring from VL53L1X to Artemis board" style="max-width:420px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

First, I had to solder a connection between the ToF sensors and the Artemis board. The red wire was connected to Vin and the black wire to ground. Then, I had to identify the roles of the blue and yellow wires, which correspond to the SDA and SCL data signals. From looking in the Artemis board datasheet, I determined that the blue wire should be soldered to the SDA pin and the yellow wire to the SCL pin for both sensors. I also soldered both the XSHUT pins to Artemis GPIO pins, in order to configure the sensors to different addresses, as noted in the prelab.

### I2C
The SparkFun **VL53L1X 4m laser distance sensor** library was installed via the Arduino IDE. Then, the **Apollo3 → Example05_Wire_I2C.ino** was used as an example to learn the I2C library. The address that appears on the serial monitor is **0x29**, which is different from the **0x52** address indicated in the datasheet. I believe this is because the final bit of the address, which signifies read/write status, is not considered part of the address. Therefore, the address could be either 0x52 or 0x29. The connection is displayed below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3onetof.png?raw=true" alt="Single ToF I2C connection output" style="max-width:480px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### Distance Measurements
To verify the reliability of the sensor, we need to test its measurement data. This was done by comparing actual versus collected data. The ToF sensor has two modes, **short** and **long**. Depending on the task, different distance modes can be set on the robot in order to maximize accuracy. For example, a task that involves avoiding many nearby obstacles would rely on the short distance mode. Because of this, a slower-moving robot will benefit due to its effective range of up to ~1.3 meters. However, we are building a fast robot, and therefore we will need the ability to detect obstacles at greater distances. While the long distance mode introduces more noise, it extends the range to approximately **4 meters**, and the fast-moving robot will be able to react in time.  
Below is the output for one sensor.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3dist1.png?raw=true" alt="Single sensor distance output" style="max-width:480px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

I needed to evaluate the accuracy of the sensor at shorter distances, as those are not the ranges the mode is optimized for. With the short mode, I collected various data points over distances by marking distances along a wall and moving the ToF sensor along it facing an object on the other end. The data points are displayed in the graph below. The data is consistent and linear. The standard deviation bars, which I included in the graph, didn’t show up for shorter ranges. However, at longer distances the deviation bars became somewhat noticeable, suggesting a slight tendency of the ToF sensor to overshoot distances as the range increased.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3graph2.png?raw=true" alt="Measured distance vs ground truth with error bars" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### 2 ToF sensors
To enable parallel operation of the two ToF sensors, I adapted the Artemis code as outlined in the prelab and assigned distinct I2C addresses to each sensor. This was achieved by cutting off power to one sensor using pin 8, allowing me to modify the powered sensor’s address to **0x20**. Then, the unpowered sensor was reactivated and the same thing was done to the second sensor which was modified to **0x29**. After this, both sensors were able to function concurrently and effectively gather data. Below is my code to set the addresses.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3setadd.png?raw=true" alt="Code to set I2C addresses for two ToF sensors" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

The figure below displays the output for the two sensors in mm.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3twotof.png?raw=true" alt="Two sensors simultaneous distance readings" style="max-width:360px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

### ToF sensor speed
The response time of the sensors is a limiting factor and therefore, I modified the code to only read the data when the data is ready. This modification was based on the example code used for sensor testing. I also placed a command to print the current time using `millis()` outside the conditional statement that prints the time once for every loop. This is helpful to decide how often distance readings are recorded. See the implementation and output below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3speedcode.png?raw=true" alt="Timing logic for reading data-ready and millis()" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3speed.png?raw=true" alt="Loop timing output while sampling" style="max-width:300px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

One can see that the loop duration is approximately **2 ms** when the sensors are inactive but extends to around **20 ms** during data collection. This shows that acquiring sensor data is the limiting factor in the process. By minimizing the frequency of sensor polling, there’s an opportunity to increase the number of other calculations performed within the same time frame.

### Time versus Distance
I modified the code from the first lab to include the distance data, which is important for debugging the ToF sensors. I followed the same procedure as Lab 1 for adding a Bluetooth command. Then, I integrated my new distance measurement method from the previous section into the `handle_command()` function. Lastly, I stored them in two variables, `distance1` and `distance2`. Below is the Arduino code that was implemented.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3finalcode.png?raw=true" alt="Arduino code integrating distance into BLE command handler" style="max-width:520px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

These modifications enabled me to retrieve data from both sensors using a single command, **GET_DIST_FOR2**. The distances of both sensors were taken over time and sent to Jupyter using an updated notification handler. This data provided clear visualizations and is displayed below.

<div style="text-align:center;">
  <img src="https://github.com/segergabriel/FastRobots/blob/main/images/3finalgraph.png?raw=true" alt="Time vs distance for two sensors plotted in Jupyter" style="max-width:760px; width:100%; border-radius:8px; margin:1em auto; display:block;">
</div>

