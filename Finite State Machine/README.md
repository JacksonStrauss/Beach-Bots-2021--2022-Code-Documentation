## Finite State Machine

When working with a system that needs to perform a certain set of tasks at one point in time and then switch to a different behavior later on, it can become complicated to create and manage these different "states" in which the system runs. A way to simplify this process is to implement a framework called a [finite-state machine](https://en.wikipedia.org/wiki/Finite-state_machine) which makes it much easier for the programmer to create distinct "states" that the program can switch between for different behaviors.

I decided to use a finite-state machine for the different states of our lifting mechanism. We used a PS4 controller as our robot controller and therefore we  had a limited amount of buttons that we could use for certain actions. We needed to perform only certain actions during certain phases of the lifting process and we wanted to make sure we didn't accidentally perform incorrect actions during other phases. So, I implemented the finite-state machine so that actions could only be performed during the correct phase.

To create the state machine, I first had to create the actual variables which would keep track of which state the robot/lifting mechanism was in. I used the [enumerator datatype](https://en.wikipedia.org/wiki/Enumerated_type) in Java.

```java
public enum State {HOME, DEPOSIT, TRANSITION}

static State state = State.HOME;

public State getState() {
  return state;
}
```
I then added a method which checked which state the lifting mechanism was in and performed the correct actions accordingly:
```java
switch(state) {
  case HOME:
    if (gamepad2.dpad_right) {
      // LOW
      target = lowPOS;
      state = State.TRANSITION;
    }
    
    if (gamepad2.dpad_left) {
      // MEDIUM
      target = mediumPOS;
      state = State.TRANSITION;
    }
    
    if (gamepad2.dpad_up) {
      // HIGH
      target = highPOS;
      state = State.TRANSITION;
    }
  break;
  case TRANSITION:
      if(gamepad2.right_bumper && (currentloop - last_rightbumper_press) > PRESS_TIME_MS) {
        last_rightbumper_press = currentloop;
        state = State.DEPOSIT;
      }
  break;
  case DEPOSIT:
    if(!gamepad2.right_bumper) {
      state = State.TRANSITION;
    }
  break;
}
```
A lot of the functional code that makes the servos turn or the motors run is cut out here to showcase the main functionality of the state machine. When the conditions of one state are met, the state machine then immediately switches to the next state. The states are in a constant cycle of switching back and forth between each other. 

One neat little addition is the cooldown timer for the "TRANSITION" state. A timer keeps track of the last time the button was pressed and if it is too recent then it will not call for the state change. Because we use the same button to switch between the transition state and the deposit state, we have to add in the cooldown timer or else the state machine will switch between the two states for every single cycle that the button is pressed down.

The state machine itself isn't too complex of a system to implement but it really does increase the efficiency and readability of the code substantially. We also used a state machine in the autonomous mode for the dynamic pathing (which you can learn about in the Dynamic Pathing section of this repository) but I think this page illustrates well enough how we used finite-state machines for our robot.
