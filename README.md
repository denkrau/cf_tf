# cf_tf
## Introduction
cf_tf_ is a [ROS](http://ros.org) package to read the position in [cob_fiducials](http://wiki.ros.org/cob_fiducials) and send it to crazyflie_driver to control a [Bitcraze Crazyflie](http://wiki.ros.org/crazyflie). The instructions here will refer to the system with the modified crazyflie_controller. For keeping the controller package clean, as is from a third party, launch files and other configuration files are moved to this package.

* Authors: [Xavier Gisbert Beguer](xavigisbeg@gmail.com), [ETIT TU-Darmstadt](http://www.etit.tu-darmstadt.de/fachbereich/)


## Dependencies
The software needed is ROS Indigo and the following packages:

* cob_object_perception_ 
* cob_perception_common
* crazyflie_ros

The dependencies of those individual packages are not written here, and should be installed using "rosdep install" o downloaded in the workspace after checking them with "rosdep check"

## Hardware
The minimum devices needed to use the cf_tf package are:

* Computer with Ubuntu 14.04 and ROS Indigo
* External or internal camera for the computer
* Crazyflie 2.0
* Crazyradio PA
* Custom frames to put the markers in the Crazyflies

The software is tested with Crazyflie 2.0 and Crazyradio PA. Other models may run but are not supported in this moment.

## Usage

Open several terminal windows. On each of them, run:
* roscore
* roslaunch cf_tf cob_uhura.launch (or the name of your launch file for camera node, tud_img_prep and cob_fiducials)
* rostopic hz /fiducials/detect_fiducials 
* rosrun cf_tf cf_tf (can be included into previous launch file)
* roslaunch cf_tf hover_cf_tf<#cf>.launch 
* rqt
* rviz

After running those:
COB_FIDUCIALS:
* Turn debug on, recompile
* Lower the maximum aspect ratio to the angle needs and rise the minimum radius.
* Turn debug off, recompile

TUD_IMG_PREP:
* Tune to the values that work best on certain lightning conditions
* Look for loss of detection in the maximum distance to use, move quickly the marker verti cally and horizontally and check for frame loss, avoid detection of background texture.

CF_TF:
* Place a marker in sight of the camera, press set world in dynamic_reconfigure or call the /setworld service
* Do not press the manual box unless you mean to write the world position yourself. 

CRAZYFLIE_CONTROLLER:
* Press the joystick button for manual sevice to switch to automatic X, Y and Yaw control. Can be done calling the service.
* Check that the PID constants in dynamic_reconfigure are proper.
* Check the goal position in Rviz. Set switch goal to false when you do not control it from dyn_rec.
* If way point navigation will be used, choose stepped or not and the maximum length for the steps if positive.

## Configuration files
The cfg files must be set as executables.

* camera node: cf_tf/data/camera/camera.yaml (your calibration file)
* tud_img_prep: tud_img_prep/cfg/dynamic_param_config.cfg (configure what dynamic_reconfigure will see. Consider setting your final values as default to speed setup)
* Pi-Tag: cf_tf/data/cob_fiducials/piTagIni_0.xml (configure your marker dictionary. Pay attention to the values or the measure will be incorrect)
* cf_tf: cf_tf/cfg/dynamic_param_config.cfg (configure what dynamic_reconfigure will see. Consider setting your final values as default to speed setup)
* crazyflie_controller: cf_tf/data/crazyflie/crazyflie2.yaml(will be overwritten by dynamic reconfigure), crazyflie_controller/cfg/dynamic_param_config.cfg (configure what dynamic_reconfigure will see. Consider setting your final values as default to speed setup), cf_tf/scripts/controller_cf_tf.py (turns the joy buttons into service requests. Manual was added.)


## Extra installation info to ROS newcomers

After downloading or cloning the mentioned packages, probably the user's system does not have all the packages or libraries that all the packages need. This will result in failures when doing "catkin_make" or "catkin build". 
To solve this, there are two possibilities.

* Install the packages in the root partition where ROS is installed (/opt/share/...) executing the command "sudo rosdep install --from-paths /path/to/catkin_ws/src --ignore-src" in the terminal. If it does not install succesfully a dependency you can try to install or update them in the root manually or follow the next step.

* Check the dependencies executing "rosdep install --from-paths /path/to/catkin_ws/src --ignore-src" and download the packages you need to your workspace. This gives you flexibility to change files in those packages without permission problems. 

In any case, you should be able to include in your own package the special files of other packages that you will modify. This is specially the case of configuration yaml files. 

Ideally you should adapt your code to be used along the other packages without any modifications in them, therefore your code will work for other users out of the box, and will benefit of the development and mantainance of those packages. 

Remember also to source your workspace in the ~/.bashrc file.
