# CarND-Semantic-Segmentation-Project - Jose C. Marti
## Self-Driving Car Engineer Nanodegree Program.

### Overview
This is the final project of the Self-Driving Car Engineer Nanodegree Program. This is an individual contribution not done as a part of a team (even though I have got some support from other colleagues).

### Description
The project introduces us into the ROS operating system. The project is considered to include all the accumulated knowledge during the 3 terms of the Nanodegree Program. 
The goal is to understand and implement the core functionality of an autonomous vehicle system using the Robot Operating System and testing in onto the Udacity Simulator. Unfortunately, I do not expect to test my code in Carla, the real autonomous car from Udacity.

![pic](final-project-ros-graph-v2.png)

Several nodes have to be updated:
#### Waypoint Updater Node
In this node, the obstacles and the traffic lights are considered in order to set the velocity for each waypoint. This node then publishes the next number of waypoints that are closest to vehicle's current location and are ahead of the vehicle.

#### Traffic Light Detection Node
A crucial part of the vehicle’s self-driving capabilities comes from the ability to detect and classify upcoming traffic lights. 
This node processes images provided by the vehicle’s onboard camera and publishes upcoming traffic light information to the previous described node. The Waypoint Updater node uses this information to determine when to stop or slow down the car in when facing a red light. 
Traffic light detection takes a captured image as input and produces the bounding boxes as the output to be fed into the classification model. 
I used TensorFlow Object Detection API. This is an open source framework built on top of TensorFlow to construct, train and deploy object detection models. A single shot localizer-classifier solution was chosen for the project.

#### DBW (Drive-By-Wire) Node
This node finally calculates throttle, brake and steering angle to follow longitudinal and lateral trajectory simultaneously. The throttle, brake and steering angle is calculated within this node. A PID controller was used to calculate throttle and brake based on the difference between the current velocity and the target velocity. another PID controller was also used based on cross-track error to calculate the appropriate steering angle.

### Conclusions
I had many issues during compilation. The folder /home/workspace/CarND-Capstone/ros/src/dbw_mkz_msgs to be included in your project in order not to have the following error:

```
Make Warning at /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:76 (find_package):
Could not find a package configuration file provided by "dbw_mkz_msgs" with any of the following names:
    dbw_mkz_msgsConfig.cmake
    dbw_mkz_msgs-config.cmake
```
In addition to that, the following error appeared:
```
File "/opt/ros/kinetic/lib/python2.7/dist-packages/catkin/terminal_color.py", line 2, in <module> from catkin_pkg.terminal_color import *  # noqa
ImportError: No module named terminal_color
```
apparently the ```terminal_color.py``` file was not in the correct location. So, after googling for a while I found a couple of forums where it was explained that the Python module in question has recently been moved from ```catkin``` into ```catkin_pkg``` and recommended to copy the content of [this file](https://github.com/ros-infrastructure/catkin_pkg/blob/master/src/catkin_pkg/terminal_color.py#L119) in to the abovementioned ```terminal color.py``` file in order to get the compilation working.

* https://answers.ros.org/question/294780/ubuntu18-and-ros-melodic-catkin-error-importerror-no-module-named-terminal_color/
* https://answers.ros.org/question/301702/ros-kinetic-importerror-no-module-named-terminal_color/?answer=301712#post-id-301712

Finally, I also noticed that even in the Workspace with GPU from Udacity the refresh rate in the simulator when the camera is activated is low so, I decided to change the code in bridge.py to configure the camera to send only a frame every 6 to reduce latency.

```EVERY_NTH_IMAGE = 6
...
    def publish_camera(self, data):
        if self.n == 0:
           imgString = data["image"]
           image = PIL_Image.open(BytesIO(base64.b64decode(imgString)))
           image_array = np.asarray(image)
           image_message = self.bridge.cv2_to_imgmsg(image_array, encoding="rgb8")
           self.publishers['image'].publish(image_message)
           #print((rospy.get_time(), "image"))

        self.n += 1
        self.n = self.n % EVERY_NTH_IMAGE
 ```
_(Thanks to Aleksandr Likhterman)_

In my personal opinion, this has been the most complex project along all the course, not only due to the complexity of the system to implement, but also due to the fact that a new operating system (ROS) is used, and high HW requirements are needed to smoothly test the result in the simulator. Thankfully we have the Walkthroughs and the support of the community that help us to get ideas and move forward every time we get stuck in the project. 

Finally, I would like to remark that ROS has been a surprising discovery for me and with no doubt I am going to continue reading and learning in order to integrate it in my hobby future projects.

J.Marti



















-------------------



This is the project repo for the final project of the Udacity Self-Driving Car Nanodegree: Programming a Real Self-Driving Car. For more information about the project, see the project introduction [here](https://classroom.udacity.com/nanodegrees/nd013/parts/6047fe34-d93c-4f50-8336-b70ef10cb4b2/modules/e1a23b06-329a-4684-a717-ad476f0d8dff/lessons/462c933d-9f24-42d3-8bdc-a08a5fc866e4/concepts/5ab4b122-83e6-436d-850f-9f4d26627fd9).

Please use **one** of the two installation options, either native **or** docker installation.

### Native Installation

* Be sure that your workstation is running Ubuntu 16.04 Xenial Xerus or Ubuntu 14.04 Trusty Tahir. [Ubuntu downloads can be found here](https://www.ubuntu.com/download/desktop).
* If using a Virtual Machine to install Ubuntu, use the following configuration as minimum:
  * 2 CPU
  * 2 GB system memory
  * 25 GB of free hard drive space

  The Udacity provided virtual machine has ROS and Dataspeed DBW already installed, so you can skip the next two steps if you are using this.

* Follow these instructions to install ROS
  * [ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) if you have Ubuntu 16.04.
  * [ROS Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu) if you have Ubuntu 14.04.
* [Dataspeed DBW](https://bitbucket.org/DataspeedInc/dbw_mkz_ros)
  * Use this option to install the SDK on a workstation that already has ROS installed: [One Line SDK Install (binary)](https://bitbucket.org/DataspeedInc/dbw_mkz_ros/src/81e63fcc335d7b64139d7482017d6a97b405e250/ROS_SETUP.md?fileviewer=file-view-default)
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

### Docker Installation
[Install Docker](https://docs.docker.com/engine/installation/)

Build the docker container
```bash
docker build . -t capstone
```

Run the docker file
```bash
docker run -p 4567:4567 -v $PWD:/capstone -v /tmp/log:/root/.ros/ --rm -it capstone
```

### Port Forwarding
To set up port forwarding, please refer to the [instructions from term 2](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/16cf4a78-4fc7-49e1-8621-3450ca938b77)

### Usage

1. Clone the project repository
```bash
git clone https://github.com/udacity/CarND-Capstone.git
```

2. Install python dependencies
```bash
cd CarND-Capstone
pip install -r requirements.txt
```
3. Make and run styx
```bash
cd ros
catkin_make
source devel/setup.sh
roslaunch launch/styx.launch
```
4. Run the simulator

### Real world testing
1. Download [training bag](https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/traffic_light_bag_file.zip) that was recorded on the Udacity self-driving car.
2. Unzip the file
```bash
unzip traffic_light_bag_file.zip
```
3. Play the bag file
```bash
rosbag play -l traffic_light_bag_file/traffic_light_training.bag
```
4. Launch your project in site mode
```bash
cd CarND-Capstone/ros
roslaunch launch/site.launch
```
5. Confirm that traffic light detection works on real life images
