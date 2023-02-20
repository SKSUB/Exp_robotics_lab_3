# Exp_robotics_lab Assignment3
## INTRODUCTION
Purpose of this repository is to develop the assignment 3 of Experimental Robotics Laboratory. In this assigment the robot moves between different rooms in the Gazebo simulation. The rooms has aruco markers, robot reads the marker and collects the hint. The robot navigate in the simulation with the help of slam gmapping and move base navigation with the help of laser and fixed goals. And checks for consistency and complete solution. And return to oracle room to check for solution. For the correct solution the simulation ends or the robot navigates again to other rooms from the first.

## SOFTWARE ARCHITECTURE
In order to better understand the software architecture we can utilise the UML component diagram

![cluedo_mapping](https://user-images.githubusercontent.com/82164428/219997455-ae5f1b28-13f5-4b94-b0a2-33d4bc6ebafd.jpg)

The state machine is the main node of the architecture effectively communicates with all other nodes to run the simulation. It request the Oracle node for hint and asks the solution. It sends the move_base action goal to move the robot in the move state. It receives all the IDs from the aruco ros via /marker id. 

And it defines the place to initiate the scene. And it communicate with the ontology interface for adding, updating and consistency check via add_hint service, update_request service and check_consistency service.  

The ontology interface node performs all the querying operation with the armor service with the help of myArmor class.

Movearm node effectively moves the arm no matter the state of the simulation to read the marker ID. Aruco_ros publish the ID to when the aruco marker detected on the camera. 

Oracle node generates the hint and solution of the simulation. The gmapping and Move base navigation to localise the robot and move the robot in the simulation with the help of laser and move base action goal. 

Now lets look at the ros workspace, workspace has four directories. Aruco_ros, moveit_config, erl2 and exp_assignment_3.

Moveit_config folder is generated by the moveit setup assistant and the troubleshooting steps are carried to effectively run the simulation. 

aruco_ros folder has predefined package, miner alteration is done to publish the marker ID. More details in this link : https://github.com/pal-robotics/aruco_ros.

exp_assignment_3 folder is already provided and only the dependencies are added.

### erl2:
The folder has all the developed scripts to run the simulation

### Scripts:
### state_machine.py 
This is the main node of the architecture, it implements the finite state machine. The robot will be in the one of these three states, (move, check consistency and solution).
MOVE state: It is moving state class, in which the robot is move from one room to another in the continuous to collect all the hints. It checks whether the robot should move in a oracle room or other room to check the soution or to collect the hints. 
CHECK CONSISTENCY state: In this state all the aruco IDs are received and the corresponding hints are collected from the /oracle_hint. Then it stores the hints and check for consistency and if they are consistent it ask the robot to move to the oracle room to check the solution or it ask to move to other room otherwise and also remove the hint from the ontology if they are inconsistent.
SOLUTION state: This is the state where the robot checks the solution received via /oracle_solution service, if the solution is correct the game ends or the game is continues i.e to the move state.

### ontology_interface.py
This node recieves the hint from the state_machine node node and add them. And it checks the received hint is whether consistent and complete. When the recieved solutio is consistent and complete it gives the solution ID to the /ask_solution, otherwise removes them from the onotology. 

### myArmor class:
This is the class method to communicate with the Armor service. It is based on the message format to be communicated with the Armor service.

### place class:
This is the class to create a place object.

### src:
### movearm.cpp:
This node implements the MovearmActionInterface: the corresponding behaviour for the movearm action defined with the moveit setup assistant.

### URDF:
This folder all the descriptions of the robot also has the URDF generated by the move it.
     
### WORLD:
Contains the description for the simulation.

The srv and msg folder has all the custom messages for services and messages respectively.

Launch folder has the gmapping.launch to launch the the SLAM and move_base navigaiton packages. simulation.launch to launch the simulation and the nodes.launch all the nodes to effectively run the simulation.

Config folder has the rviz configuration and all the motor configurations.

Param folder has all the parameters for the ros SLAM  and navigation. 

The cluedo_onotolg.owl initial scene setup. And the simulation.sh for launching the simulation with the aid of genome terminal.

### OTHER PACKAGES 
ARMOR SERVICE (More details in https://github.com/EmaroLab/armor)

MOVE_BASE (More details in https://wiki.ros.org/move_base)

GMAPPING (More details in https://wiki.ros.org/gmapping)

ROS NAVIGATION (More details in https://wiki.ros.org/navigation?distro=noetic)

MOVEIT (older version)  

In order to understand the workflow of simulation here is the temporal diagram:

![cluedo_mapping_temp](https://user-images.githubusercontent.com/82164428/220025431-c41aa52e-1fad-4e35-9229-17c79f47169c.jpg)

## INSTALLATION AND RUNNING:
In order to run the simulation clone this repostitory into your workspace. If you are running on the docker machine of the course no need to clone other packages. Otherwise clone all the packages to the workspace.

    git clone https://github.com/SKSUB/Exp_robotics_lab_2
    
After cloning usual procedures of building the package, and install the dependencies based on the error.

    catkin_make
    
In order to start the simulation:

    ./simulation.sh

It starts all the necessry nodes to simulate the simulation of the game.

Following are the pictures of the simulation

![simulation intitiliased](https://user-images.githubusercontent.com/82164428/220026738-c8e27d49-772c-4d0d-aa6d-9df574833163.png)

Simulation is initialised in the above picture 

![all the nodes intialised with the ontology](https://user-images.githubusercontent.com/82164428/220026896-c1fcedf1-a531-4fad-8ad8-68d411eaf0ce.png)

All the nodes are launched and the onbtology is loaded to the simulation

![Screenshot 2023-02-20 022835](https://user-images.githubusercontent.com/82164428/220027027-65bb8eef-0fc4-41b4-8366-9c6e93707c3b.png)

Autonomous navigation of the robot based on the goal.

In order to better visualise the nodes and the software architecure, here is the rqt_graph:

![rqt_graph](https://user-images.githubusercontent.com/82164428/220027325-dee34e37-fcb1-4466-988c-2f10d4b69d1b.png)

## SYSTEM WORKING AND LIMITATIONS
The system works as a finite state machine, the robot will be in one of the three states; Move, check consistency or solution checking. Based on the state corresponding actions are executed. Robot will be navigating the environment based on the goal. And comes to oracle to check the solutio to end the game.

This system is time consuming in the aspect of robot movement. Robot covers lots of distance in this simulation. It is slow process. And also the random hint genration process even more slower. And has usual miss firing of the nodes will lead to simulation failure. Sometimes the navigation of the robot stops and also there is stuck in th simulatio due to overconsuming of the main simulation node. And also the marker postion are send and need to wait to get the hints and also there are more time to check the consistency and solution chekcing. And also some nodes been killed due to wait times in the machine. 

In order to overcome the issue, the nodes are monitored and the parameters are altered to speed up the simulation process.


## CONTACTS

SATHISH KUMAR SUBRAMANI. ROBOTICS ENGINEER, UNIGE. 4847560@studenti.unige.it





