# Introduction to ROS!

Please follow these steps in class to get your ROS workspace running. Please note that this tutorial assumes familiarity with python and basic linux commands.


### Creating Directory

We will place all our ROS files in a workspace. To create the workspace, open a terminal and run following commands-
```
mkdir -p ~/perception_ws/src 
cd ~/perception_ws 
catkin_make
```
Here we have created a workspace called perception_ws. `catkin_make` command is used to build and compile the packages in the workspace. 

### Sourcing the workspace
Source the workspace using following commands-

```
source devel/setup.bash
```
To **source a workspace** in ROS means to update your shell environment so that it recognizes the packages, libraries, and tools available in a specific ROS installation or workspace. The second command basically automates this process and source your ws every time.

### Build ROS package

Now we are ready to build our first package-

```
cd ~/perception_ws/src 
catkin_create_pkg circle_turtle rospy std_msgs geometry_msgs
```
ROS packages are the units which contain your projects. It will be the directory which will contain all the scripts like launch files and configuration files. They also contain all your source codes. 
```rospy``` : Python client library for ROS.
```std_msgs``` : Contains simple message types like String and Int32.
```geometry_msgs``` : Defines message types used to describe geometric data such as points, vectors, poses, twists, and transformations.

### Running a Subscriber Node

Now that we have our package ready, we will first start a standard node called turtlesim that comes with ROS. To start the node, first launch a new terminal window and run-

```
source /opt/ros/noetic/setup.bash
roscore
```
You must have a roscore running for the nodes to communicate with each other. After running roscore, open a new terminal window and run-

```
source /opt/ros/noetic/setup.bash
rosrun turtlesim turtlesim_node
```
If you have followed the above instructions correctly, you should be seeing a small screen with a turtle in the center. 
Our aim in this tutorial is to move the turtle in a circle.

### Making a Publisher

Now we will write our publisher in python. Open a new terminal window and run-

```
source /opt/ros/noetic/setup.bash
cd ~/perception_ws/src/circle_turtle
mkdir scripts
touch scripts/make_circle.py
chmod +x scripts/make_circle.py
```

`chmod +x` will make the python script executable.

Open the make_circle.py file in an editor and copy this code-

```
#!/usr/bin/env python3

import rospy
from geometry_msgs.msg import Twist

def move_in_circle():
    # Initialize the ROS node
    # This initializes the node with a name. This name can be anything you want.
    rospy.init_node('circle_turtle', anonymous=True)

    # Here we define the publisher that will publish to the topic: /turtle1/cmd_vel
    # In ROS, the topics are created dynamically. What it means is when there is a publisher that is publishing to a topic and when there is subscriber listening to it, the topic is dyanmiccaly created.
    # Twist here tells the type of msg the topic will contain.
    pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)
    rate = rospy.Rate(10)  # 10 Hz

    # Define the Twist message. Twist is a standard msg type in ros. You can read about Twist here: http://docs.ros.org/en/noetic/api/geometry_msgs/html/msg/Twist.html
    vel_msg = Twist()
    vel_msg.linear.x = 1.0  # Linear speed
    vel_msg.angular.z = 1.0  # Angular velocity

    rospy.loginfo("Turtle is moving in a circle...")

    while not rospy.is_shutdown():
        # Publish the velocity using the publisher we defined.
        pub.publish(vel_msg)
        rate.sleep()

if __name__ == '__main__':
    try:
        move_in_circle()
    except rospy.ROSInterruptException:
        pass
```

### Launching the Publisher 

Now we are done with the code, so let's launch the publisher. Before launching we need to compile the code that we have written.

```
cd ~/perception_ws
catkin_make
```

Finally, run the publisher-
```
source devel/setup.bash
rosrun circle_turtle make_circle.py
```
