---
description: package com.seattlesolvers.solverslib.controller
---

# Controllers

In SolversLib, there are controllers that can improve the motion of mechanisms in FTC. This includes PID, feedforward, SquID, and cascade control.

{% hint style="success" %}
If you wish to use any one of the controllers mentioned above directly, you can look at the individual subpages for them directly
{% endhint %}

The primary controller is an abstract class called `Controller`, which is a generic closed-loop controller. You can find the source code for the `Controller` class [here](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/controller/Controller.java), and the theory behind it on CtrlAltFtc [here](https://www.ctrlaltftc.com/introduction-to-closed-loop-control).

{% hint style="info" %}
All other controllers (PID/PIDF, FF, SquIDF, Cascade) extend this controller in some manner, and inherits its protected fields and methods. They provide added functionality that will be usually be more helpful to most users.
{% endhint %}

The `Controller` class serves as the foundation for all control algorithms in SolversLib. It provides common functionality for setpoint tracking, error calculation, tolerance checking, and output management. Concrete implementations (like PID, Feedforward, or SquID) extend this class and implement the `calculateOutput()` method with their specific control logic.

There are 3 key points that are a bit more specific for SolversLib's `Controller`:

* **Minimum Output**: When not at setpoint, the absolute value of the output will be at least `minOutput` (if it is set), preserving the sign of the correction (using `Math.signum()`)
* **Error Calculation**: Error is automatically updated whenever `setSetPoint()` is called
* **atSetpoint()**: Returns `true` only when **both position and velocity errors** are within tolerance - this is especially important for PID/PIDF classes

This class is generally used for advanced purposes, like SolversLib's[ Point-to-Point](../../pathing/point-to-point.md) drivetrain control system.

### Using the Controller Class

in order to use SolversLib's Controller, users must first construct a `Controller` object with its only constructor:

```java
Controller controller = new Controller();
```

This constructor is implicitly called during construction of the other controllers.

#### Methods

1. **`double calculate(double pv)`**

Calculates the control output using the given measured value (process variable).

**Parameters:**

* `pv` - The current measured value

**Returns:** The control value u(t), respecting minimum output if not at setpoint

2. **`double calculate(double pv, double sp)`**

Calculates the control output with a new setpoint and measured value.

**Parameters:**

* `pv` - The current measured value
* `sp` - The desired setpoint

**Returns:** The control value u(t)

3. **`double calculate()`**

Calculates the control output using the stored measured value.

**Returns:** The control value u(t)

**Note:** Requires `measuredValue` to be set beforehand

4. **`void setSetPoint(double sp)`**

Sets the controller's target setpoint and updates error calculations.

**Parameters:**

* `sp` - The desired setpoint

**Side Effects:**

* Updates `errorVal_p` (positional error)
* Updates `errorVal_v` (velocity error)

5. **`double getSetPoint()`**

Returns the current setpoint.

**Returns:** The current target setpoint

6. **`boolean atSetPoint()`**

Checks if the controller has reached the setpoint within the defined tolerances.

**Returns:** `true` if both positional and velocity errors are within tolerance, `false` otherwise

7. **`double getPositionError()`**

Returns the current positional error e(t).

**Returns:** The difference between setpoint and measured value

8. **`double getVelocityError()`**

Returns the current velocity error e'(t).

**Returns:** The rate of change of the error

9. **`void setTolerance(double positionTolerance)`**

Sets the acceptable positional error for `atSetPoint()` checks. Velocity tolerance remains at infinity.

**Parameters:**

* `positionTolerance` - Maximum acceptable positional error

**Example:**

```java
controller.setTolerance(2.0); // Within 2 units is acceptable
```

10. **`void setTolerance(double positionTolerance, double velocityTolerance)`**

Sets both positional and velocity error tolerances.

**Parameters:**

* `positionTolerance` - Maximum acceptable positional error
* `velocityTolerance` - Maximum acceptable velocity error
*

11. **`double[] getTolerance()`**

Returns the current tolerance settings.

**Returns:** Array containing `[positionTolerance, velocityTolerance]`

12. **`void reset()`**

Resets the controller state by clearing previous error and timestamp values.

**Use Case:** Call this when switching between different control tasks or when re-enabling a disabled controller

13. **`Controller setMinimumOutput(double minOutput)`**

Enforces a minimum output magnitude when the controller is not at the setpoint. This prevents the controller from applying insignificantly small corrections.

**Parameters:**

* `minOutput` - Minimum absolute value of the output (sign is preserved)

**Returns:** The controller instance (for method chaining)

14. **`double getPeriod()`**

Returns the time period between controller calculations.

**Returns:** The period in seconds (or your time unit)

### Protected Methods

`abstract double calculateOutput(double pv)`

This is the core control algorithm that is implemented by subclasses.

**Parameters:**

* `pv` - The measured value

**Returns:** The raw control output before minimum output enforcement

**Note:** This method is called internally by `calculate()`. Subclasses override this to implement specific control strategies (PID, feedforward, etc.).
