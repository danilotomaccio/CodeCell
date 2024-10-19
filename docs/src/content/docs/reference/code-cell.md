---
title: CodeCell Reference
description: A reference page in my new Starlight docs site.
---

# CodeCell Class
## Overview
The CodeCell class provides an interface for interacting with the CodeCell device, which appears to be a development board with integrated sensors and an LED. It offers functionalities for reading sensor data (light, proximity, motion), controlling the onboard LED, managing power states, and putting the device to sleep.

## Version
CODECELL_SW_VERSION 1.2.3

## Constants
### Power States
- **POWER_BAT_CHRG**: Device is running on battery power.
- **POWER_USB**: Device is powered via USB.
- **POWER_INIT**: Initial power state after startup or wakeup.
- **POWER_BAT_CHRG_FULL**: Battery is fully charged.
- **USB_VOLTAGE**: Voltage threshold (4100 mV) for detecting USB power.
- **MIN_BATTERY_VOLTAGE**: Minimum battery voltage (3350 mV) for operation.
- 
### LED
- **LED_PIN**: GPIO pin connected to the LED (pin 10).
- **LED_DEFAULT_BRIGHTNESS**: Default brightness level for the LED (10).
- **LED_SLEEP_BRIGHTNESS**: Reduced brightness level for sleep mode (5).
- **LED_COLOR_RED**, LED_COLOR_ORANGE, ..., LED_COLOR_WHITE: Predefined color values for the LED.
- 
### Light Sensor
- **LIGHT**: Seems unused in the provided code.
- **VCNL4040_ADDRESS**: I2C address of the VCNL4040 light sensor (0x60).
- **VCNL4040_ALS_CONF_REG**, VCNL4040_PS_CONF1_2_REG, ... : Registers of the VCNL4040 sensor.

### Motion Sensor
- BNO085_ADDRESS: I2C address of the BNO085 motion sensor (0x4A).
- MOTION_DISABLE: Disable motion sensing.
- MOTION_ACCELEROMETER, MOTION_GYRO, ..., MOTION_ACTIVITY: Flags for enabling specific motion sensor functionalities.
- MOTION_STATE_UNKNOWN, MOTION_STATE_ONTABLE, ..., MOTION_STATE_MOTION: Possible states reported by the motion sensor.

## Public Methods

#### CodeCell()

Constructor: Initializes a new CodeCell object.
Init(uint16_t sense_motion)
Initializes the CodeCell device.
sense_motion: Specifies which motion sensor functionalities to enable (e.g., MOTION_ACCELEROMETER | MOTION_GYRO).
Performs device setup, including sensor initialization, LED configuration, and pin mode settings.
Prints sensor readings and version information to the serial port.

#### PrintSensors()

Prints the current readings of all enabled sensors to the serial port.

#### Test()

Performs a test routine.
Sets GPIO pins 1, 2, 3, 5, 6, and 7 HIGH for 500ms, then LOW. This likely controls external components connected to these pins.
USBSleep(bool cable_polarity)
Puts the device into a low-power sleep mode when powered via USB.
cable_polarity: Indicates the polarity of the USB cable.
If cable_polarity is true, the device shuts down completely, allowing for reprogramming.
If cable_polarity is false, the device enters deep sleep and wakes up on GPIO 0 activity (likely a button press).
Sleep(uint16_t sleep_sec)
Puts the device into deep sleep for a specified duration.
sleep_sec: Sleep duration in seconds.
Configures the light sensor and motion sensor for sleep.
Disables peripherals and enters deep sleep, waking up after the specified time.

#### WakeUpCheck()

Checks the wakeup reason after the device wakes from sleep.
Returns true if the device woke up from the timer, false otherwise.

#### Run()

Handles the main runtime logic of the CodeCell.
Monitors battery voltage and charging state.
Controls LED behavior based on power source and battery level.
Manages serial communication.
Returns true every 100ms, providing a timing mechanism for the application.

#### BatteryRead()

Reads the battery voltage.
Returns the battery voltage in millivolts.
LED(uint8_t r, uint8_t g, uint8_t b)
Sets the color of the onboard LED.
r, g, b: Red, green, and blue color components (0-255).
LED_Breathing(uint32_t rgb_color_24bit)
Creates a breathing effect on the LED with the specified color.
rgb_color_24bit: 24-bit RGB color value.
The breathing effect is influenced by the proximity sensor reading.

#### Light_Init()

Initializes the VCNL4040 light sensor.
Configures the sensor for continuous conversion mode, high dynamic range, and specific integration time.
Returns true if an error occurs during initialization, false otherwise.

#### LightReset()

Resets the light sensor to a default configuration.

#### Light_ProximityRead()

Reads the proximity value from the light sensor.
Returns the proximity value.

#### Light_WhiteRead()

Reads the white light intensity from the light sensor.
Returns the white light intensity in lux.

#### Light_AmbientRead()

Reads the ambient light intensity from the light sensor.
Returns the ambient light intensity in lux.

#### Motion_Init()

Initializes the BNO085 motion sensor.
Enables the motion sensor functionalities specified by the _msense member.
Prints messages to the serial port indicating the status of each functionality.

#### Motion_Read()

Reads data from the motion sensor.
Waits for a sensor event to be available.
If no event is available within a timeout period, it prints an error message and resets the device.

#### Motion_AccelerometerRead(float &x, float &y, float &z)
Reads accelerometer data from the motion sensor.
x, y, z: References to variables that will store the accelerometer readings in m/s².

#### Motion_GyroRead(float &x, float &y, float &z)
Reads gyroscope data from the motion sensor.
x, y, z: References to variables that will store the gyroscope readings in rad/sec.

#### Motion_MagnetometerRead(float &x, float &y, float &z)
Reads magnetometer data from the motion sensor.
x, y, z: References to variables that will store the magnetometer readings in uT (microtesla).

#### Motion_GravityRead(float &x, float &y, float &z)
Reads gravity vector data from the motion sensor.
x, y, z: References to variables that will store the gravity vector components in m/s².

#### Motion_LinearAccRead(float &x, float &y, float &z)
Reads linear acceleration data from the motion sensor.
x, y, z: References to variables that will store the linear acceleration components in m/s².

#### Motion_StepCounterRead(uint16_t &x)
Reads the step count from the motion sensor.
x: Reference to a variable that will store the step count.


#### Motion_RotationRead(float &roll, float &pitch, float &yaw)
Reads rotation vector data from the motion sensor.
roll, pitch, yaw: References to variables that will store the roll, pitch, and yaw angles in degrees.

#### Motion_RotationNoMagRead(float &roll, float &pitch, float &yaw)
Reads rotation vector data from the motion sensor without using the magnetometer.
roll, pitch, yaw: References to variables that will store the roll, pitch, and yaw angles in degrees.

#### Motion_TapRead()
Checks if a tap event has been detected by the motion sensor.
Returns true if a tap is detected, false otherwise.

#### Motion_StateRead()
Reads the current state of the device from the motion sensor.
Returns a value representing the state (e.g., MOTION_STATE_STABLE, MOTION_STATE_MOTION).

#### Motion_ActivityRead()
Reads the current activity classification from the motion sensor.
Returns a value representing the detected activity (e.g., walking, running, still).