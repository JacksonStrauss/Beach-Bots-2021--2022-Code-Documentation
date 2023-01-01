## Dynamic Pathing

A key component of the FTC challenges is that there are 30 seconds of "autonomous mode" at the beginning of every match where the robot is not controlled
by a person but rather runs a set of automated tasks. After the 30 seconds are up, the robot is then allowed to be controlled manually.

Programming the robot to perform automated tasks can be extremely challenging because you have to keep track of the robot's position the whole time. If the robot's location tracking system is not extremely accurate then cumulative errors will build and the robot's location tracking becomes less and less accurate over time. This is similar to the issue found when using the [dead reckoning navigation technique](https://en.wikipedia.org/wiki/Dead_reckoning).

We utilized a really useful library called [Road Runner](https://learnroadrunner.com/#frequently-asked-questions) which allowed us to use the combination of an [IMU](https://en.wikipedia.org/wiki/Inertial_measurement_unit) (internal measurement unit) and [odometry dead wheels](https://gm0.org/en/latest/docs/common-mechanisms/dead-wheels.html) to determine the location of our robot in autonomous mode and create custom trajectories that the robot could follow. Road Runner generates its own pathing functions with custom PID control to ensure that the robot travels smoothly but quickly from one point to another.

One of the issues that we faced using Road Runner was that you could only create a trajectory for the robot if you knew the exact starting and end position. Our robot could keep track of its location position but if you told it to go to a certain position, it would not be able to 100% accurately travel to that position (it would sometimes stop a few inches short of the desired destination). We believe that we did not end up correctly tuning Road Runner and so the robot was unable to follow the instructions given.
