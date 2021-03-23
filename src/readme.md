
[![N|Solid](https://www.ekumenlabs.com/images/logo.png)](https://www.ekumenlabs.com/)
# Technical briefing about "robot.cpp" 📋

### About the Nodes / Topics
In the following image, it is possible to see the main topics and nodes of the solution running under node **/master** (ROS) and the **core** (**roscore**) 

[![N|Solid](https://live.staticflickr.com/65535/50997579005_1411232887_z.jpg)](https://flic.kr/p/2kGtVvx)
- The main node **/Robot** advertise on **/master** whom returns **vel_pub** to be able to publish the linear and angular velocity to turtle's **/cmd_vel** using a **twist message** [^1].
[^1]: 
    File: geometry_msgs/Twist.msg
    Raw Message Definition
    This expresses velocity in free space broken into its linear and angular parts.
    
        Vector3  linear
        Vector3  angular



- Then it subscribes to the topic **/directions** using **messages_sub** in order to obtain from him coordinates from the **PY UI** or the testing node **/dirs**, using the callback function **dirCallback**, this function will be pushing the coordinates (expressed in a **Pose2d message** [^2]) into a queue.
 
[^2]:

    File: geometry_msgs/Pose2D.msg
    Raw Message Definition
    This expresses a position and orientation on a 2D manifold.

           float64 x
           float64 y
           float64 theta
- **pose_sub** is a subscription to turtles topic **/pose** in order to obtain from him the current coordinates of the turtle using the callback function **poseCallback**, this function updates the current coordinates (expressed in a **Pose message** [^3]) send it to the **/feedback** node and to the "core" **toGoal** function.
[^3]:
    File: geometry_msgs/Pose.msg
    Raw Message Definition
    A representation of pose in free space, composed of position and orientation. 

           Point position
           Quaternion orientation
 - **feedback_pub** is used by **posCallback** function to publish to current coordinates into the topic **/feedback** .

- **service** is a the published service of the node **/Robot**, which have the entry point **manual_commands**, each time the entry point is called the function **process** takes cares of the command (stop/resume/reset) an calls the entry point of turtle's service **teleportabsolute** using **client** in order to update the current coordinates depending the value of **order**.

- **toGoal** is a key method becuase in order for this to work, we’ll need to turn the [x, y] cartesian coordinate to its polar form. We can’t send the distance to turtles’s topic **/cmd_vel**, you have to send a velocity, linear and angular velocities using a **twist message** [^4].
In order to get to a certain point, we must set the velocity to a value proportional to the distance between our current position and the goal.
This is that I choose to have implemented, a [proportional controller](https://en.wikipedia.org/wiki/PID_controller), setting the velocity proportional to a constant Kv and the distance to go. (The values of Kv and Kh were somewhat random in this sample). 

[^4]:
    File: geometry_msgs/Twist.msg
    Raw Message Definition
    This expresses velocity in free space broken into its linear and angular parts.

          Vector3  linear
          Vector3  angular

- Finally the principal C++ thread **main** do the rest, it checks the queue, it is not empty (data from **/directions**) pop them, calls to main positioning using function (uses **order** received from **manual_commands** | queued coordinates managed by **/directions** or **posecallback** it makes all the math and trigo calculations calling **toGoal** and set **/cmd_vel** then enter enter in a loop, pumping callbacks.  
 
    The following image shows this graphically.

[![N|Solid](https://live.staticflickr.com/65535/50997579050_0a8bf3ef7e_z.jpg)](https://flic.kr/p/2kGtVwj)
---
---

## Built with 🛠️


* [ROS "MELODIC"](http://wiki.ros.org/melodic) - ROS version used
* [UBUNTU 18.04](https://releases.ubuntu.com/18.04/) - Operating system
* [QT Designer](https://doc.qt.io/qt-5/qtdesigner-manual.html) - Tool used for Python's UI
* [Code::Blocks](https://www.codeblocks.org/) - Programming IDE 



Puedes encontrar mucho más de cómo utilizar este proyecto en nuestra [Wiki](https://github.com/tu/proyecto/wiki)


## Author ✒️


* **Chris Beneduce** -  [chrisbeneduce](https://github.com/chrisbeneduce)



## Thanks 🎁

* Juan, Alejo and Fernando from Ekumen 🤓.


---


⌨️ by [chrisbeneduce](https://github.com/chrisbeneduce) 😊
