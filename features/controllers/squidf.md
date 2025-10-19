---
description: package com.seattlesolvers.solverslib.controller
---

# SquIDF

A `SquIDF` controller is a variation of the `PIDF` controller that square roots the positional error term. Effectively, it extends the `PIDF` class, and overrides the `calculateOutput` method. This modification changes how the controller responds to errors of different magnitudes.



#### Key Differences from PIDF

| Aspect                    | PIDF                        | SquIDF               |
| ------------------------- | --------------------------- | -------------------- |
| **Proportional Response** | Linear with error           | Square root of error |
| **Large Errors**          | Stronger correction         | Moderate correction  |
| **Small Errors**          | Generally weaker correction | Stronger correction  |
| **Response Curve**        | Straight line               | Curved (sub-linear)  |



`SquIDF` helps reduces overshoot (since the square root dampens aggressive corrections when initial error is high), and generally gives a smoother approach. However, like a PID/PIDF, tuning is critical. The controller is only as good as you can tune it.

#### Usage Example

```java
// Create SquIDF controller with same gains as PIDF
SquIDFController squidfController = new SquIDFController(
    1.0,  // kP
    0.1,  // kI
    0.05, // kD
    0.2   // kF
);

// Use just like a PIDF controller
squidfController.setSetPoint(targetPosition);
double output = squidfController.calculate(currentPosition);
```

#### Tuning Notes

* **kP values** will typically need to be **higher** than in PIDF since √(e) < e for errors > 1
* Start with your PIDF gains and increase kP gradually
* The I, D, and F terms work pretty much identically to PIDF
