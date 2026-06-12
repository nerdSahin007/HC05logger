# HC05logger

# HC-05 Distance Logger For Arduino Uno

This project is now targeted at:

- `Arduino Uno ATmega`
- `HC-05` Bluetooth Classic module
- `Android phone`

The app connects to the HC-05 over Bluetooth Serial Port Profile, reads live ultrasonic distance telemetry, lets you send control commands, and exports the session as a CSV log.

## What To Use

Use these folders:

- `android-hc05/` - Android Studio project for your phone app
- `arduino/hc05_distance_logger/hc05_distance_logger.ino` - Arduino Uno sketch for the HC-05

The older `app/` web app is a BLE-oriented prototype and is not the right transport for `HC-05`.

## Important Compatibility Note

`HC-05` is Bluetooth Classic, not BLE.

That means:

- Android phones can connect well.
- iPhones generally cannot use `HC-05` for this serial workflow.
- A browser-based Web Bluetooth app is not the right approach for this module.

## Hardware Wiring

### Ultrasonic Sensor

- Trigger pin -> Arduino pin `7`
- Echo pin -> Arduino pin `8`
- LED -> Arduino pin `13`

### HC-05 Module

- HC-05 `TXD` -> Arduino pin `10`
- HC-05 `RXD` <- Arduino pin `11`
- HC-05 `VCC` -> `5V`
- HC-05 `GND` -> `GND`

Use a voltage divider on the Arduino-to-HC-05 line going into `RXD`, because the Uno TX side can be `5V`.

## Arduino Behavior

The sketch:

- measures distance every `500 ms`
- sends JSON telemetry over USB serial and HC-05
- turns the LED off automatically when the measured distance is below the safety threshold
- accepts commands from the phone

Commands the phone can send:

- `LED:1`
- `LED:0`
- `THRESHOLD:5`
- `STATUS`

Telemetry line example:

```json
{"distanceCm":24.78,"thresholdCm":5.00,"tooClose":false,"ledRequested":true,"ledActual":true,"uptimeMs":3150}
```

## Android App Features

The Android app in `android-hc05/` includes:

- paired HC-05 device picker
- Bluetooth connect and disconnect
- live distance display
- LED on and off controls
- threshold update control
- session statistics
- session log viewer
- CSV export through Android's save-file picker

## How To Use

1. Open `arduino/hc05_distance_logger/hc05_distance_logger.ino` in the Arduino IDE.
2. Select your `Arduino Uno`.
3. Upload the sketch.
4. Pair the `HC-05` in your Android Bluetooth settings first.
5. Open `android-hc05/` in Android Studio.
6. Build and install the app on your Android phone.
7. Open the app, refresh the paired device list, choose the HC-05, and tap `Connect`.
8. Watch live distance values and export CSV when needed.

## Android Permissions

The app requests Bluetooth connection permission on Android 12 and above.

If the phone asks for permission:

- allow Bluetooth access
- keep Bluetooth enabled before tapping `Connect`

## Project Layout

- `android-hc05/settings.gradle.kts`
- `android-hc05/app/build.gradle.kts`
- `android-hc05/app/src/main/java/com/codex/hc05distancelogger/MainActivity.kt`
- `android-hc05/app/src/main/res/layout/activity_main.xml`
- `arduino/hc05_distance_logger/hc05_distance_logger.ino`

## Notes

- The default HC-05 baud in the sketch is `9600`.
- The Uno uses `SoftwareSerial` on pins `10` and `11` for Bluetooth.
- If your HC-05 AT configuration uses another baud rate, change `bluetooth.begin(9600);` in the sketch.
