---
description: package com.seattlesolvers.solverslib.gamepad
---

# Gamepad

SolversLib provides enhanced Gamepad features. These classes are essentially extensions of the stock FTC SDK Gamepad features but with easier implementation methods.

## GamepadKeys

Provides enum representations of the buttons, D-Pad, bumpers, and triggers. Buttons, D-Pad, and bumpers are stored in `GamepadKeys.Button` and triggers are stored in `GamepadKeys.Trigger`. SolversLib has updated support for PS4 / PS5 controllers as well.

| Buttons              |
| -------------------- |
| Y                    |
| X                    |
| A                    |
| B                    |
| TRIANGLE             |
| CIRCLE               |
| SQUARE               |
| CROSS                |
| LEFT\_BUMPER         |
| RIGHT\_BUMPER        |
| BACK                 |
| START                |
| DPAD\_UP             |
| DPAD\_DOWN           |
| DPAD\_LEFT           |
| DPAD\_RIGHT          |
| LEFT\_STICK\_BUTTON  |
| RIGHT\_STICK\_BUTTON |
| PS                   |
| SHARE                |
| TOUCHPAD             |
| TOUCHPAD\_FINGER\_1  |
| TOUCHPAD\_FINGER\_2  |

| Trigger        |
| -------------- |
| LEFT\_TRIGGER  |
| RIGHT\_TRIGGER |

```java
// these are from the GamepadButton class that is used
// for command-based frameworks
GamepadButton grabButton = new GamepadButton(
    gamepad1, GamepadKeys.Button.A
);
GamepadButton releaseButton = new GamepadButton(
    gamepad2, GamepadKeys.Button.B
);

GamepadEx gamepadEx = new GamepadEx(gamepad1);
```

## GamepadEx

An extension of the stock FTC SDK `Gamepad` class. Constructed simply from a Gamepad. Provides six intuitive value-getting methods:

* `getButton()`: Given a `GamepadKeys.Button`, this method will check if that Button is pressed, returning a boolean of whether that Button is pressed.

```java
gamepadEx.getButton(GamepadKeys.Button.A);
```

* `getTrigger()`: Given a `GamepadKeys.Trigger`, this method will return the value of the Trigger (0 if unpressed, 1 if fully depressed).

```java
gamepadEx.getTrigger(GamepadKeys.Trigger.RIGHT_TRIGGER);
```

* `getLeftY()`: Returns the value of the y-axis of the left joystick (note that the value returned is the opposite of what would be returned from the standard gamepad object).

```java
gamepadEx.getLeftY();
```

* `getRightY()`: Returns the value of the y-axis of the right joystick

```java
gamepadEx.getRightY();
```

* `getLeftX()`: Returns the value of the x-axis of the left joystick

```java
gamepadEx.getLeftX();
```

* `getRightX()`: Returns the value of the x-axis of the right joystick

```java
gamepadEx.getRightX();
```

## KeyReader

The `KeyReader` interface is the base for objects that monitor an individual button or trigger on a gamepad. All `Reader` classes must implement these functions:

* `readValue()`: Reads the current value of the key, true or false, and updates the values used by the reader. Returns nothing. This must be called once every loop.
* `isDown()` : Checks if key is currently down. Will return a boolean of whether that key is pressed.
* `wasJustPressed()` : Returns boolean whether the key is pressed, but only if it was previously not pressed.
* `wasJustReleased()` : Returns boolean indicating whether the key is not pressed, but only if it was previously pressed.
* `stateJustChanged` : Returns boolean indicating that the key's value has switched.

## TriggerReader

The `TriggerReader` class implements the `KeyReader` interface. Because `GamepadEx` Triggers return a `double` , the `TriggerReader` class interprets a value of greater than `0.5` as a trigger press.

The following constructs a new Trigger Reader with a `GamepadEx` gamepad and `GamepadKeys.Trigger` trigger.

```java
TriggerReader triggerReader = new TriggerReader(
    gamepadEx, GamepadKeys.Trigger.RIGHT_TRIGGER
);
```

Below are the different methods you can use with the trigger reader.

```java
triggerReader.isDown();
triggerReader.readValue();
triggerReader.stateJustChanged();
triggerReader.wasJustPressed();
triggerReader.wasJustReleased();
```

## ButtonReader

The `ButtonReader`class implements the `KeyReader` interface. It checks if a button is pressed, released, or is down.

```java
ButtonReader reader = new ButtonReader(
    gamepadEx, GamepadKeys.Button.A
);
```

* `ButtonReader(GamepadEx gamepad, GamepadKeys.Button button)`: Constructs a new Button Reader with a `GamepadEx` gamepad and a `GamepadKeys.Button` button.
* `ButtonReader(BooleanSupplier supplier)`: Constructs a new Button Reader using the value of a boolean supplier instead of a gamepad, which allows reading value states easily without a gamepad.

```java
reader.readValue();
reader.wasJustPressed();
reader.stateJustChanged();
reader.isDown();
reader.wasJustReleased();
```

