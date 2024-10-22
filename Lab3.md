## LAB 3: Time-Of-Flight Sensor

### Lab Objective
The goal of this lab was to connect the Artemis board to two VL53L1X Time-of-Flight Distance Sensors. This involved soldering and connecting the sensors to the Artemis board.  Distance sensors will allow our robot to detect and react to obstacles in future tasks, and the faster the robot can sample data, the faster it will be able to drive. 

### Prelab
Prior to executing the lab, the VL53L1X's user manual and datasheet were reviewed. Before starting this lab, several considerations regarding the ToF sensors had to be decided on. 

First, when constructing the robot, we will use two VL53L1X Time-of-Flight sensors, which utilize the same I2C address. To fix this, a gpio pin from the Artemis board was used to deactivate one sensor, allowing for the I2C address of the second sensor to be reconfigured. Following this, the inactive sensor was turned on again, enabling distinct address assignments for both sensors. This method ensured that both ToF sensors could operate at the same time and be read independently.

The positioning of the ToF sensors is important and can influence different characteristics. . Given the robot's forward motion, I decided to position one ToF sensor at the front. Placement of the second sensor could vary, however. If I positioned it at the front too, it would provide better data for frontal obstacles but could limit spatial awareness. Installing a sensor at the back would improve navigational awareness when the robot rotates 180 degrees. However, to have quicker environmental perception without the need for the robot to rotate, I decided to place the second ToF sensor on one of the sides of the robot. This side placement will give the best opportunity for the robot to understand its surroundings as fast as possible in my opinion. 

### Connecting the first ToF sensor

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/4board.jpeg?raw=true" width="400" height="400">

First, I had to solder a connection between the ToF sensors and the Artemis board. So, the red wire was connected to the Vin and the black wire to the ground. Then, I had to identify the roles of the blue and yellow wires, which correspond to the SDA and SCL data signals. From looking in the Artemis board datasheet, I could determine that the blue wire should be soldered to the SDA pin and the yellow wire to the SCL pin for both sensors. I also soldered both the xshut pins to artemis gpio pins, in order to configure the sensors to different addresses, as noted in the prelab.

### I2C

The SparkFun VL53L1X 4m laser distance sensor library was installed via the Arduino IDE. Then, the Apollo 3 -> Example05_Wire_I2C.ino was used as an example to learn the I2C library. The address that appears on the serial monitor is 0x29, which is different from the 0x52 address indicated in the datasheet. I believe this is due to the final bit of the address, which signifies read/write status, is not considered part of the address. Therefore, the address could be either 0x52 or 0x29. The connection is displayed below. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/3onetof.png?raw=true" width="400" height="150">

### Distance Measurements

To verify reliability of the sensor, we need to test its measurement data. This was done by comparing actual versus collected data. The ToF sensor has two modes, short and long. Depending on the task, different distance modes can be set on the robot in order to maximize accuracy. For example, a task that involves avoiding many nearby obstacles would rely on the short distance mode. Because of this, a slower moving robot will benefit due to its effective range of up to 1.3 meters. However, we are building a fast robot, and therefore we will need the ability to detect obstacles at greater distances. While the long distance mode introduces more noise, it extends the range to approximately 4 meters, and the fast-moving robot will be able to react in time. 
Below is the output for one sensor.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/3dist1.png?raw=true" width="400" height="150">

I needed to evaluate the accuracy of the sensor at shorter distances, as those are not the ranges the mode is optimized for.  With the short mode, I collected various data points over distances by marking distances along a wall and moving the TOF sensor along it facing an object on the other end. The data points which are displayed in the graph below. The data is consistent and linear. The standard deviation bars, which I included in the graph, didn't showed up for shorter ranges. However, at longer distances the deviation bars became somewhat noticeable, suggesting a slight tendency of the ToF sensor to overshoot distances as the range increased.

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/3graph2.png?raw=true)

### 2 TOF sensors

To enable parallel operation of the two ToF sensors, I adapted the Artemis code as outlined in the prelab and assigned distinct I2C addresses to each sensor. I talked about this in the prelab but this was achieved by cutting off power to one sensor using pin 8, allowing me to modify the powered sensor's address to 0x20. Then, the unpowered sensor was reactivated and the same thing was done to the second sensor which was modified to address 0x29. After this, both sensors were able to function concurrently and effectively gather data. Below is my code to set the addresses. 

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/3setadd.png?raw=true" width="400" height="250">

The figure below displays the output for the two sensors in mm.

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/3twotof.png?raw=true" width="300" height="80">

### Tof sensor speed

The response time of the sensors are a limiting factor and therefore, I modify the code to only read the data when the data is ready. This modification was based on the example code used for sensor testing. I also placed a command to print the current time using millis() outside the conditional statement that prints the time once for every loop. This is helpful to decide how often distance readings are recorded. See the implementation and output below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/3speedcode.png?raw=true)
![advert](https://github.com/segergabriel/FastRobots/blob/main/images/3speed.png?raw=true)

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/3speedcode.png?raw=true" width="400" height="400">

<img src="https://github.com/segergabriel/FastRobots/blob/main/images/3speedtof.png?raw=true" width="200" height="500">


One can see that the loop duration is approximately 2ms when the sensors are inactive but extends to around 20ms during data collection. This shows that acquiring sensor data is the limiting factor in the process. By minimizing the frequency of sensor polling, there's an opportunity to increase the number of other calculations performed within the same time frame.

### Time versus Distance

I modified  the code from the first lab to include the distance data, which is important for debugging the ToF sensors. I followed the same procedure as lab 1 for adding a Bluetooth command. Then, I integrated my new distance measurement method from the previous section into the handle_command() function. Lastly, I stored them in two variables, distance1 and distance2. Below is the arduino code that was implemented. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/3finalcode.png?raw=true)

These modifications enabled me to retrieve data from both sensors using a single command, GET_DIST_FOR2. The distances of both sensors were taken over time, and sent to Jupyter using an updated notification handler. This data provided clear visualizations wand is displayed below. 

![advert](https://github.com/segergabriel/FastRobots/blob/main/images/3finalgraph.png?raw=true)

