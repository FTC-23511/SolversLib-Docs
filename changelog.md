# Changelog

This is the Changelog for SolversLib versions for 0.3.x and higher. It includes the changes made from the previous iteration, and important notes for it as well. You can change SolversLib documentation versions by using the selector in the top left corner.

### [0.3.3](./)

* Added two new Pedro Commands: [`TurnToCommand`](pedro-pathing/pedro-commands/hold-point-command-2.md) and [`TurnCommand`](pedro-pathing/pedro-commands/hold-point-command-1.md)&#x20;
* Added support for Pedro Pathing versions 2.0.0 and higher
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

### [0.3.2](https://app.gitbook.com/o/Vbh44pUmrudj7jrlevBI/s/qHp1wuxUd3u5XDcqQNPs/)

* Added new Pedro Command: [`HoldPointCommand`](pedro-pathing/pedro-commands/hold-point-command.md)
* Added support for Pedro Pathing 1.0.9
* Added beta SquID support and early Swerve Kinematics
* Fixed `CommandScheduler`'s `cancelAll()` from throwing an error
* Added additional constructors to [`RepeatCommand`](command-base/command-system/convenience-commands.md#repeatcommand)
* Rewrote SolversHardware wrappers into hardware

### [0.3.1](https://app.gitbook.com/s/4WjwIOqhnAnCe7dwK3v4/)

* Added new Pedro Command: [`FollowPathCommand`](pedro-pathing/pedro-commands/followpathcommand.md)
* Added support for Pedro Pathing 1.0.8
* Add SolversHardware caching wrappers
* Fixed known FTCLib bugs:
  *
