# kidbright_ros

Install gudev in ROS Medolic

1. Download the `python-gudev` package from [here](https://launchpad.net/ubuntu/+archive/primary/+files/python-gudev_147.2-3_armhf.deb).
    Install it by using `dpkg`.
    ```
    $ sudo dpkg -i <filepath>.deb
    $ sudo apt install -f


```

sudo pico /etc/ros/kinetic/linorobot.d/minimal.launch

<launch>
	<node pkg="rosbridge_server" type="rosbridge_websocket" name="rosbridge_websocket"/>
	<node pkg="web_video_server" type="web_video_server" name="web_video_server"/>
<!--<node pkg="kidbright" type="cam_stream.py" name="cam_stream"/>-->
    <!-- Start ROS communication between the robot's computer and Linorobot base -->
    <node pkg="rosserial_python" name="rosserial_lino" type="serial_node.py" output="screen">
        <param name="port" value="/dev/linobase" />
        <param name="baud" value="57600" />
    </node>

    <!-- IMU Relay and Filter -->
    <include file="$(find linorobot)/launch/include/imu/imu.launch" />

    <!-- Publish Linorobot odometry -->
    <node pkg="linorobot" name="lino_base_node" type="lino_base_node"></node>

    <!-- Publish static transform from base_footprint to base_link -->
    <node pkg="tf2_ros" type="static_transform_publisher" name="base_footprint_to_base_link" args="0 0 0.098 0 0 0  /base_footprint /base_link"/>

    <!-- Odom-IMU Extended Kalman Filter-->
    <node pkg="robot_localization" type="ekf_localization_node" name="ekf_localization"> 
        <remap from="odometry/filtered" to="odom" />
        <rosparam command="load" file="$(find linorobot)/param/ekf/robot_localization.yaml" />
    </node>
    <!-- Ros_bridge -->
</launch>


sudo pico /usr/sbin/linorobot-start

sleep 2
rosrun kidbright cam_stream.py &



