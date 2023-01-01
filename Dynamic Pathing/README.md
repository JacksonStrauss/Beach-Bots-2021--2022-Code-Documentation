## Dynamic Pathing

A key component of the FTC challenges is that there are 30 seconds of "autonomous mode" at the beginning of every match where the robot is not controlled
by a person but rather runs a set of automated tasks. After the 30 seconds are up, the robot is then allowed to be controlled manually.

Programming the robot to perform automated tasks can be extremely challenging because you have to keep track of the robot's position the whole time. If the robot's location tracking system is not extremely accurate then cumulative errors will build and the robot's location tracking becomes less and less accurate over time. This is similar to the issue found when using the [dead reckoning navigation technique](https://en.wikipedia.org/wiki/Dead_reckoning).
