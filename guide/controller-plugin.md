## Controller Plugin

The controller functionality can be extended with user-implemented plugins. The
purpose of a controller plugin is to facilitate the programming of robot-
specific robot windows and remote-control wrappers.

Programming controller plugins rather than programming directly in the
controller is more convenient because it increases considerably the modularity
and the scalability of the code. For example a robot window can be used for
several robots.

### Fundamentals

Whatever its language, a controller executable is linked with the Webots
controller library (libController) at startup. A controller plugin is a shared
library loaded dynamically (at runtime) by libController after a specific event
depending on its type.

The  shows an overview of the controller plugin system. In this figure, the
dashed arrows shows how the shared libraries are loaded, and the large dash
lines represents an Inter-Process Communication (IPC). The IPC between
libController and Webots is a pipe (On Windows this is a named pipe, and
otherwise a local domain socket). The IPC between libRemoteControl and the real
robot is defined by the user (TCP/IP, Serial, etc.).

The system has been designed as follow. Every entities (the controller, the
remote control library and the robot window library) should only call the
libController interface (Webots API) functions. The controller should not be
aware of its robot window and its real robot for modularity reasons. The only
exception is about the robot window library which can be aware of the remote
control library to initialise and monitor it. This can be done trough the
libController API through the `wb_robot_get_mode()`, `wb_robot_set_mode()` and
the `wb_remote_control_custom_function()` functions. Of course these rules can
be easily broken because every entities runs into the same process. However we
recommend to respect them to get a good design.

The controller plugins have been designed to be written in C/C++, because the
result should be a dynamic library. However it's certainly possible to write
them in other languages using a C/C++ wrapper inbetween.

After its loading, some controller plugin functions (entry points) are called by
libController. A set of entry points have to be defined to let the controller
plugin work smoothly. Some of these entry points are required and some are
optional.

The `Robot` node defines the location of the controller plugin through its
*robotWindow* and its *remoteControl* fields (cf. reference manual)

The controller plugin run in the main thread of the process (also known as GUI
thread): the same as the controller executable. This implies that if an entry
point of a plugin is blocking, the controller will also be blocked. And if the
plugin crashes, the controller is also crashed.

The search algorithm to convert the *robotWindow* and the *remoteControl* to an
existing path is explained in the reference manual.

Each distributed shared library is built thanks to the main Makefile (the same
as the one used to build the controllers):
`WEBOTS_HOME/resources/Makefile.include`


%figure "Controller plugin overview"
![Controller plugin overview](pdf/controller_plugin.pdf.png)
%end

### Robot Window Plugin

A robot window plugin allows the programmer to efficiently create custom robot
windows. Robot windows can be opened by double-clicking on the virtual robot, or
by selecting the `Robot | Show Robot Window` menu item.

The *robotWindow* field of the `Robot` node specifies a robot window (cf.
documentation in the reference manual).

The entry points of a robot window controller plugin are:

- `bool wbw_init()`This is the first function called by libController. Its aim is to initialize the graphical user interface without showing it.
- `void wbw_cleanup()`This is the last function called by libController. Its aim is to cleanup the library (destroy the GUI, release the memory, store the current library state, etc.)
- `void wbw_pre_update_gui()`This function is called before `wbw_update_gui()` to inform its imminent call. Its purpose is to inform that from this moment, the pipe answering from Webots to the controller can receive data. If data is coming from the Webots pipe `wbw_update_gui()` should return as soon as possible.
- `void wbw_update_gui()`The aim of this function is to process the GUI events until something is available on the Webots pipe.
- `void wbw_read_sensors()`This function is called when it's time to read the sensors values from the Webots API. For example in this function the `wb_distance_sensor_get_value()` function can be called.
- `void wbw_write_actuators()`This function is called when it's time to write the actuator commands from the Webots API. For example in this function the `wb_motor_set_position()` function can be called.
- `void wbw_show()`This function is called when the GUI should be show. This can occur either when the user double-click on the virtual robot, either when he selects the `Robot | Show Robot Window` menu item, or either at controller startup if the *showRobotWindow* field of the `Robot` node is enabled.
- `void *wbw_robot_window_custom_function(void *)`This function can optionally be defined to communicate and pass data from and to the controller program. It is called directly by the user from the controller when executing the `wb_robot_window_custom_function` C function or `Robot::robotWindowCustomFunction` C++ function. You can find more information in the reference manual.

The internal behavior of the `wb_robot_step()` call is the key point to
understand how the different entry points of the robot window plugin are called
(pseudo-code):


``` c
wb_robot_step() {
  wbw_write_actuators()
  wbw_pre_update_gui()
  write_request_to_webots_pipe()
  wbw_update_gui() // returns when something on the pipe
  read_request_to_webots_pipe()
  wbw_read_sensors()
}
```

As the Qt libraries are included in Webots (used by the Webots GUI), and all our
samples are based on it, we recommend to choose also this framework to create
your GUI. The `Makefile.include` mentioned above allows you to efficiently link
with the Qt framework embedded in Webots.

The Webots pipe handle (integer id) is available in the WEBOTS_PIPE_IN
environment variable.

The Qt utility library helps you to initialize Qt correctly (pipe, window
visibility, ...). Refer to the next section for more information related with
this library.

