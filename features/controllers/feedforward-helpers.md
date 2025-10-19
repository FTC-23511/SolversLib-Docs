# Feedforward Helpers

## Feedforward Control

So far, we’ve used feedback control for reference tracking (making a system’s output follow a desired reference signal). While this is effective, it’s a reactionary measure; the system won’t start applying control effort until the system is already behind. If we could tell the controller about the desired movement and required input beforehand, the system could react quicker and the feedback controller could do less work. A controller that feeds information forward into the plant like this is called a feedforward controller.

A feedforward controller injects information about the system’s dynamics (like a mathematical model does) or the intended movement. Feedforward handles parts of the control actions we already know must be applied to make a system track a reference, then feedback compensates for what we do not or cannot know about the system’s behavior at runtime.

There are two types of feedforwards: model-based feedforward and feedforward for unmodeled dynamics. The first solves a mathematical model of the system for the inputs required to meet desired velocities and accelerations. The second compensates for unmodeled forces or behaviors directly so the feedback controller doesn’t have to. Both types can facilitate simpler feedback controllers. We’ll cover several examples below.

SolversLib provides a number of classes to help users implement accurate feedforward control for their mechanisms. In many ways, an accurate feedforward is more important than feedback to effective control of a mechanism. Since most FTC mechanisms closely obey well-understood system equations, starting with an accurate feedforward is both easy and hugely beneficial to accurate and robust mechanism control.

SolversLib currently provides the following three helper classes for feedforward control. The feedforward components will calculate outputs in units determined by the units of the user-provided feedforward gains. Users _must_ take care to keep units consistent as it does not have a type-safe unit system.

### SimpleMotorFeedforward

The `SimpleMotorFeedforward` class calculates feedforwards for mechanisms that consist of permanent-magnet DC motors with no external loading other than friction and inertia, such as flywheels and robot drives.

To create a `SimpleMotorFeedforward`, simply construct it with the required gains:

```java
// Create a new SimpleMotorFeedforward with gains kS, kV, and kA
SimpleMotorFeedforward feedforward =
    new SimpleMotorFeedforward(kS, kV, kA);
```

Please note that the `kA` value is optional. If the mechanism does not have much inertia, then it is not required.

To calculate the feedforward, simply call the `calculate()` method with the desired motor velocity and acceleration:

```java
// Calculates the feedforward for a velocity of 10 units/second
// and an acceleration of 20 units/second^2
// Units are determined by the units of the gains passed
// in at construction.
feedforward.calculate(10, 20);
```

### ArmFeedforward

The `ArmFeedforward` class calculates feedforwards for arms that are controlled directly by a permanent-magnet DC motor, with external loading of friction, inertia, and mass of the arm. This is an accurate model of most arms in FTC.

To create an `ArmFeedforward`, simply construct it with the required gains:

```java
// Create a new ArmFeedforward with gains kS, kCos, kV, and kA
ArmFeedforward feedforward = new ArmFeedforward(kS, kCos, kV, kA);
```

To calculate the feedforward, simply call the `calculate()` method with the desired arm position, velocity, and acceleration:

```java
// Calculates the feedforward for a position of 1 units,
// a velocity of 2 units/second, and
// an acceleration of 3 units/second^2
// Units are determined by the units of the gains passed
// in at construction.
feedforward.calculate(1, 2, 3);
```

### ElevatorFeedforward

The `ElevatorFeedforward` class calculates feedforwards for elevators that consist of permanent-magnet DC motors loaded by friction, inertia, and the mass of the elevator. This is an accurate model of most elevators in FTC.

To create a `ElevatorFeedforward`, simply construct it with the required gains:

```java
// Create a new ElevatorFeedforward with gains kS, kG, kV, and kA
ElevatorFeedforward feedforward = new ElevatorFeedforward(
    kS, kG, kV, kA
);
```

To calculate the feedforward, simply call the `calculate()` method with the desired motor velocity and acceleration:

```java
// Calculates the feedforward for a position of 10 units,
// velocity of 20 units/second,
// and an acceleration of 30 units/second^2
// Units are determined by the units of the gains passed
// in at construction.
feedforward.calculate(10, 20, 30);
```

### Using Feedforward to Control a Mechanism

Feedforward control can be used entirely on its own, without a feedback controller. This is known as “open-loop” control, and for many mechanisms (especially robot drives) can be perfectly satisfactory. A `SimpleMotorFeedforward` might be employed to control a robot drive as follows:

```java
public void tankDriveWithFeedforward(double leftVelocity,
                                     double rightVelocity) {
  leftMotor.set(feedforward.calculate(leftVelocity));
  rightMotor.set(feedforward.calculate(rightVelocity));
}
```
