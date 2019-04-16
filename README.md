# RealSense for VI-Mapping

This repo contains submodules with specific version of [librealsense](https://github.com/IntelRealSense/librealsense) 
and a fixed (mostly IMU related) and modified version of [realsense](https://github.com/intel-ros/realsense).
The main goal was to enable visual inertial mapping (e.g. with maplab).
This has been tested with a D435i and T265 on Ubuntu 18.04 melodic.

maplab should now work with the `rs_maplab.launch` file.

### librealsense
 * No changes, Version 2.19.1

### realsense
 * Based on Version 2.2.1
 * Replaces the unite_imu logic, which combines the individual gyro/accel measurements at different frequencies into one IMU message, by interpolating the faster signal onto the slower one.
 * Extends the ROS node interface to allow configuration of:
     * Emitter ON/OFF
     * Format of the image streams (MONO8, MONO16, BGR8, RGB8, etc.)
     * Publishing CameraInfo ON/OFF. Disabling allows certain high-resolution configurations for the streams that do not have intrinsics stored on the sensor. These configurations would otherwise just crash the driver.
     
     
 ## Installation
 
 * Set up a catkin workspace. If you do not know how to do that follow [the instructions of maplab](https://github.com/ethz-asl/maplab/wiki/Installation-Ubuntu#create-a-catkin-workspace).
 * Follow the instructions in these chapters in the intel wiki to prepare your system and dependencies for librealsense.
    * https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md#ubuntu-build-dependencies
    * https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md#prerequisites
      * **Troubleshooting:**
        If the patching of the kernel fails because the videobuf2_core ore uvcvideo module cannot be unloaded, make sure your sensor is unplugged and then try this:
        ```bash
        sudo modprobe -r uvcvideo
        sudo modprobe -r videobuf2_core
        sudo modprobe -r videodev
        ```
         
 * Build librealsense: `catkin build librealsense2`
 * Build realsense: `catkin build realsense2_camera`
 * You will probably need to reboot/logout to make sure the sensor is recognized correctly after the this installation.
