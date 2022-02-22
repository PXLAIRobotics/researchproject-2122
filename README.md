# Research Project '21-'22: AI & Robotics
## PADAWANS: Predatory Animal Detection: A Wildlife-protecting A* Navigation System

Teams are provided a Unity simulation environment.
The environment can be used with ROS, using [this Docker container](https://github.com/PXLRoboticsLab/ROS2_Unity).

# Unity simulation
You can download the (standalone) Unity scene in our MS Teams channel (Files > Course Material), or via [this link](https://hogeschoolpxl.sharepoint.com/:u:/s/ResearchProject-AIRobotics/EZcivc9etfVCkYHheYEYRg8Bmm0xlwB8HkP8diUBsRMZCA?e=OOb1ui). A Unity installation is not required to run the simulation.

You should be able to start the simulation by executing ```./build.x86_64```. The build is targetted for Linux systems, so this won't work for Windows. 

*If a Windows version is required, let us know. (but do remember that ROS2 will need to run in Windows as well in that case)*

# Setup
Start the (standalone) Unity scene. You should see the thermal camera of the drone. Use the 'C' button to switch camera's. *(more cameras will be added in next version)*

Build and run the [ROS2 Unity Docker container](https://github.com/PXLRoboticsLab/ROS2_Unity). **Read the documentation first** to understand how it works. Summary: start container, attach to it and start the ./01... script so it connects to your (local) Unity instance. Unity can run on your local host machine, the Docker container should pick this up automatically.

When attaching a second terminal to the Docker container, a ```ros2 topic list``` command should reveal the ROS topics below, because they are exposed by the Unity environment.

# Communication
All info that can be acquired from the scene is available on ROS topics. The user can command the drone in the simulation to move along a defined navigation graph. All details about the graph (node names, node positions, node neighbors / edges) are made available using ROS services.

Below, you can view all details about the available topics and services, along with the commands that you can send to the drone.

## Graph info
*(services eager to share their data at your call)*
* **/graph/nodes**: No data needed in request. Returns list with names of all nodes in the graph. Uses Nodes/NodesRequest/NodesResponse. 
* **/graph/node**: Request must contain a valid node name. Returns details about that node: x,y,z position and a list with names of neighbor nodes. Uses NodeInfo/NodeInfoRequest/NodeInfoResponse.

## Camera's
*(publishers pushing their image data continuously)*
* **/image_raw/compressed**: Normal camera images. Camera attached to drone. Resolution 640x480, FPS dependend on system. (max) Uses CompressedImage.
* **/thermal_cam/compressed**: Thermal camera images. Camera attached to drone. Resolution 320x240, 4 FPS. Uses CompressedImage.

## Drone status
*(more publishers regarding drone status)*
* **/drone/position**: Sends the current position of the drone. Uses Vector3Msg.
* **/drone/nav/src**: If the drone is moving from node A to node B, the **source** is node A. This will be returned by this topic while not arrived. Uses StringMsg.
* **/drone/nav/dst**: If the drone is moving from node A to node B, the **destination** is node B. This will be returned by this topic while no new valid destination is set. Uses StringMsg.
* **/drone/nav/at_node**: Returns true if the drone reached its destination node. False when still flying towards target. Uses BoolMsg.


## Drone commands 
*(listeners waiting for your command)*

### /drone_cmd
*Main topic on which the drone listens to commands. Different command can be sent. Command must always be in String format*

* **PAUSE**: Pauses the drone at current position.
* **RESUME**: Resumes the movement of the drone.
* **TURN_LEFT** / **TURN_RIGHT**: Rotates the drone 45 degrees left or right. Used to look around at current position. Drone will automatically turn towards destination position when flying towards it.
* ***NODENAME***: Instruct the drone to fly towards the node with the specified name. (e.g. "ALPHA", "BRAVO", ...) The drone will only set the node as its destination if it has arrived at its last destination node AND the specified node is directly connected to the current location node where the drone is located. In other scenarios, the command will be ignored. Single exception is when the drone is flying from node ALPHA to node BRAVO and we instruct it to fly back to A. In this case, the drone will turn around and fly back to ALPHA when instructed to do so.
