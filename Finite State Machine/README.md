## Finite State Machine

A [finite-state machine](https://en.wikipedia.org/wiki/Finite-state_machine) is a system that, at a given point in time, is in one particular state out of a finite list of several states. The state machine can switch between certain states when ordered to (this is called a transition). A finite-state machine can be helpful when you have something that needs to perform a specific set of tasks at one point in time and then an entirely different set of tasks at another point in time. 

We decided that it would be best to implement a finite-state machine for our robot's lifting mechanism due to the complicated processes of raising, extending, and lowering the lift. To manually control our robot, we used a PS4 controller, which has a limited number of buttons. Implementing the finite-state machine allowed us to assign different actions to the same physical button depending on the state of the lifting mechanism.


I used the [enumerator datatype](https://en.wikipedia.org/wiki/Enumerated_type) in Java to keep track of the individual states.

```java
public enum State {HOME, DEPOSIT, TRANSITION}

static State state = State.HOME;

public State getState() {
  return state;
}
```
I then added a method that checked which state the lifting mechanism was currently in and performed the correct actions accordingly:
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
A lot of the functional code that makes the servos turn or the motors run is cut out here to showcase the main functionality of the state machine. When the conditions of one state are met, the state machine immediately switches to the next state. The states are in a constant cycle of switching back and forth between each other. 

One neat little addition is the cooldown timer for the "TRANSITION" state. A timer keeps track of the last button press, and if the press is too recent, then the code will not call for a state change. Because we use the same button to switch between the transition state and the deposit state, we have to add in the cooldown timer, or else the state machine will switch between the two states for every single cycle that the button is pressed down.

The state machine itself isn't too complex of a system to implement and if used in a proper setting, it can increase the efficiency and readability of the code substantially.
