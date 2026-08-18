# Changelog

This is the Changelog for SolversLib versions for 0.3.1 and higher. It includes the changes made from the previous iteration, and important notes for it as well. You can change SolversLib documentation versions by using the selector in the top left corner.

## [0.3.5](./)

#### Core:

* Added `preRun()` method to `CommandOpMode`, which runs once after "Play" is pressed and before the main `run()` loop starts
  * `preRun()` is not called if the OpMode is stopped during the init phase
* Bug fixed `CascadeController` velocity measurement to use the change in measured position instead of the change in error
* Bug fixed `CascadeController` outputting `NaN` on the first control loop cycle
* Bug fixed `CascadeController`'s primary controller not receiving the position setpoint
* Added `reset()` override to `CascadeController` that also resets its primary and secondary controllers
* Added `getMeasuredVel()` method to `CascadeController`

#### General:

* Changed the FTC SDK dependencies of all modules (`core`, `pedroPathing`, and `photon`) to `compileOnly`, so SolversLib no longer pins an SDK version and works with newer FTC SDK versions
* Split the license into [FIRST-LICENSE](https://github.com/FTC-23511/SolversLib/blob/master/FIRST-LICENSE) and [23511-LICENSE](https://github.com/FTC-23511/SolversLib/blob/master/23511-LICENSE)

## [0.3.4](https://docs.seattlesolvers.com/0.3.4)

#### Core:

* Fixed bug with `RunMode` in `MotorEx` using distance instead of position
* Added `ServoExGroup`
* Added optional `GamepadEx` Slew Rate Limiters (mostly for swerve, but can be used for mecanum chassis as well)
* Added optional `P2PController` Slew Rate Limiters (for swerve)
* Added `mirror()` method to `Pose2d` for alliance swapping
* Updated swerve kinematics with maxSpeed
* Refactored `Timing` class
  * `Stopwatch`
  * `Timer`
  * `Rate` (Refresh Rate Timer)
* Added `rawPower` tracking and access in hardware classes
* Bug fixed `MotorEx` 's PositionControl using `encoder.getPosition()` instead of `encoder.getDistance()`
* Added `LambdaCommand`
* Added `SubsystemBase` `isAvailable()` method
* Added CommandScheduler `getScheduledCommands()` method
* Added `Commands` utility class for pre-defined actions (not to be confused with the Command interface)
* Added `CallbackCommand`
* Bug fixed deadline `end()` command in `ParallelDeadlineGroup`

#### PedroPathing:

* Fixed maxPower not saving for multiple paths

#### Photon (NEW):

* Added stable PhotonCore for FTC

## [0.3.3](https://docs.seattlesolvers.com/0.3.3)

#### Core:

* Rewrote Hardware classes:
  * Deprecated [`ServoEx` interface](features/hardware/motors-1.md#servoex-interface-and-simpleservo-class) and [`SimpleServo` class](features/hardware/motors-1.md#servoex-interface-and-simpleservo-class)
    * The new [`ServoEx` class](features/hardware/servos.md#servoex) serves as a replacement to both of the classes above.
  * Added [`CRServoEx`](features/hardware/servos.md#crservoex-and-absoluteanalogencoder) and [`AnalogAbsoluteEncoder`](features/hardware/servos.md#crservoex-and-absoluteanalogencoder) (4th wire support on Servos)
  * Added optional power caching to `Ex` classes like `MotorEx`, `ServoEx`, and `CRServoEx`, etc.
  * Added a Color Sensor class for the [`REV Color Sensor V3`](features/hardware/sensors.md#sensors)
  * Modified [`SensorDistance`](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorDistance.java) & [`SensorDistanceEx`](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorDistanceEx.java) to handle custom minimum and maximum distance threshold values
* Beta Swerve support:
  * Added [`Point-to-Point`](pathing/point-to-point.md) control
  * Rewrote Swerve kinematics and updated `ChassisSpeeds`
  * Created [`SolversLib Visualizer`](https://visualizer.seattlesolvers.com/) (forked from Pedro Pathing Visualizer)
* Implemented abstract [`Controller`](features/controllers/#using-the-controller-class) class and added [`SquIDF`](features/controllers/squidf.md) controller
* Added native [`RetryCommand`](command-base/command-system/convenience-commands.md#retrycommand) from Marrow

#### PedroPathing:

* Added two new Pedro Commands: [`TurnToCommand`](pedro-pathing/pedro-commands/hold-point-command-2.md) and [`TurnCommand`](pedro-pathing/pedro-commands/hold-point-command-1.md)
* Added support for Pedro Pathing versions 2.0.0 and higher

## [0.3.2](https://docs.seattlesolvers.com/0.3.2)

#### Core:

* Added beta SquID support and early Swerve Kinematics
* Fixed `CommandScheduler`'s `cancelAll()` from throwing an error
* Added additional constructors to [`RepeatCommand`](command-base/command-system/convenience-commands.md#repeatcommand)
* Rewrote SolversHardware wrappers into hardware

#### PedroPathing:

* Added new Pedro Command: [`HoldPointCommand`](pedro-pathing/pedro-commands/hold-point-command.md)
* Added support for Pedro Pathing 1.0.9

## [0.3.1](https://docs.seattlesolvers.com/0.3.1)

#### Core:

* Add SolversHardware caching wrappers
* Fixed known FTCLib bugs/issues:
  * [Double ending issue in ParallelRaceGroup](https://github.com/FTCLib/FTCLib/pull/261)
  * [SequentialCommandGroup is incompatible with other command groups](https://github.com/FTCLib/FTCLib/issues/253)
  * [ConcurrentModificationException in cancelAll()](https://github.com/FTCLib/FTCLib/pull/260)
  * [IllegalAccessError when trying to build clean dev branch on Windows](https://github.com/FTCLib/FTCLib/issues/255)
  * [Missing Aliases for PS4 and PS5 buttons](https://github.com/FTCLib/FTCLib/issues/250)

#### Pedro Pathing:

* Added new Pedro Command: [`FollowPathCommand`](pedro-pathing/pedro-commands/followpathcommand.md)
* Added support for Pedro Pathing 1.0.8
