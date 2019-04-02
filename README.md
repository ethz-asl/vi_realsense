# realsense_d435i

This repo contains submodules with specific version of [librealsense](https://github.com/IntelRealSense/librealsense) 
and a fixed (mostly IMU related) and improved version of [realsense](https://github.com/intel-ros/realsense).
The main goal was to enable mapping with ROVIOLI.

**librealsense**
 * No changes, Version 2.19.1

**realsense**
 * Based on Version 2.2.1
 * Implement an actually meaningful unite_imu logic
 * Extends the ROS node interface to allow configuration of:
     * Emitter ON/OFF
     * Format of the image streams (MONO8, MONO16, BGR8, RGB8, etc.)
     * Publishing CameraInfo ON/OFF. Disabling allows certain high-resolution configurations for the streams that do not have intrinsics store on the sensor. These configurations would otherwise just crash the driver.
 
