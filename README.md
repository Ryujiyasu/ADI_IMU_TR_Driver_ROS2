# ADI_IMU_TR_Driver_ROS2

This repository is the ROS2 driver for ADI_IMU.

[Click here](https://github.com/technoroad/ADI_IMU_TR_Driver_ROS1) for ROS1 version.

### Overview
“TR-IMU1647X” is Analog Devices IMU sensor that can be easily connected to ROS and output high-precision attitude angles.

<div align="center">
  <img src="doc/TR-IMU16475-2.jpg" width="60%"/>
</div>


### Compatible sensors
This software is compatible with these sensors.
- TR-IMU16470
- TR-IMU16475-2
- TR-IMU-Platform

### Operating environment
This software has been confirmed to work on the following OS and ROS versions.
- Ubuntu 18.04 LTS + ros2 eloquent  
- Ubuntu 20.04 LTS + ros2 foxy


### How to use
#### DIP switch settings
First, set the DIP switch.
- For TR-IMU16470 or TR-IMU16475-2, turn on No. 1 and No. 4 and turn off all the rest.
- For TR-IMU-Platform, turn on No. 1 and No. 5 and turn off all the rest.

After setting the switch, connect the sensor via USB.

#### Install
Go to your package directory and clone.
```
$ cd [your package directory]
$ git clone --recursive https://github.com/technoroad/ADI_IMU_TR_Driver_ROS2
```

Then resolve dependencies.
```
$ cd [your workspace directory]
$ rosdep update
$ rosdep install --from-paths src --ignore-src --rosdistro ${ROS_DISTRO} -y
```

#### Build
Go to your workspace directory and run the build command.  
```
$ cd [your workspace directory]
$ colcon build --symlink-install
```
Then set the path.
```
$ source ./install/setup.bash
```

#### Run
Run the launch file as:

```
$ ros2 launch adi_imu_tr_driver_ros2 adis_rcv_csv.launch.py mode:=Attitude device:=/dev/ttyACM0
```

You can see the model of ADIS16470 breakout board in rviz2 panel.

### Modes
#### Explanation
This software has the following modes.
- Attitude mode
  - The attitude angle of the sensor is output as tf message.
- Register mode
  - The angular velocity and linear　acceleration are output as an IMU message.

#### How to switch
Open 'ADI_IMU_TR_Driver_ROS1/launch/adis_rcv_csv.launch' and change it with the mode tag.  
```
<launch>
Choice mode "Attitude" or "Register"
  <arg name="mode"      default="Attitude"/>
・・・
```
### Topics
This software outputs the following topics.

- /imu/data_raw (sensor_msgs/Imu)

  IMU raw output. It contains angular velocities and linear
  accelerations. The orientation is always unit quaternion.  
  To view this data, execute sensor in the Register mode.  
  example:


```
$ ros2 tocpic echo /imu/data_raw
・・・
angular_velocity:
  x: -0.0116995596098
  y: -0.00314657808936
  z: 0.000579557116093
angular_velocity_covariance: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
linear_acceleration:
  x: 0.302349234658
  y: -0.303755252655
  z: 9.87837325989
linear_acceleration_covariance: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
・・・
```

- /diagnostics (diagnostic_msgs/DiagnosticArray)

  Sensor state output.  
  example:

```
$ ros2 topic echo /diagnostics
・・・
header:
  seq: 80
  stamp:
    secs: 1587104853
    nsecs: 921894057
  frame_id: ''
status:
  -
    level: 0
    name: "adis_rcv_csv_node: imu"
    message: "OK"
    hardware_id: "ADIS16470"
    values: []
・・・
```
### Common operations
#### Calibration
How to update the calibration parameters.
1. Start the sensor in attitude mode using the following command.
```
$ ros2 launch adi_imu_tr_driver_ros2 adis_rcv_csv.launch.py mode:=Attitude device:=/dev/ttyACM0
```
2. Leave the sensor stationary for 120 seconds. (Parameters are calculated automatically)
3. Update the parameters with the following command.

```
ros2 service call /imu/cmd_srv adi_imu_tr_driver_ros2/srv/SimpleCmd "{cmd: 'START_BIAS_CORRECTION', args: []}"
```

#### Reset attitued
How to reset the attitude angle.
1. Start the sensor in attitude mode using the following command.
```
$ ros2 launch adi_imu_tr_driver_ros2 adis_rcv_csv.launch.py mode:=Attitude device:=/dev/ttyACM0
```
2. Leave the sensor stationary for 120 seconds. (Parameters are calculated automatically)
3. Update the parameters with the following command.

```
$ ros2 service call /imu/cmd_srv adi_imu_tr_driver_ros2/srv/SimpleCmd "{cmd: 'RESET_FILTER', args: []}"
```


### License
MIT
