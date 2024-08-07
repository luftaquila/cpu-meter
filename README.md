# hardware-meter

`hardware-meter` is a classical analog gauge that shows a hardware usage. It is a simple analog voltmeter with a microprocessor. Any MCU with USB serial communication and PWM GPIO could be used.

https://github.com/luftaquila/hardware-meter/assets/17094868/05a8670c-ace7-459e-b40a-1173809b3f6e

Supported OS:

* Windows
* MacOS
* Linux (Not tested yet)

Supported metrics:

* CPU Utilization
* CPU Frequency
* Memory Utilization
* Swap Utilization
* Networks Speed

CPU temperature, disk metrics and GPU metrics will supported in the future.

Any MCUs like Arduino(ATmega), STM32 and ESP32 with USB and PWM could be used.

## Prerequisites
* Any MCU with USB serial and PWM output
* An analog voltmeter
  
> [!NOTE]
> Arduino(ATmega) must have `u#` in its chip name (ex. ATmega32u4), which means on-chip USB support.
> <br><br>
> STM32 series have too many variants to explain here. Take a look at their catalog and choose a chip with USB peripheral.\
> Provided firmwares are for `STM32F103C8T6` and `STM32F407VET6`.
> <br><br>
> On ESP32 series, `ESP32-S2`, `ESP32-S3`, `ESP32-C3`, `ESP32-C6`, `ESP32-H4` are supported.\
> Plain `ESP32` and `ESP32-C2` is not supported since there is no USB peripheral. 

> [!TIP]
> There are common analog panel meters called `85C1` and `91C4` series, which cost only $2~3 and have a various voltage range. There are 0-3V and 0-5V models, which fit with most common MCUs.
> <br><br>
> Choose a voltage range by your MCU's logic level. 5V MCUs such as Arduino can manipulate both 0-5V and 0-3V models. 3.3V MCUs such as ESP32 or STM32, Raspberry Pi, etc. can only run 0-3V models.

### Reminder
If you write your own MCU code instead of using provided firmwares, please read followings carefully:

> [!IMPORTANT]  
> If you are using a 5V MCU with a 0-3V voltmeter, the PWM output ***must*** be regulated to 3V max. If your MCU is 3.3V, it is recommended to adjust the PWM voltage to a max 3V, not 3.3V.

> [!IMPORTANT]  
> If you use a 0-5V range voltmeter, please keep in mind that the PWM output voltage could be lower than 5V depending on your PC and MCU.\
> If your PC supplies a voltage less than 5V to the MCU via the USB, the MCU can only output that voltage to its PWM pin.
> Also, most MCUs have a protection diode on their USB input, which makes additional VCC voltage drop. Mostly 0.3V (Schottky diode) or 0.7V (general diode).\
> This will result in the analog gauge not pointing its max position, even if the metric is pointing 100%. For example, if your PC supplies 4.7V and a protection diode drops 0.7V, the gauge will point 4V position even if the CPU is in full load.

## DIY!
1. Connect the voltmeter and the MCU. Test your voltmeter's polarity with some batteries before connect with MCU.
    * `+` terminal goes to the PWM supported GPIO pin, and `-` terminal goes to the GND.
3. Upload a firmware in [`devices/*`](https://github.com/luftaquila/hardware-meter/tree/main/devices) to your MCU.

|Supported MCU|PWM pin|Voltmeter<br>range|Note|Implementation|
|:-:|:-:|:-:|:-:|:-:|
|Arduino|D3|0-5V|Arduino IDE project (*.ino)|[4255dcc](https://github.com/luftaquila/hardware-meter/commit/4255dcc31e8221ad6f17f32b5cbf0cf269fe91b5)|
|STM32F4|PA8|0-3V|STM32CubeIDE project<br>(`STM32F407VET6`)|[7c3e3a1](https://github.com/luftaquila/hardware-meter/commit/7c3e3a1a7421477c3b945049cbb990eb700c9f11)|
|STM32F1|PA8|0-3V|STM32CubeIDE project<br>(`STM32F103C8T6`)|[265403c](https://github.com/luftaquila/hardware-meter/commit/265403c16b42c878c83aad94794e0df8ea754b39)|

> [!NOTE]
> If there is no firmware for your MCU, please implement and contribute your own!
> <br>
> It is just a basic program that reads 4 bytes little-endian float value from the serial and makes corresponding PWM output.

Optional:
* Build a 3d printed housing with 3d models at [`3d models/*`](https://github.com/luftaquila/hardware-meter/tree/main/3d%20models)

## Run
1. Download a latest desktop program at [Releases](https://github.com/luftaquila/hardware-meter/releases).
2. Connect your MCU and PC with a USB cable.
3. Run your desktop executable. The tray icon will be generated.
4. Click the tray icon and select your MCU's serial port.

> [!NOTE]
> The list of the ports will be only scanned at the program launch.
> <br>
> This is a limit of [tray-item-rs](https://github.com/olback/tray-item-rs) as their tray items are not editable or removable.

> [!TIP]
> You can build a desktop program on your own by running `cargo build --release`.

## More
If you are good at soldering, something like this is possible:

<p align=center><img src='https://github.com/luftaquila/hardware-meter/assets/17094868/64b86bb4-c493-46ee-a92e-635a30d26e69' height=200px> <img src='https://github.com/luftaquila/hardware-meter/assets/17094868/f41067a8-71cc-41a4-b089-065da2732e4b' height=200px> <img src='https://github.com/luftaquila/hardware-meter/assets/17094868/11d534f2-2b8d-4f98-adbc-e1453bd10fb8' height=200px></p>

This is a `hardware-meter` with a USB Type-C port. All components including MCU and the Type-C port is mounted inside voltmeter.

The [Arduino Pico](https://projecthub.arduino.cc/MellBell/arduino-pico-the-worlds-smallest-arduino-board-bc7986) runs 0-5V 85C1 voltmeter. USB Micro port on the Pico is desoldered and jumped onto a DIY Type-C board at the back side.
