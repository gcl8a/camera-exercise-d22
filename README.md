# Lab exercise: Using AprilTags for Navigation
This is your third group assignment. Please follow the instructions below to receive full points. It is important that you create > 10 commits for each demonstration. Be sure to create commits for each part of your process so we can see what you did. Individual and group grades may vary.

## Overview
Upon completion of this activity, you will have a robot that can detect [AprilTags](https://april.eecs.umich.edu/software/apriltag) and use them for basic navigation. 

Starting with your wall following code from the last exercise, you will first _individually_ program your robot so that it follows a wall until it detects an AprilTag, at which point it will stop. Then, working as a group, you will program the Romi to detect and approach a tag to a specified distance.

## Background and preparation
The [OpenMV](https://openmv.io) camera is a low-cost, extensible, machine vision module designed to "bring machine vision algorithms closer to makers and hobbyists" -- OpenMV strives to be the "Arduino of computer vision." Though we often refer to it simply as a "camera," the OpenMV breakout includes not only the camera, but also a powerful on-board processor. In addition, the OpenMV IDE  includes a pile of example codes to do everything from face detection to optical flow to much more. Though we won't be using the functionality here, the creators have even tied it in with the [Edge Impulse](https://edgeimpulse.com) machine learning engine to do powerful image recognition. You are encouraged to load some of the sample programs onto the device and try them out!

[AprilTag](https://april.eecs.umich.edu/software/apriltag) is "a visual fiducial system, useful for a wide variety of tasks including augmented reality, robotics, and camera calibration." One can easily print a variety of AprilTags and use them as landmarks for navigation or for [detecting other robots](https://april.eecs.umich.edu/media/apriltag/apriltagrobots_overlay.jpg). In your final project, your Romi will register AprilTags as it drives around the arena, which will provide cues to help it navigate.

The OpenMV camera is programmed using [MicroPython](http://micropython.org), a variation of Python3 that is optimized for running on microcontrollers. We have modified the OpenMV example scripts to allow the camera to communicate with your robot -- there is a version that communicates via the UART and another that uses I2C. Your team will explore both versions. (We'll give you a non-functional bonus point if you can tell us why we didn't we create an SPI version.)

For these exercises, you will need to mount the camera on your Romi using one of the camera mounts provided. The wiring will depend on which example program you choose to use. There are instructions for both wiring and implementing the library in the repository's readme's.

You will also need to install the OpenMV IDE; versions for all three of the major operating systems can be found on the [OpenMV](https://openmv.io) website.

### Preparing your previous repos
In your previous group assignment you worked on a wall follower. In that assignment you used a PI controller to control speed and a PD controller to control distance. In this assignment you will add the camera module. Your Romi will follow the wall at a specified distance and velocity, until it detects an AprilTag on the wall. Therefore, you will need to reuse code from your previous group repositories. You have two options: 1) you copy and paste code from last week's repo to this one (not preferred) or 2) you modify (clean up) your previous repos and include them as libraries in the repository for this assignment. If you choose the latter option, you will receive an additional 10% to your overall grade for your group assignment. 

#### Option 1: copy/paste
For this option, simply copy the files of interest into this repository. 

#### Option 2: cleaning up repositories and including them as library
You are continuing to read, which implies that you are considering adding your previous code via libraries to this new repository. First, you need to go back to your previous assignment (i.e., the wall follower code) and restructure your code into classes. Once your code is structured as classes, it can be easily used by creating objects of your classes, and you can include it in this repository. Do so by adding the repository address to your `lib_deps` in the `platformio.ini` file, as shown below.

```
lib_deps =
	wpiroboticsengineering/wpi-32u4-library@2.8.0
	https://github.com/D22-RBE2002/group-assignment-velocity-control-alpha-super-cool-dynamite-wolf-squadron
```
Now you can access your code by using standard include statements,
```
#include <myAwesomeWallFollower.h>
```

## Step 0: decide who will use which communication protocol
Each student will implement one of the communication protocols to connect the camera and the Romi. At least one person from each team must implement the UART interface and one person the I2C -- part of this exercise is to compare the two protocols and justify your decision for one over the other.

## Step 1: each team member: clone this repository to your computer
Clone this repository to your local machine.
```
git clone <PASTE THE REPO ADDRESS HERE>
```

Clone [WPI's AprilTag library](https://github.com/WPIRoboticsEngineering/openmv-apriltags) to your computer. The repository contains AprilTag library files and sample code for _both_ the Romi and the OpenMV camera.

## Step 2: create files and branches
As you have done in previous exercises, create a personal file with your name and id and your own branch.

## Step 3: each team member: load one of the example scripts onto the OpenMV camera
Open the OpenMV IDE and the example python script of your choice (either the I2C or UART version) from the AprilTag repository. Connect the camera to your computer with a USB cable and, following instructions on the [OpenMV website](https://openmv.io), upload the script to the camera. Open the Serial Monitor in the OpenMV IDE and hold an AprilTag in front of the camera. It should print off information about the tag.

## Step 4: connect the camera to the Romi
Disconnect your computer from the OpenMV camera, first by stopping the program, then by pressing the disconnect button in the IDE. Remove the USB cable. **Make sure that no power is going to either the camera or the Romi while you are wiring them together.**

Following the instructions in the repository, make three connections: two wires for the communication bus of your choice and one for `GND`. We'll add a power connection later. You may need to move connections made in previous exercises to free up the correct pins.

Note that the camera is 5V tolerant, so there is no problem sending a 5V signal from the Romi to the camera. Is a 3.3V signal from the camera enough to trigger a HIGH reading on the Romi's 32U4 chip? (In case you haven't memorized the entire document yet, you can find the answer on page 383 of the [datasheet](https://ww1.microchip.com/downloads/en/devicedoc/atmel-7766-8-bit-avr-atmega16u4-32u4_datasheet.pdf)).

## Step 5: each team member: open one of the cloned examples in VS Code
Re-open the OpenMV IDE, load the script again, and verify that you can still read a tag. In the next step, you will save the program in a way that it isn't lost between power cycles, but when you do that, you can't use the USB connection for debugging, and it is important to verify functionality first.

Open the corresponding example code from the AprilTag repository in VSCode. Upload it to the Romi. Open the Serial Monitor in platformio and verify that _the Romi_ can detect the tag.

## Step 6: "headless" camera operation
As described [here](https://docs.openmv.io/openmvcam/tutorial/openmvide_overview.html#tools), save the open python script as `main.py` on the camera. **Disconnect all power from both devices** and add a connection from 5V on your Romi to the `VIN` pin on the camera. Have someone independently check your wiring. Reconnect the USB to the Romi, which should also power the camera. Verify that you can still detect the tag.

## Step 7: Wall following with cues
Starting with your wall following code from last week, create code in *this* repository so that your robot follows a wall at 30cm offset and monitors the wall for a given AprilTag, which will be mounted on the edge of the arena. When the robot detects the tag, it must stop and enter an idle state. You may not use a simple `while()` loop, but you must add to the basic state machine from last week. The state machine must be programmed such that the button can be pressed again to restart the line following.

Notet that you have two options for including the AprilTag library:

1) You can include the library through `lib_deps`, much as you have done before, or
2) Since you have cloned the AprilTag repo to your machine, you can include the local version using `lib_extra_dirs`, for example,

```
lib_extra_dirs =
    <path to local directory where you cloned the repo
```
The advantage of this method is that you can edit the library files if you wish. The downside is that each individual will have a different local directory, so you will need to add `platformio.ini` to your `.gitignore` file. 

[SIGN-OFF: 1]

Create a release of your code in GitHub and call it: `demo 1 YOUR-NAME`. As tag, enter 1.0.X.

## Step 8: As a group, decide which communication method you will use
Discuss the pros and cons of each of the communication methods. Choose which method your team will use for the group activity. You are expected to make a _positive statement_ about your choice in your commit message.

A good statement would be something like, "We chose to use the ultrasonic sensor because it has longer range and was shown to be more reliable in experiments."

A poor statement would be something like, "It worked when we first tried it, so we went with that." Alternatively, "Group member A was the last to use the robot, so we just left it as is," would not give us confidence that you are actively choosing the best method.

## Step 9: As a group, program your robot to track a tag
Program your robot to find an approach an AprilTag. To demonstrate functionality, course staff will put your robot in a random position near the center of the arena and your robot will have to find and drive towards a tag after the press of a button. The robot must stop when it comes within 30 +/- 5 cm of the tag, but note that it could approach the tag from any angle, so you'll have to be thoughtful in how you calculate distance. Note that your robot may not be able to see the tag from its starting orientation. You will need to build on your state machine from previous exercises. Be sure to draw out a proper state transition diagram.

To complete the exercise, start a [live session](https://code.visualstudio.com/learn/collaboration/live-share) via Visual Studio Code, and ensure that all team members are present. When using live share and commiting code as a group, author attributions should be automatically added in the VSCode 'Source Control' menu. If they do not show up when you perform a test commit, add them manually (*We will not be able to tell who worked on each commit UNLESS you state so in the commit message. This is important for receiving full points for your individual contribution*).

Prepare Demo 2 in which you showcase your Romi to a SA (i) searching for and detecting a tag, and (ii) driving towards the tag and stopping when it reaches it. The SA will move the tag and press the button again.

[SIGN-OFF: 2]

Create a release of your code in GitHub and call it: `demo 2 YOUR-GROUP-NAME`. As tag, enter 2.0.0.

