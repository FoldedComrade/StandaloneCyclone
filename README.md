# StandaloneCyclone
Trying to run a Fod Cyclone Duratec 37 and 6R80 transmission on the OEM PCM and no other modules. Working project.

# Powertrain
Donor Vehicle: 2011 F-150 (2WD)
~160k miles
* Note the Duratec 37 out of any transverse-mounted application has an internal water pump known for failing and dumping all the coolant into the block. Wild design choice.


## Engine
3.7L Cyclone

## Transmission
6R80 6 speed automatic

# Control
* PCM flashed to delete PATS - couldn't for the life of me find any record online of someone figuring out a workaround

Do-outs
* Not sure the PCM is going to love not having O2 sensors
* Not sure the PCM is going to love not having an ABS module, which seems to be what the PCM uses for vehicle speed source
* Not sure the PCM is going to love that the final drive ratio is wildly different than what it's programmed for

## References
Dr. Ken Tindell seems to be the CAN godfather
* [Canis Labs](https://canislabs.com/)
* [Personal Blog](https://kentindell.github.io/)

Yogesh Ojha has a helpful getting started 101
* [Part 1](https://medium.com/@yogeshojha/car-hacking-101-practical-guide-to-exploiting-can-bus-using-instrument-cluster-simulator-part-i-cd88d3eb4a53)
* [Part 2](https://medium.com/@yogeshojha/car-hacking-101-practical-guide-to-exploiting-can-bus-using-instrument-cluster-simulator-part-ee998570758)
* [Part 3](https://medium.com/@yogeshojha/car-hacking-101-practical-guide-to-exploiting-can-bus-using-instrument-cluster-simulator-part-ea40c05c49cd)

Brent Picasso was able to [log wheel speeds, steering agnle, brake pressure via CAN](https://www.autosportlabs.com/reverse-engineering-ford-mustang-2011-2014-can-bus-steering-angle-throttle-position-brake-pressure-and-wheel-speeds/)

## Hardware
* Purchased the [Canis CANPico from CopperHill](https://copperhilltech.com/canpico-v2-with-pico-wh-pre-installed/)
  * [Pi Pico W Firmware](https://micropython.org/download/RPI_PICO_W/)
* [Waveshare USB-CAN-B](https://www.waveshare.com/wiki/USB-CAN-B) offers two channels and good isolation, but forum posts indicate hit-or-miss functionality/setup
* [CANable](https://openlightlabs.com/) looks good but seems perpetually out of stock
* [USB2CAN]
* [Macchina M2](https://www.macchina.cc/catalog/m2-boards) - recommended by Yogesh Ojha as a cheap, open-source, modular interface
* [CLX000](https://www.csselectronics.com/pages/can-bus-sniffer-reverse-engineering) - looks good for sniffing & logging, discriminator is visualisation
* [PEAK CAN Interfaces](https://www.peak-system.com/PCAN-USB.199.0.html?L=1) - highly recommended by industry pros but expesive af

## Interface

2011 F150 OBD2 Port
| Pin | Wire | Signal |
|-----|------|--------|
| 3 | Gry/Org | MS CAN + |
| 4 | Blk | Logic Gnd |
| 5 | Blk | Logic Gnd |
| 6 | Wht/Blu | HS CAN + |
| 11 | Vio/Org | MS CAN - |
| 14 | Wht | HS CAN - |
| 16 | Vio/Red | Power |

PCM
| Pin | Wire | Signal |
|-----|------|--------|
| 58 | Wht | HS CAN - |
| 59 | Wht/Blu | HS CAN + |

# CANPico Setup
* Doing everything on Kali despite having little to no linux experience
* [Solid guide on setting up vscode & micropython for the Pico](https://randomnerdtutorials.com/raspberry-pi-pico-vs-code-micropython/#micropico-install)
* Readme in the CANPico/CANHack github has good instructions and includes a step-by-step on compiling toward the bottom
* canhack-master/CANPico/firmware/README.txt
  * Step-by-step references v1.18, but should be v1.22.2 (since 2024-04-01) per notes at top of readme
  * I was unable to build mpy-cross, [presumably](https://github.com/lvgl-micropython/lvgl_micropython/issues/436) because my GCC version is 15.2.0
