# Ambientika Solo+ ESPHome
ESPHome Setup for Home Assistant that allows local control of a "Südwind Ambientika Solo+" ventilation system. Replaces the default infrared remote control with an ESP32 microcontroler for controlling all the ventilator functions via WLAN from Home Assistant.

This solution is heavily inspired by https://github.com/Cicatr1x/Ambientika-ESP32-Control

There is a "smart" version of this product for 619€ instead of 379€ which you can integrate into Home Assistant via a cloud connection and this integration:
https://github.com/ambientika/HomeAssistant-integration-for-Ambientika.
But with some soldering and programming, we can achieve a much cheaper and fully local control.

## Components
- **Ambientika solo+**
	- The fan that we will make Home Assistant-compatible.
	- Provides 24V and GND for our custom modules.
	- https://www.ambientika.eu/dezentrale-wohnraumlueftung-ambientika-solo-einzelraumloesung-neu/sw10038
-   **ESP32 D1 Mini**
	- The microcontroller used to control the other components.
	- https://www.amazon.de/dp/B0DHY6WXV5
-   **MP1584EN DC-DC 3A Step-Down Converter**: 
	- Converter used to step down the available 24V to 5V for the ESP32.
	- https://www.amazon.de/dp/B07DJ5HZ7G
- **GP8403 2-Channel I2C DAC Module (0-10V)**
	- This module can output a voltage between 0-10V and is connected to the ESP32 via the I2C interface.
	- The voltage controls the speed of the fan.
	- https://www.komputer.de/zen/index.php?main_page=product_info&products_id=654
	- https://www.dfrobot.com/product-2613.html
- **5V Relais Module**
	- This relay controls the 230V voltage to the component responsible for opening the ventilation cover.
	- https://www.komputer.de/zen/index.php?main_page=product_info&products_id=707


## Setup
The original is controled via infrared-remote and provides three speed settings. The voltages were measured on the original configuration and implemented here.

The onboard 24V DC needs to be converted to 5V DC (with a MP1584EN) to power a ESP-32. It controls a GP8403 via I2C, which outputs any voltage between 0V and 10V.
The ESP-32 also controls a relay module, which in turn controls the 230V deliverd to the "100331.14" actuator, which keeps the ventilation flap/cover open during operation.

![Schematic](https://github.com/jnettels/ambientika_solo_esphome/blob/main/schematic/suedwind_ambientika_solo_esphome.png)

The states of the fan and cover are synchronized in the automations, such that the cover is always open when the fan is active. When direction is changed, a 10 second pause is implemented to allow the fan to stop spinning, before turning it in the other direction. The oscillating mode copies the original, which changes directions after 70 seconds.

The esp32 boards needs to be loaded with the ESPHome yaml configuration provided in the config folder. This will create a ``Fan`` entity in Home Assistant. There a many tutorials for installing ESPHome.

The directions are defined as follows:
- forward = outward
- reverse = inward

## Installation
The original setup with the infrared board. It connects to 230V, 24V and to the fan motor. We will reuse those connections.
![Original](https://github.com/jnettels/ambientika_solo_esphome/blob/main/pictures/ambientika_solo_esphome_01_original.jpg)

The custom components fully connected look like this:
![Components](https://github.com/jnettels/ambientika_solo_esphome/blob/main/pictures/ambientika_solo_esphome_02_components.jpg)

The final setup with all components within the fan casing. This did not seem possible and took me a lot of time, but worked out in the end. I had to cut the interior plastic away at some spots.
![Setup](https://github.com/jnettels/ambientika_solo_esphome/blob/main/pictures/ambientika_solo_esphome_03_setup.jpg)

The housing can be closed, as usual:
![Closed](https://github.com/jnettels/ambientika_solo_esphome/blob/main/pictures/ambientika_solo_esphome_04_closed.jpg)

As you can see, the ventilation cover is held open by the enabled system. The infrared remote control is no longer needed :-)
![Operation](https://github.com/jnettels/ambientika_solo_esphome/blob/main/pictures/ambientika_solo_esphome_05_operation.jpg)

