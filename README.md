# RealSense for VI-Mapping

This repo contains submodules with specific version of [librealsense](https://github.com/IntelRealSense/librealsense) 
and a fixed (mostly IMU related) and modified version of [realsense](https://github.com/intel-ros/realsense).
The main goal was to enable visual inertial mapping (e.g. with maplab).
This has been tested with a D435i and T265 on Ubuntu 18.04 melodic.

# T265 (newest releases 05.09.2019, not tested for maplab yet)

Use the `devel/T265_latest` branch, the built-in pose estimation should work with the `rs_t265.launch` file. For maplab you can use the same launch file, however, we have encountered difficulties running ROVIOLI with the fisheye cameras. The best model that is supported by ROVIO is the pinhole-equi model, but the double-sphere model might be a better choice. Therefore the sensor is not working well with maplab out of the box and needs some more work.

### librealsense (SDK)
 * Official intel release: `v2.28.0`

### realsense-ros (ROS Wrapper)
 * Official intel release 2.2.8

# T265 (fixed older version, tested with maplab)

Use the `devel/T265` branch, the built-in pose estimation should work with the `rs_t265.launch` file. For maplab you can use the same launch file, however, we have encountered difficulties running ROVIOLI with the fisheye cameras. The best model that is supported by ROVIO is the pinhole-equi model, but the double-sphere model might be a better choice. Therefore the sensor is not working well with maplab out of the box and needs some more work.

### librealsense (SDK)
 * No changes, `development` branch

### realsense (ROS Wrapper)
 * Based on Version 2.2.3
 * Remove accelerometer factor that is used to scale the accelerometer based on the first measurement.

# D435i

Use the `master` branch, maplab should now work with the `rs_maplab.launch` file.

### librealsense (SDK)
 * No changes, Version 2.19.1

### realsense (ROS Wrapper)
 * Based on Version 2.2.1
 * Replaces the unite_imu logic, which combines the individual gyro/accel measurements at different frequencies into one IMU message, by interpolating the faster signal onto the slower one.
 * Extends the ROS node interface to allow configuration of:
     * Emitter ON/OFF
     * Format of the image streams (MONO8, MONO16, BGR8, RGB8, etc.)
     * Publishing CameraInfo ON/OFF. Disabling allows certain high-resolution configurations for the streams that do not have intrinsics stored on the sensor. These configurations would otherwise just crash the driver.
     * Remove accelerometer factor that is used to scale the accelerometer based on the first measurement.
     
     
 ## Installation
 
 * Set up a catkin workspace. If you do not know how to do that follow [the instructions of maplab](https://github.com/ethz-asl/maplab/wiki/Installation-Ubuntu#create-a-catkin-workspace).
 * The installation involves the following steps:
    * First you need to install some prerequisites for the SDK and the kernel modules
    * Patch the kernel modules
    * Build librealsense2 SDK
    
    The steps above are described in the librealsense [install instructions](https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md), make sure you follow the instructions for the version of librealsense you are trying to install (i.e. the one that is in this repo's submodule). There is also the option to install the SDK and kernel modules using apt, please follow the install instructions linked above if you would like to try that.
    
    * Build ros driver: `catkin build realsense2_camera`, if there are some issues with this driver, checkout the [install instructions on the repo](https://github.com/IntelRealSense/realsense-ros)
 * You will probably need to reboot/logout to make sure the sensor is recognized correctly after the this installation.
 
 
## Troubleshooting
* Sometimes your realsense will not be recognized as a USB3 device. This can happen if the USB cable is connected too slowly and the OS already decideds that this is a USB2 device, before all USB pins are in contact with the socket. It sounds like a joke, but plugging in the USB connectors with one fast motion is preventing this.
* If the patching of the kernel fails because the videobuf2_core ore uvcvideo module cannot be unloaded, make sure your sensor is unplugged and then try this:
```bash
sudo modprobe -r uvcvideo
sudo modprobe -r videobuf2_core
sudo modprobe -r videodev
```
* If patching the kernel module fails (e.g. uvcvideo, or videodev) it might be caused by UEFI safe boot enabled on your system, which prevents these changes. Disable it in the BIOS menu and retry the installation
 * When switching branches in this repository, the submodules might change both their subfolder name and the remote. The easiest solution is simply to remove and clone this repo again, switch to the branch you want and only then initialize and update the submodules. Alternatively, there is a script in the devel/T265_latest branch that might help with updating the submodules.

## Running the driver

 - Source the workspace
 
 - **T265**
    ```
    roslaunch realsense2_camera rs_t265.launch
    ```
      - **Note:** In order to get unified gyro/accel measurements, change the following line in the launch file:
        ```diff
        -  <arg name="unite_imu_method"      default=""/>
        -  
        +  <arg name="unite_imu_method"      default="linear_interpolation"/>
        +
        ```