If the robot window cannot be loaded (bad path, bad initialization, etc.), a
generic robot window is opened instead. This generic robot window displays
several sensors and actuators. The source code of this robot window is a good
demonstrator of the robot window plugin abilities. All the source code is
located there:
`WEBOTS_HOME/resources/projects/plugins/robot_windows/generic_window`

Other samples can be found:

`WEBOTS_MODULES_PATH/projects/robots/e-puck/plugins/robot_windows/botstudio`

`WEBOTS_MODULES_PATH/projects/robots/e-puck/plugins/robot_windows/e-puck_window`

### Qt utility library

In order to facilitate the creation of robot window plugins using the Qt
framework, Webots has a utility library allowing to hide the complexity of the
management of the robot windows.

This library contains various classes including a subclass of QMainApplication
managing the pipe events, a subclass of QMainWindow dealing with the close
events, a class to handle Webots standard paths, and a collection of widgets to
display the Webots devices. The source code of the generic robot window is a
good example illustrating how to use this library.

The location of the qt utility library is
`WEBOTS_HOME/resources/projects/libraries/qt_utils`


### Motion editor

A motion is a chronological sequence of robot poses. A pose is defined by a set
of commands (in position) of the robot motors.

The motion is stored in a motion file in a user-readable format. The controller
API allows to play easily the motion files on your robot. You can get the
complete motion API in the reference manual.

The motion editor (cf. the ) is a GUI helping to create motions which can be
played by a robot. It is implemented in the Qt utils library, and is embedded
inside the generic robot window plugin. This implies that the motion editor is
accessible only if the robot is linked (cf. the Robot::robotWindow field) with
either the generic window, or on a window using the Qt utils library's
corresponding widget.

In the motion editor different fonts and colors are used to identify the status
of poses and motor states: modified items are displayed using bold font,
disabled states are written in gray, and items with values outside the valid
motor position range are written in red.


%figure "Motion editor view"
![Motion editor view](png/motion_editor.png)
%end

### Remote-control Plugin

A remote-control plugin allow to simply and efficiently create an interface
using the Webots API to communicate with a real robot. The main purpose of a
remote-control library is to wrap all the Webots API functions used by the robot
with a protocol communicating to the real robot. Generally, a program (client)
runs on the real robot, and decodes the communication protocol to dialog with
the real robot devices.

The remote-control library is initialized when an entity calls the
`wb_robot_set_mode()` libController function. This entity is typically
libRobotWindow, because it's quite convenient to use the GUI to initialize the
communication (i.e. entering the IP address of the robot, etc.)

There are two entry points to the remote-control library:

- `bool wbr_init(WbrInterface *ri)`This function is called by libController to initialize the remote control library. It is called after the first `wb_robot_set_mode()` call. The aim of this function is to map the functions given into the `WbrInterface` structure with functions inside the remote-control library.
- `void wbr_cleanup()`This function is called by libController to cleanup the library.

The `WbrInterface` structure has several functions (mandatory) which have to be
mapped to let the remote-control library run smoothly. Here they are:

- `bool wbr_start(void *arg)`This function is called when the connection with the real robot should start. The return value of this function should inform if the connection has been a success or not. The argument matches with the argument given to `wb_robot_set_mode()` when initializing the remote-control. As the robot window library is often responsible in calling `wb_robot_set_mode()`, the structure passed between them should match.
- `void wbr_stop()`This function is called when the connection with the real robot should stop. Typically a command stopping the real robot actuators should be sent just before stopping the connection.
- `bool wbr_has_failed()`This function is called very often by libController to check the validity of the connection. The value returned by this function should always match with the connection validity.
- `void wbr_stop_actuators()`This function is called to stop the actuators of the real robot. This is called when the user pressed the pause button of the simulator.
- `int wbr_robot_step(int period)`This function is called when the controller enters in the step loop. The aim of this function is to send the actuator commands and then to read the vaues of the enabled sensors. The timing problem should be solved there. The robot should wait at least *period* milliseconds, and returns the delta time if this *period* is exceeded.

As said above, all the Webots API functionalities that should work with the real
robot have to be wrapped into the remote-control library. To achieve this:

- The internal state of the libController has to be setup to match with the current state of the robot.Typically, when the value of a sensor is known the corresponding `wbr_sensor_set_value()` has to be called.
- The commands send to the libController have to be wrapped.Typically, when the command of an actuator is setup the corresponding `wbr_actuator_set_value()` is called, and has to be sent to the real robot.

The complete definition of the remote control API and of the `WbrInterface`
structure is contained in
`WEBOTS_HOME/include/controller/c/webots/remote_control.h`

For example, if you want to be able to use the distance sensor of the real
robot, you have to wrap the `wbr_set_refresh_rate()` function (to set the
internal state of the remote control library to read this distance sensor only
when required), and to call `wbr_distance_sensor_set_value()` into the remote-
control library when the distance sensor is refreshed (typically into the
`wbr_robot_step()` function).

A complete sample (communicating with the e-puck robot using bluetooth) can be
found in this directory:

`WEBOTS_MODULES_PATH/projects/robots/e-puck/plugins/remote_controls/e-puck_bluet
ooth`
