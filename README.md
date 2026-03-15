# esp-idf-icm42688
A demo showing the pose of the icm42688 6DoF IMU sensor in 3D using esp-idf.   

You can use the Kalman filter or the Madgwick filter to estimate the Euler angle.   
Euler angles are roll, pitch and yaw.   
It's very intuitive and easy to understand.   
However, since icm42688 is a 6DoF IMU, YAW estimation is not possible.   
![a-Pitch-yaw-and-roll-angles-of-an-aircraft-with-body-orientation-O-u-v-original](https://user-images.githubusercontent.com/6020549/226072914-a7f923fc-eb6e-4d19-b2ff-8c9f2749ee6f.jpg)   
You can view like this.   
![Image](https://github.com/user-attachments/assets/cb9f1408-b372-4e55-84f6-1191f068cd1a)

# Software requirements
ESP-IDF V5.2 or later.   
Because this project uses the new I2C driver.   

# Hardware requirements
ICM42688 Accelerometer Gyroscope module 6 Dof inertial Measurement Sensors.   

# Wireing
|ICM42688||ESP32|ESP32-S2/S3|ESP32-C2/C3/C6||
|:-:|:-:|:-:|:-:|:-:|:-:|
|VCC|--|3.3V|3.3V|3.3V||
|GND|--|GND|GND|GND||
|ADO/MISO|--|GND/3.3V|GND/3.3V|GND/3.3V|(*2)|
|SDA/MOSI|--|GPIO21|GPIO11|GPIO4|(*1)|
|SCL/SCLK|--|GPIO22|GPIO12|GPIO5|(*1)|
|CS|--|3.3V|3.3V|3.3V|Use i2c|

(*1)You can change it to any pin using menuconfig.   

(*2)Choosing an i2c address.   
GND:i2c address is 0x68.   
3.3V:i2c address is 0x69.   

# Find the sensor
We can find the sensor using [i2c-tools](https://github.com/espressif/esp-idf/tree/master/examples/peripherals/i2c/i2c_tools).   

- Selcect SCL and SDA using menuconfig.   
![Image](https://github.com/user-attachments/assets/5a5d46fd-ce1e-4199-acf4-c6b49abc2dd8)

- Detect senser.   
The i2c address for this sensor is 0x68 or 0x69.   
![Image](https://github.com/user-attachments/assets/46327a94-2623-47ed-b73c-088f7b882865)

- Read register.   
Register 0x75 for this sensor is 0x47.   
![Image](https://github.com/user-attachments/assets/6d8bd6bc-8349-4fdc-a2f5-1ea57e0a85a0)

# Get Euler angles from icm42688 using Kalman filter
I used [this](https://github.com/TKJElectronics/KalmanFilter).
```
git clone https://github.com/nopnop2002/esp-idf-icm42688
cd esp-idf-icm42688/Kalman
idf.py set-target {esp32/esp32s2/esp32s3/esp32c2/esp32c3/esp32c6}
idf.py menuconfig
idf.py flash
```

### Configuration
![config-top](https://user-images.githubusercontent.com/6020549/226072705-e26bfc3e-1a70-4df2-98c9-a603daf58761.jpg)
![config-app](https://user-images.githubusercontent.com/6020549/226072711-ce59da34-bf0e-4bc2-8305-851f5d418097.jpg)


# Get Euler angles from icm42688 using Madgwick filter
I used [this](https://github.com/arduino-libraries/MadgwickAHRS).
```
git clone https://github.com/nopnop2002/esp-idf-icm42688
cd esp-idf-icm42688/Madgwick
idf.py set-target {esp32/esp32s2/esp32s3/esp32c2/esp32c3/esp32c6}
idf.py menuconfig
idf.py flash
```

### Configuration
![config-top](https://user-images.githubusercontent.com/6020549/226072705-e26bfc3e-1a70-4df2-98c9-a603daf58761.jpg)
![config-app](https://user-images.githubusercontent.com/6020549/226072711-ce59da34-bf0e-4bc2-8305-851f5d418097.jpg)

# View Euler angles with built-in web server   
ESP32 acts as a web server.   
I used [this](https://github.com/Molorius/esp32-websocket) component.   
This component can communicate directly with the browser.   
It's a great job.   
Enter the following in the address bar of your web browser.   
```
http:://{IP of ESP32}/
or
http://esp32.local/
```

![bmi160-euler](https://user-images.githubusercontent.com/6020549/232381988-f4003a78-2145-493c-829e-9d0952117ea6.JPG)

WEB pages are stored in the html folder.   
I used [this](https://threejs.org/) for 3D display.   
I used [this](https://canvas-gauges.com/) for gauge display.   
Configuration Options for the gauge display is [here](https://canvas-gauges.com/documentation/user-guide/configuration).   
You can change the design and color according to your preference like this.   
![Image](https://github.com/user-attachments/assets/dfe82573-27a7-4395-bad6-cef1e3b4299c)   


# View Euler angles using PyTeapot   
You can view Euler angles using [this](https://github.com/thecountoftuscany/PyTeapot-Quaternion-Euler-cube-rotation) tool.   
It works as a UDP display server.   
This is a great application.   

```
+-------------+          +-------------+          +-------------+
|             |          |             |          |             |
|     IMU     |--(i2c)-->|    ESP32    |--(UDP)-->| pyteapot.py |
|             |          |             |          |             |
+-------------+          +-------------+          +-------------+
```

### Installation for Linux
```
$ sudo apt install python3-pip python3-setuptools
$ python3 -m pip install -U pip
$ python3 -m pip install pygame
$ python3 -m pip install PyOpenGL PyOpenGL_accelerate
$ git clone https://github.com/thecountoftuscany/PyTeapot-Quaternion-Euler-cube-rotation
$ cd PyTeapot-Quaternion-Euler-cube-rotation
$ python3 pyteapot.py
```
The posture of your sensor is displayed.   
![bmi180_2023-03-17_06-08-57](https://user-images.githubusercontent.com/6020549/226072858-b5e52dc5-db87-4613-8c05-4008a4bb8170.png)

### Installation for Windows   
Install Git for Windows from [here](https://gitforwindows.org/).   
Install Python Releases for Windows from [here](https://www.python.org/downloads/windows/).   
Open Git Bash and run:   
```
$ python --version
Python 3.11.9
$ python -m pip install -U pip
$ python -m pip install pygame
$ python -m pip install PyOpenGL PyOpenGL_accelerate
$ git clone https://github.com/thecountoftuscany/PyTeapot-Quaternion-Euler-cube-rotation
$ cd PyTeapot-Quaternion-Euler-cube-rotation
$ python pyteapot.py
```
![Image](https://github.com/user-attachments/assets/3aa9fd0d-2a0a-4a7c-ac40-4b84a70acaaf)


# View Euler angles using panda3d library   
You can view Euler angles using [this](https://www.panda3d.org/) library.   
It works as a UDP display server.   

```
+-------------+          +-------------+          +-------------+
|             |          |             |          |             |
|     IMU     |--(ic2)-->|    ESP32    |--(UDP)-->|  panda.py   |
|             |          |             |          |             |
+-------------+          +-------------+          +-------------+
```

### Installation for Linux
```
$ python3 --version
Python 3.11.2
$ sudo apt install python3-pip python3-setuptools
$ python3 -m pip install -U pip
$ python3 -m pip install panda3d
$ git clone https://github.com/nopnop2002/esp-idf-mpu6050-dmp
$ cd esp-idf-mpu6050-dmp/panda3d
$ python3 panda.py --help
usage: panda.py [-h] [--model {jet,biplain,707,fa18}]

options:
  -h, --help            show this help message and exit
  --model {jet,biplain,707,fa18}
```
![Image](https://github.com/user-attachments/assets/83804b5e-3ffe-4e18-966e-0ce180c1ab21)

### Installation for Windows
Install Git for Windows from [here](https://gitforwindows.org/).   
Install Python Releases for Windows from [here](https://www.python.org/downloads/windows/).   
Open Git Bash and run:   
```
$ python --version
Python 3.11.9
$ python -m pip install -U pip
$ python -m pip install panda3d
$ git clone https://github.com/nopnop2002/esp-idf-mpu6050-dmp
$ cd esp-idf-mpu6050-dmp/panda3d
$ python panda.py --help
usage: panda.py [-h] [--model {jet,biplain,707,fa18}]

options:
  -h, --help            show this help message and exit
  --model {jet,biplain,707,fa18}
```
![Image](https://github.com/user-attachments/assets/7f2fbdf4-97d9-40c3-87db-9f8386741220)

### How to use   
See [here](https://github.com/nopnop2002/esp-idf-mpu6050-dmp/blob/main/panda3d/README.md)   


# MPU6050 and ICM42688 Comparison
Both the MPU6050 and ICM42688 are TDK InvenSense 6-axis IMUs (accelerometer + gyroscope).   
But they belong to different generations and have significant differences in performance and application.   
|Features|MPU6050|ICM42688|
|:-:|:-:|:-:|
|Generation|Old generation|Latest generation|
|Noise|High|Extremely low|
|Output Rate|Low (Maximum 1kHz)|Very High (Maximum 32kHz)|
|Interface|I2C only (depending on the module)|SPI + I2C|
|Accelerometer Resolution|16-bit|18-bit|
|Gyroscope Resolution|16-bit|19-bit|
