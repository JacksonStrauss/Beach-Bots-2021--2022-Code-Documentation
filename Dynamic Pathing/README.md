## Dynamic Pathing

A key component of the FTC challenges is that there are 30 seconds of "autonomous mode" at the beginning of every match where the robot is not controlled
by a person but rather runs a set of automated tasks. After the 30 seconds are up, the robot is then allowed to be controlled manually.

Programming the robot to perform automated tasks can be extremely challenging because you have to keep track of the robot's position the whole time. If the robot's location tracking system is not extremely accurate then cumulative errors will build and the robot's location tracking becomes less and less accurate over time. This is similar to the issue found when using the [dead reckoning navigation technique](https://en.wikipedia.org/wiki/Dead_reckoning).

We utilized a really useful library called [Road Runner](https://learnroadrunner.com/#frequently-asked-questions) which allowed us to use the combination of an [IMU](https://en.wikipedia.org/wiki/Inertial_measurement_unit) (internal measurement unit) and [odometry dead wheels](https://gm0.org/en/latest/docs/common-mechanisms/dead-wheels.html) to determine the location of our robot in autonomous mode and create custom trajectories that the robot could follow. Road Runner generates its own pathing functions with custom PID control to ensure that the robot travels smoothly but quickly from one point to another.

One of the issues that we faced using Road Runner was that you could only create a trajectory for the robot if you knew the exact starting and end position. Our robot could keep track of its location position but if you told it to go to a certain position, it would not be able to 100% accurately travel to that position (it would sometimes stop a few inches short of the desired destination). We believe that we did not end up correctly tuning Road Runner and so the robot was unable to follow the instructions given. But we still needed the robot to travel to the desired position...

The solution was to tell the robot to travel further than it really should have been. For example, if the robot needed to travel to (10, 10) from (0, 0) but was only making it to (8, 8), then we could tell the robot to try and travel to (12, 12). This sort of worked and the robot would usually end up around (10, 10) but this was a really janky and bad way of solving the problem. We weren't 100% sure where are robot was going to end up every time and this posed a problem for creating our trajectories. Because of the constraints of Road Runner, we had to create our trajectories before the program ran and because we couldn't rely on the end position of our robot after each trajectory, the compounding errors became really bad.

I decided to make a way to dynamically create trajectory paths as the robot was moving around. We could now create trajectories whenever we wanted based on our true position rather than having to define all our trajectories before the program could run (a major limitation of Road Runner). 

```java
public TrajectorySequence createTrajectory(SampleTankDrive drive) {

  TrajectorySequence trajectory1 = drive.trajectorySequenceBuilder(drive.getPoseEstimate())
    .splineTo(new Vector2d(-10, -64), Math.toRadians(180))
    .build();
  return trajectory1;
}
```
By using the PoseEstimate of the robot (the location of the robot) in real time, we could much more accurately create paths for the robot to follow. There isn't really too much code here to showcase but this solution ended up being extremely helpful. I think in the future, it would be better to either tune Road Runner better so that the robot can actually follow the path it's supposed to, or create a custom autonomous motion controller for the robot.
