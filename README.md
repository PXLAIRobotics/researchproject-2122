# Research Project '21-'22: AI & Robotics
## PADAWANS: Predatory Animal Detection: A Wildlife-protecting A* Navigation System

Teams are provided a Unity simulation environment.
The environment can be used with ROS, using [this Docker container](https://github.com/PXLRoboticsLab/ROS2_Unity).

All info that can be acquired from the scene is available on ROS topics. The user can command the drone in the simulation to move along a defined navigation graph. All details about the graph (node names, node positions, node neighbors / edges) are made available using ROS services.

Below, you can view all details about the available topics and services, along with the commands that you can send to the drone.

# Graph info
*(services eager to share their data at your call)*
* **/graph/nodes**: No data needed in request. Returns list with names of all nodes in the graph. Uses Nodes/NodesRequest/NodesResponse. 
* **/graph/node**: Request must contain a valid node name. Returns details about that node: x,y,z position and a list with names of neighbor nodes. Uses NodeInfo/NodeInfoRequest/NodeInfoResponse.

# Camera's
*(publishers pushing their image data continuously)*
* **/image_raw/compressed**: Normal camera images. Camera attached to drone. Resolution 640x480, FPS dependend on system. (max) Uses CompressedImage.
* **/thermal_cam/compressed**: Thermal camera images. Camera attached to drone. Resolution 320x240, 4 FPS. Uses CompressedImage.

# Drone status
*(more publishers regarding drone status)*
* **/drone/position**: Sends the current position of the drone. Uses Vector3Msg.
* * **/drone/nav/src**: If the drone is moving from node A to node B, the **source** is node A. This will be returned by this topic while not arrived. Uses StringMsg.
* * **/drone/nav/dst**: If the drone is moving from node A to node B, the **destination** is node B. This will be returned by this topic while no new valid destination is set. Uses StringMsg.
* * **/drone/nav/at_node**: Returns true if the drone reached its destination node. False when still flying towards target. Uses BoolMsg.


# Drone commands 
*(listeners waiting for your command)*

COMING SOON


