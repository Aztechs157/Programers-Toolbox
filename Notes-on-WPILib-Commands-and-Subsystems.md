# Notes on WPILib Commands and Subsystems
Based on WPILib 2016

## Command Class

* _Command_ is started with a call to `Command.start()`
* Once started, it will call `Command.initialize()` once and then `Command.execute()` repeatedly until `Command.isFinished()` returns True.
* Once it does, `Command.end()` will be called once to clean up.
* `Command.execute()` is guaranteed to be called at least once, even if `Command.isFinished()` always returns true.
* If at any point while running, if `Command.cancel()` is called, then `Command.interrupted()` will be called.

### Requires Subsystem

* If a _Command_ uses a _Subsystem_, then it should specify that it does so by calling the `Command.requires(Subsystem)` method in its constructor. 
* Note that a _Command_ may have multiple requirements, and `Command.requires(Subsystem)` should be called for each one.
* If a _Command_ is running and a new _Command_ with shared requirements is started, then one of two things will happen. 
   
  * If the active _Command_ is interruptible, then `Command.cancel()` will be called and the _Command_ will be removed to make way for the new one. 
  * If the active _Command_ is not interruptible, the other one will not even be started, and the active one will continue functioning.
  * By default, _Commands_ are interruptible.  Call `Command.setInterruptible(false)` to change this.

### Timeouts

* A timeout value in seconds can be set by calling the _Command_'s base class constructor with a parameter of type double.
   
  * Alternately, you can call `Command.setTimeout(double)`, which is easier to code.
  * Timing starts when the _Command_ starts.
  * Use the `Command.isTimedOut()` boolean in the `Command.execute()` and `Command.isFinished()` logic to determine if the _Command_ has timed out.

### Joystick Buttons

* Joystick _Button_ methods:
   
  * `whenPressed() `
  * `whenReleased() `
  * `whileHeld() `
  * `toggleWhenPressed() `
  * `cancelWhenPressed() `
   
* `Button.whenPressed(Command)` will call `Command.start()` once when the _Button_ is pressed.  The _Command_ will execute until `Command.isFinished()` returns true or something else calls `Command.cancel()`

* Ditto for `Button.whenReleased(Command)`

* `Button.whileHeld(Command)` will repeatedly call `Command.start()` while the _Button_ is pressed.  Once released, it will call `Command.Cancel()` once.
   
  * This has the effect of calling `Command.initialize()` then repeatedly calling `Command.execute()`.  If the _Command_ finishes, it will be restarted again by calling Command.initialize() again and then repeatedly calling `Command.execute()`, etc.
      
* `Button.toggleWhenPressed(Command)`: Each time the _Button_ is pressed:
   
  * if the _Command_ is running, it will call `Command.cancel()`
  * otherwise it calls `Command.start()`
   
* `Button.cancelWhenPressed()` calls `Command.Cancel()` when pressed if the _Command_ is running.
   
  * The _Command_ must be running for `Command.cancel()` to be called.  So you cannot cheat by having a _Command_ where only cancel is called.

