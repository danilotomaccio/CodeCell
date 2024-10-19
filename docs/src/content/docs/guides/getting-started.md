---
title: Getting Started
description: 'CodeCell Basics: Your First Steps'
---

# CodeCell Basics: Your First Steps

If you’ve just got your hands on the CodeCell, you're in for a treat. This tiny module is designed to simplify your DIY projects with multiple features packed into a penny-sized board. In this guide, we’ll walk you through:

- What makes a CodeCell? And how does it circuit work?
- How to setup and use your [CodeCell](https://microbots.io/codecell)
- Getting started with Examples
- Explain all the available library functions and how you can use them in your project

## What makes a CodeCell?

CodeCell is a compact and versatile module featuring the ESP32-C3, multiple power options, and integrated sensors, all within a tiny 1.85 cm wide form factor. These features make it a powerful tool for a wide range of applications.

In this first section, we'll start by getting familiar with the circuitry that forms the CodeCell. After that, we'll walk through the simple steps to set up your CodeCell.

## ESP32C3 Module

At the heart of the CodeCell is the ESP32C3 module, a compact microcontroller known for being maker-friendly in the IoT space. It combines an Arduino-compatible architecture with built-in Wi-Fi and Bluetooth Low Energy (BLE) capabilities. This integration offers the most popular connectivity options while maintaining a small form factor.

The ESP32C3 module's PCB antenna is positioned on one side, away from other components, to minimize interference and improve signal transmission and reception. This placement helps reduce the impact of ground planes or other conductive surfaces that could degrade antenna performance. The components on the bottom side are kept within the recommended clearance for the antenna. From testing we found that the antenna's performance remains unaffected by the minimal interference from a USB-C cable, as these cables are typically shielded.

The ESP32-C3 provides plenty of memory, with 4 MB of Flash and 400 KB of SRAM, making it capable of running most typical applications. Its 32-bit RISC-V single-core processor, running at up to 160 MHz, efficiently handles various tasks. This combination of memory and processing power makes the ESP32-C3 suitable for a wide range of uses.

The ESP32C3 module also supports a USB Serial/JTAG Controller, allowing us to make the CodeCell reflashable through the USB-C port and to send serial data for communication and debugging.

## Power Management

The CodeCell offers flexibility in power supply options. It can be powered through the LiPo battery connector, a USB-C cable, or both.

The LiPo battery connector makes it easier than ever to safely connect the battery without the need for soldering or risking accidental shorting it. 

The USB-C port serves dual purposes: it is used for both powering the device and/or reprogramming it. This multi-power option is enabled through the BQ24232 battery management chip, which features dynamic power-path management (DPPM) that can power the system while simultaneously and independently charging the battery. The battery charging process is managed in three phases: conditioning precharge, constant current, and constant voltage. To protect the battery the output voltage (Vo) is regulated though the BQ24232 chip. This output supports a maximum output current of 1500mA when powered by the LiPo battery and 450mA when powered via USB.

By default, the LiPo battery charge current is set to 90mA, ensuring a balanced and a safe charge rate for the optional 170mAh LiPo battery. Further more, for those who wish to adjust the charging rate, 0402 resistor R12 have to be de-soldered and replace it with a new resistor based on the formula (R = 870/Ichrg). This is only recommended for soldering pros, who aren’t afraid of wrestling with tiny 0402 components! Check the BQ24232 datasheet for more information on the battery charging.

The CodeCell library can provides visual feedback on the battery/usb power status via the onboard addressable RGB LED:

Low Battery Warning: When the battery voltage drops below 3.3V, the LED blinks red ten times and the device enters Sleep Mode. This helps conserve power until the device is reconnected to a USB charger.

- Charging Process: During charging, the CodeCell suspends application processes, lights the LED blue, and waits for the battery to reach full charge. Once fully charged, the LED performs a breathing-light animation, indicating proximity distance detected by the sensors.

- Battery Powered: When the USBC is disconnected and running from the battery power the CodeCell will light green again performing a breathing-light animation, indicating proximity distance detected by the sensors.

The power regulation is further supported by multiple decoupling capacitors, including up to two bulk capacitors of 100µF each, placed next to the battery-connector. These capacitors are connected to the 3.3V and the output Vo pins to ensure stable power delivery. Additionally, the board features two TVS diodes for protection; one safeguards the USB input 5V voltage (Vin), and the other protects the output voltage (Vo). These TVS diodes provide protection against electrostatic discharges (ESD), capable of safely absorbing repetitive ESD strikes above the maximum level specified in the IEC 61000-4-2 international standard without performance degradation.

The board also includes an onboard 3.3V Low Dropout (LDO) regulator, which provides a stable power supply to its low-voltage components. This tiny NCP177 LDO chip can output up to 500mA output current with a typically low dropout voltage of 200mV at 500mA.

## GPIO and Power Pins

Given the compact design, the main challenge was to maximize the use of GPIO pins. We tackled this by dividing each of the three available sides of the CodeCell into different I/O sections based on their applications. We also placed power pins along the edges of the module for easy connection to various power sources, allowing you to connect other modules, sensors, and actuators to different sides.

On the bottom side, 3 out of 5 pins are used for power: a ground pin (GD), a 3.3V logic-level power pin (3V3) and a 5V input charge pin (5V0). This 5V0 pin is connected to the USB input-voltage. This means you can use it to get 5V power when the USB is connected, or you can use it as a power input for charging instead of using the USB.. The other 2 pins are the I2C SDA & SCL pins for adding external digital sensors. If your not using any external and the light/motion sensors, these I2C pins can be set up as GPIOs.

The other two sides each have a ground pin (GD) and a voltage output pin (VO). Each side also features 3 programmable GPIO pins (IO1, IO2, IO3, IO5, IO6, IO7), which can all be configured as PWM pins (ideal for directly connecting an h-bridge for actuator/motor control). IO1, IO2, and IO3 can also be used as ADC pins.

## Sensing Capabilities

The CodeCell's standout features include its onboard sensors. Each unit comes equipped with a built-in light sensor, and there's also an optional motion sensor available to elevate your project's motion detection—especially useful for robotics and wearables!

- **VCNL4040 Light Sensor**: This sensor measures both light levels and proximity up to 20 cm. It features a 16-bit high-resolution design that combines a proximity sensor, an ambient light sensor, and a high-power IRED into a compact package. By integrating photodiodes, amplifiers, and an analog-to-digital converter onto a single chip, it provides enhanced functionality. The I2C configuration is directly embedded in the CodeCell library, ensuring the sensor is automatically initialized to optimize its sensing resolution. 
- **Optional 9-axis BNO085 Motion Sensor**: This advanced IMU sensor is a pricey upgrade, but we believe it's well worth the investment! It upgrades the CodeCell’s capabilities with an integrated 3-axis accelerometer, 3-axis gyroscope, and 3-axis magnetometer + the BNO085’s advanced sensor fusion algorithms combines the data from these sensors to accurately determine the motion data of the sensor, like: Angular Rotational Reading (Roll, Pitch, Yaw), Motion State (e.g., On Table, Stationary, Motion), Motion Activity (e.g., Driving, Walking, Running), Accelerometer Readings, Gyro Reading, Magnetometer Reading, Gravity Reading, Linear Acceleration Reading, Tap detection, Step Counter. 

Next we'll dive into how the CodeCell library simplifies both configuring these sensors and reading their data.

## What about the BOOT Pin?

Some ESP32 development boards include both a **RST (Reset)** button and a **BOOT** button to manually put the device into programming mode. However, the ESP32-C3, such as the one on the CodeCell module, can automatically enter boot mode through the serial interface when using the Arduino IDE. This means the CodeCell doesn't need dedicated RST or BOOT buttons, which allowed us to make it as small as it is.

In the rare case that your CodeCell freezes or encounters an exception (causing it to continuously reset), you can manually force it into boot mode to reflash the firmware. To do this, simply connect a wire between the **SCL** pin and the **GND** pin. After that, unplug the USB or disconnect the battery, then reconnect it. This will reset the device and allow it to enter boot mode, enabling you to reprogram your CodeCell.

## How to setup and use your CodeCell?

To make programming even easier, the CodeCell library provides a wide array of functions for initializing, reading, and managing sensors and power. In this section we're going to explain everything you need to know about setup up your device and it's library.

### Unboxing Your CodeCell

Let’s start with what you’ll find inside the box. Depending on the options you selected during checkout, in the box you'll find:

1. <ins>CodeCell</ins>: The star of the show, your tiny yet mighty board featuring the ESP32-C3 module, programmable GPIO pins, and sensors.
2. <ins>Screws</ins>: Four M1.2 x 6mm screws to mount the CodeCell securely in your projects.
3. <ins>Headers</ins>: Three sets of female headers which can come unsoldered or soldered, depending on your choice.
4. <ins>Battery/Cable</ins>: Depending on your selection during checkout, you’ll either receive a free battery cable for connecting your own battery to the CodeCell's onboard connector or a 170mAh 20C LiPo battery with a pre-soldered cable. This optional battery measures 23 x 17.5 x 8.7 mm and weighing just 4.6 grams. Click here if you like to access the battery's datasheet.

### Powering Up Your CodeCell for the First Time

Let's start by plugging in a USB-C cable! Once your CodeCell receives power it should: 

- <ins>Initialization</ins>: It sets up the internal peripherals and configures the onboard sensors. Once this is being performed it outputs a Hello World message on the Serial Monitor. 
- <ins>Power Check</ins>: It monitors the power status, check if the battery is connected and whether it's charging. If no battery is connected for charging, it will run a breathing light animation with the onboard RGB LED. The animation speed changes based on the proximity of the light sensor ~ Bring your hand close to it to slow down.. Move your hand away to speed it back up! The LED flashes blue or green depending on whether the board is powered by USB or battery. If the battery is charging, the LED stays static blue until fully charged.

### Setting Up Your CodeCell

Next step is to connect the CodeCell to Arduino IDE and run a sketch:

1. <u>USB Connection</u>: Connect your CodeCell to your PC using a standard USB-C cable. This cable not only powers the board but also allows for reprogramming.
2. <u>Install the Arduino IDE</u>: If your new to the Arduino-World no worries, just download and install the latest free version of the Arduino IDE software from the official Arduino website. 
3. <u>Add ESP32 Board Manager URL</u>: If you have never used an ESP32, open the Arduino IDE and navigate to 'File > Preferences'. In the 'Additional Board Manager URLs' field, enter</u>: `https://dl.espressif.com/dl/package_esp32_index.json` and then click ok. Then go to 'Tools > Board > Boards Manager'. Search for 'ESP32' and click 'Install'.
4. <u>Select the Board</u>: The next step is to select our board. Head to 'Tools > Board' and choose 'ESP32C3 Dev Module'. In a few weeks, you'll be able to search directly for 'CodeCell', but for now, selecting 'ESP32C3 Dev Module' works perfectly fine, since it's the microcontroller used onboard the CodeCell.
5. <u>Select Port</u>: Go to 'Tools > Port' and select the COM port corresponding to your CodeCell.
6. <u>Other Settings</u>:  Navigate to 'Tools > USB_CDC_On_Boot' and ensure it's enabled if you plan to use the Serial Monitor. Also, set the clock speed to 160MHz.


With your IDE all set up, we can now go ahead an install the "CodeCell" library. To do this go to 'Sketch>Include Library>Manage Libraries' - the 'Library Manager' should open up. Just type "CodeCell" and click 'Install' to download the latest version of the CodeCell.

We are continuously updating and adding new features to this library, so make sure you're using the latest version.

To quickly get familiar with this library, go to 'File > Examples > CodeCell,' where you'll find multiple examples you can use and modify for your projects. We recommend starting with the 'GettingStarted' example, which contains just a few lines of code but explains all the sensing functionalities available with CodeCell.

Once you select an example sketch, click the 'Upload' button to flash the code onto your CodeCell. After uploading, open the Serial Monitor 'Tools > Serial Monitor' to see serial data from your CodeCell.

Here are some additional CodeCell tutorials to help you get started with various applications:

- Depth Gestures
- Tap Detection
- Proximity 
- Auto Dimming
- Step Counter
- Angle Control
- Personal Activity Guessing
- Wireless Remote
- Ai Prompt
- Alexa Light Control

## CodeCell Library Functions

To explore the code further, let's break down all the functions and explain what each one does:

### Initializing CodeCell

The first step in using the CodeCell is to initialize it. This is done using the `myCodeCell.Init()` function, which allows you to specify the sensors you want to enable.

Available Sensing Macros:

- LIGHT - Enables Light Sensing.
- MOTION_ACCELEROMETER - Enables Accelerometer Sensing.
- MOTION_GYRO - Enables Gyroscope Sensing.
- MOTION_MAGNETOMETER - Enables Magnetometer Sensing.
- MOTION_LINEAR_ACC - Enables Linear Acceleration Sensing.
- MOTION_GRAVITY - Enables Gravity Sensing.
- MOTION_ROTATION - Enables Rotation Sensing.
- MOTION_ROTATION_NO_MAG - Enables Rotation Sensing without Magnetometer.
- MOTION_STEP_COUNTER - Enables Walking Step Counter.
- MOTION_STATE - Enables Motion State Detection.
- MOTION_TAP_DETECTOR - Enables Tap Detector.
- MOTION_ACTIVITY - Enables Motion Activity Recognition.


```c++
void setup() {
    Serial.begin(115200);

    myCodeCell.Init(LIGHT + MOTION_ROTATION + MOTION_STATE);
}
```

You can combine multiple macros using the `+` operator to initialize multiple sensors at once.

### Managing Power

The `myCodeCell.Run()` function is crucial for power management. This function should be called within the `loop()` function to handle battery status and ensure optimal power usage.

Function Behavior:

- The function returns `true` every 100ms, which can be used for time-based operations.
- It controls the onboard LED to indicate different power states:
    - Red Blinking (Blinking 10 times) - Battery voltage below 3.3V, entering Sleep Mode.
    - Green LED (Breathing Animation) - Powered by battery
    - Blue LED (Static) - Battery is Charging
    - Blue LED (Breathing Animation) - Fully charged and ready for operation.


### Reading Sensor Data

After initializing the sensors, you can read their data using various functions provided by the library. Here's a quick rundown of the available functions:

Light Sensor Functions:

- Light_ProximityRead() - Reads the proximity value from the light sensor.
- Light_WhiteRead() - Reads the white light intensity from the light sensor.
- Light_AmbientRead() - Reads the ambient light intensity from the light sensor.

Motion Sensor Functions:

- Motion_AccelerometerRead(float &x, float &y, float &z) - Reads acceleration data along the x, y, and z axes.
- Motion_GyroRead(float &x, float &y, float &z) - Reads rotational velocity data along the x, y, and z axes.
- Motion_MagnetometerRead(float &x, float &y, float &z) - Reads magnetic field strength data along the x, y, and z axes.
- Motion_GravityRead(float &x, float &y, float &z) - Reads gravity vector data along the x, y, and z axes.
- Motion_LinearAccRead(float &x, float &y, float &z) - Reads linear acceleration data along the x, y, and z axes.
- Motion_TapRead(uint16_t &x) - Reads the number of taps detected.
- Motion_StepCounterRead(uint16_t &x) - Reads the number of steps counted.
- Motion_RotationRead(float &roll, float &pitch, float &yaw) - Reads angular rotational data (roll, pitch, yaw).
- Motion_RotationNoMagRead(float &roll, float &pitch, float &yaw) - Reads angular rotational data without using the magnetometer.
- Motion_StateRead(uint16_t &x) - Reads the current state (e.g., On Table, Stationary, Motion).
- Motion_ActivityRead(uint16_t &x) - Reads the current activity (e.g., Driving, Walking, Running).

Example Usage:

```c++
void loop() {
  if (myCodeCell.Run()) {
    // Runs every 100ms
    uint16_t proximity = myCodeCell.Light_ProximityRead();
    if (proximity < 10) {
        // Proximity not detected
    } else {
        // Proximity detected
    }
  }
}
```

### Sleep, Power-Saving, Diagnostic & LED Functions

The CodeCell includes several functions to manage sleep and power-saving modes:

- WakeUpCheck() - Checks the wake-up reason of the device. If the device wakes up from a timer event, it returns `true`; otherwise, it returns `false`
- Sleep(uint16_t sleep_sec) - Puts the device into deep sleep mode for a specified duration in seconds. It configures the necessary pins and sensors for low power consumption before entering sleep mode.
- USBSleep(bool cable_polarity) - Manages the device's sleep mode when the battery level is low or when the device is charging via USB power. It shuts down the application and prepares the device for sleep to allow reprogramming if needed.
- PrintSensors() - Prints the current readings from all initialized sensors to the serial monitor. This function is especially useful for debugging and data logging.
- BatteryRead() - This function reads and returns the battery voltage when the USB-C port is disconnected.
- LED_Breathing(uint32_t rgb_color_24bit) - This function is used in the Run() handler to control the onboard LED & create a breathing effect with a specific color. The `rgb_color_24bit` parameter is a 24-bit color value representing the RGB color. When using this function be careful as the 'Run' function might overwrite your LED color.
- LED(uint8_t r, uint8_t g, uint8_t b) - This function sets the color of the onboard addressable LED using the RGB model where `r`, `g`, and `b` are 8-bit values representing the red, green, and blue components of the color. Each component ranges from 0 to 255. When using this function be careful as the 'Run' function might overwrite your LED color.

## Congratulations!
You've now taken your first steps with CodeCell. Dive deeper into the library examples, explore sensor integrations, and start bringing your innovative projects to life with CodeCell!