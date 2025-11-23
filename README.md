## Excavator Object Detection Pipeline

This project provides an object detection and depth estimation pipeline for an excavator system using ROS 2 (Jazzy). The pipeline involves running the camera and depth nodes, 
along with debugging tools for inspecting topics and node communications.

## Prerequisites

ROS 2 Jazzy installed on your system.
Workspace built with colcon (assuming install/setup.bash exists).

## Running the pipeline 

1. Launch the Camera Publisher
```bash
cd excavator_ws
source /opt/ros/jazzy/setup.bash
source install/setup.bash
ros2 run camera_publisher camera_publisher
```
This will start both cameras (left and right cameras that are required for stereo vision depth estimation) and would publish data on two individual channels (one channel for each camera) 
, '/left_camera/image_raw' and '/right_camera/image_raw'. Frames captured from both cameras are transmitted on these channels at a rate of 30 frames per second.

2. Launch the Depth Node

Open a second terminal and run:

```bash
cd excavator_ws
source /opt/ros/jazzy/setup.bash
source install/setup.bash
ros2 run depth_node depth_node

```
This node 'depth_node' subscribes to the channels  '/left_camera/image_raw' and '/right_camera/image_raw' in order to have access to incoming pairs of simultaneous frames captured from left
and right camera. This node applies an already trained model YOLOv8l on both frames, rectifies the frames (using the parameters obtained from the calibration of the cameras). Then, having these
images calibrated we can estimate the distances to the traffic signs detected in both frames by selecting as target points the centres of the detected traffic signs in both frames. Having two centre 
coordinated, we can apply the triangulation and compute the distance to the traffic signs. 

## Debugging

For troubleshooting non-communicating nodes, you can use the following commands:

- List all active topics:

```bash
ros2 topic list

```

- Inspect data being published on a topic:
```bash
ros2 topic echo <topic_name>

```
- Get information about a node/topic:

```bash
ros2 info <topic_name>

```
This provides details such as the topic it publishes on and which nodes are subscribed to it.

## Handling Package Version Conflicts

If you encounter conflicts with package versions (especially while running depth_node), it is recommended to create a separate Python environment and install 
the corresponding package versions:

```bash
python3 -m venv ~/excavator_env
source ~/excavator_env/bin/activate
pip install -r requirements.txt  # or install specific packages manually
```
Then, retry launching the nodes within this environment.
  