The `GamepadEx` objects actually contain `ButtonReader`s. For every `GamepadKeys.Button`, there is a matching `ButtonReader` entry in the map. It is stored internally as a `Map<GamepadKeys.Button, ButtonReader>`. This allows you to use these features just with the `GamepadEx` class.

```java
// create the gamepad
GamepadEx myGamepad = new GamepadEx(gamepad1);

/** The methods for using the ButtonReaders **/
myGamepad.wasJustPressed(GamepadKeys.Button.A);
myGamepad.stateJustChanged(GamepadKeys.Button.A);
myGamepad.isDown(GamepadKeys.Button.A);
myGamepad.wasJustReleased(GamepadKeys.Button.A);

// pass the GamepadKeys.Button that you want to read
// into the method argument

// to read all buttons at once, perform a single call
myGamepad.readButtons();
/*
this is the equivalent of calling readValue() once
for all your readers
*/
```

## ToggleButtonReader

```java
ToggleButtonReader toggleButtonReader = new ToggleButtonReader(
    gamepadEx, GamepadKeys.Button.A
);
```

The `ToggleButtonReader` class extends `ButtonReader` and adds the ability to get the status of a toggle. `readValue()` needs to be run in a loop to get the state of the toggle.

`getState()` : Gets the toggle value of a button or boolean supplier.

```java
toggleButtonReader.getState();
```

### Usage

```java
GamepadEx toolOp = new GamepadEx(gamepad2);
ToggleButtonReader aReader = new ToggleButtonReader(
  toolOp, GamepadKeys.Button.A
);

while (...) {
  if (aReader.getState()) {
    // if toggle state true
  } else {
    // if toggle state false
  }
  aReader.readValue();
}
```

## Slew Rate Limiters

The [SlewRateLimiter](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/gamepad/SlewRateLimiter.java) class caps how fast a joystick value is allowed to change. Instead of the output jumping instantly from 0 to 1 when you slam the stick, it ramps toward the target at a maximum rate you choose, smoothing out acceleration. This reduces wheel slip and tipping on drivetrains, and is especially popular for swerve drives, where sudden input changes can fight against module rotation.

Credit to FTC 16379 Kookybotz for this code.

### SlewRateLimiter API

Rate limits are in units per second. A joystick axis ranges from -1 to 1, so a rate limit of `2.0` lets the output go from 0 to full deflection in half a second.

* `SlewRateLimiter(double positiveRateLimit, double negativeRateLimit, double initialValue)`: Constructs a limiter with separate limits for increasing and decreasing output (the negative rate limit should be a negative number) and a starting output value.
* `SlewRateLimiter(double rateLimit, double initialValue)`: Constructs a limiter with a symmetric limit of ±`rateLimit` and a starting output value.
* `SlewRateLimiter(double rateLimit)`: Constructs a limiter with a symmetric limit of ±`rateLimit` and a starting output value of 0.
* `calculate(double input)`: Returns the input, limited so it changes no faster than the rate limits allow since the previous call. Call this once per loop with the raw value.
* `updateRateLimit(double rateLimit)`: Changes the rate limit on the fly, setting the positive limit to `rateLimit` and the negative limit to `-rateLimit`.

```java
SlewRateLimiter limiter = new SlewRateLimiter(4.0);

// in your loop
double smoothed = limiter.calculate(gamepad1.left_stick_x);
```

{% hint style="warning" %}
A `SlewRateLimiter` is stateful — it remembers its previous output and an internal timer. Construct it once (e.g. in `init`) and reuse it every loop. Creating a new limiter each loop resets that state, so the output stays pinned near the initial value and the stick will barely respond.
{% endhint %}

### Enabling through GamepadEx

`GamepadEx` can apply slew rate limiting for you. Attach a limiter to each joystick axis with `setJoystickSlewRateLimiters(SlewRateLimiter LX, SlewRateLimiter LY, SlewRateLimiter RX, SlewRateLimiter RY)`, where L/R is the left/right joystick and X/Y is the axis. Pass `null` for any axis you do not want limited. The method returns the `GamepadEx` object for chaining.

Once set, `getLeftX()`, `getLeftY()`, `getRightX()`, and `getRightY()` automatically return the rate-limited values — no extra calls needed in your loop. Note that limiting only applies through these `GamepadEx` getters, not when reading the stock `gamepad` fields directly.

```java
GamepadEx driverOp = new GamepadEx(gamepad1)
    .setJoystickSlewRateLimiters(
        new SlewRateLimiter(4.0), // left stick x
        new SlewRateLimiter(4.0), // left stick y
        new SlewRateLimiter(6.0), // right stick x (turning can respond faster)
        null                      // right stick y (not limited)
    );
```

### Usage

```java
GamepadEx driverOp = new GamepadEx(gamepad1)
    .setJoystickSlewRateLimiters(
        new SlewRateLimiter(4.0),
        new SlewRateLimiter(4.0),
        new SlewRateLimiter(6.0),
        null
    );

waitForStart();

while (opModeIsActive()) {
    double strafe = driverOp.getLeftX();
    double forward = driverOp.getLeftY();
    double turn = driverOp.getRightX();

    drive.driveRobotCentric(strafe, forward, turn);
}
```
