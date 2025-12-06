# StandaloneCyclone
Trying to run a Fod Cyclone Duratec 37 and 6R80 transmission on the OEM PCM and no other modules. Working project.

# Powertrain
* [Powertrain notes](https://github.com/FoldedComrade/StandaloneCyclone/blob/main/Powertrain/Powertrain.md)
Donor Vehicle: 2011 F-150 (2WD)
~160k miles
* Note the Duratec 37 out of any transverse-mounted application has an internal water pump known for failing and dumping all the coolant into the block. Wild design choice.


## Engine
3.7L Cyclone

## Transmission
6R80 6 speed automatic

# Control
* PCM flashed to delete PATS - couldn't for the life of me find any record online of someone figuring out a workaround
* Doing all the CAN stuff on a Dell Latitude 5400 running Kali which I've come to learn may not have been a great decision, but we'll see how far it takes us

Do-outs
* Not sure the PCM is going to love not having O2 sensors
* Not sure the PCM is going to love not having an ABS module, which seems to be what the PCM uses for vehicle speed source
* Not sure the PCM is going to love that the final drive ratio is wildly different than what it's programmed for

## References
### Ford
* [Operation Charm](https://charm.li/Ford/2011/F%20150%202WD%20V6-3.7L/Repair%20and%20Diagnosis/) is fucking incredible - factory manual, free & public, no ads, easy to navigate.
* 
### CAN
Dr. Ken Tindell seems to be the CAN godfather
* [Canis Labs](https://canislabs.com/)
* [Personal Blog](https://kentindell.github.io/)

Yogesh Ojha has a helpful getting started 101
* [Part 1](https://medium.com/@yogeshojha/car-hacking-101-practical-guide-to-exploiting-can-bus-using-instrument-cluster-simulator-part-i-cd88d3eb4a53)
* [Part 2](https://medium.com/@yogeshojha/car-hacking-101-practical-guide-to-exploiting-can-bus-using-instrument-cluster-simulator-part-ee998570758)
* [Part 3](https://medium.com/@yogeshojha/car-hacking-101-practical-guide-to-exploiting-can-bus-using-instrument-cluster-simulator-part-ea40c05c49cd)

Brent Picasso was able to [log wheel speeds, steering agnle, brake pressure via CAN](https://www.autosportlabs.com/reverse-engineering-ford-mustang-2011-2014-can-bus-steering-angle-throttle-position-brake-pressure-and-wheel-speeds/)

Ford
* Service Manual CANBus starts at pg 6020
* CAN Message Chart on pg 6031

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

### 2011 F150 PCM Interface
PCM has three connectors - C175 E/B/T

[C175B](https://charm.li/Ford/2011/F%20150%202WD%20V6-3.7L/Repair%20and%20Diagnosis/Diagrams/Connector%20Views/Engine%20Control%20Module/C175B%20Powertrain%20Control%20Module%20%28PCM%29/) is the Body/Cowl connector
| Pin | Wire | Signal |
|-----|------|--------|
| 58 | WHT | HS CAN - |
| 59 | WHT/BLU | HS CAN + |
| 62 | YEL/RD | KAPWR |
| 67 | YEL/GRY |  VPWR1 |
| 68 | YEL/GRY |  VPWR2 |
| 69 | BLK/YEL |  PWRGND |
| 70 | BLK/YEL |  PWRGND |

* VPWR (Vehicle Power is the primary source of PCM power. VPWR is switched through the PCM power relay and is controlled by the ignition switch
* PWRGND (Power Ground) circuit(s) is connected directly to the battery negative (B-) terminal. PWRGND provides a return path for the PCM vehicle power (VPWR) circuits
* KAPWR (Keep Alive Power) provides a constant voltage input independent of ignition switch state to the PCM. This voltage is used by the PCM to maintain the KAM.
* ~~VBPWR (Vehicle Buffered Power) is a regulated voltage supplied by the PCM to vehicle sensors. These sensors require a constant 12 volts for operation and cannot withstand VPWR voltage variations. VBPWR is regulated to VPWR minus 1.5 volts and is also current limited to protect the sensors.~~
     * Not actually sure which one this is at the moment....
* VREF (Vehicle Reference Voltage) is a consistent positive voltage (5 volts plus or minus 0.5 volt) provided by the PCM. VREF is typically used by 3-wire sensors and some digital input signals.
     * This might be what the Ford wiring manual refers to as ```C REF``` or ```C-REF``` (C175B-52). Further research required...

[C175E](https://charm.li/Ford/2011/F%20150%202WD%20V6-3.7L/Repair%20and%20Diagnosis/Diagrams/Connector%20Views/Engine%20Control%20Module/C175E%20Powertrain%20Control%20Module%20%28PCM%29/) is the Engine connector

[C175T](https://charm.li/Ford/2011/F%20150%202WD%20V6-3.7L/Repair%20and%20Diagnosis/Diagrams/Connector%20Views/Engine%20Control%20Module/C175T%20Powertrain%20Control%20Module%20%28PCM%29/) is the Trans connector

## CANPico
* [Solid guide on setting up vscode & micropython for the Pico](https://randomnerdtutorials.com/raspberry-pi-pico-vs-code-micropython/#micropico-install)

After two days trying to compile firmware I realized it is in fact I who am the fool, and the repo includes pre-compiled firmware. Learning is fun.

### Basics
Following [Dr. Tindell's CANPico intro video](https://www.youtube.com/watch?v=MmRj8UEvXXM&t=1s)...\
canpico.py seems to no longer be included in the CANPico github directory, found another guy who uploaded it).\
Host setup...\
Download min repo, start by changing to whatever directory you want to put it in\
```$ git clone https://github.com/min-protocol/min.git```\
move `canpcap.py` from min/ to min/host\
```$ mv canpcap.py /host```\
Canpico setup...\
```
>>> from canpico import *
>>> c = CAN()
>>> cp = CANPico(c)
```
Create & transmit frames...
```
>>> hello = CANFrame(CANID(0x123), data=b'hello')
>>> world = CANFrame(CANID(0x124), data=b'world')
>>> c.send_frames(hello, world)
```
Receive frames...\
```>>> hello, world = c.recv()```\
Print frames in buffer...
```
>>> hello
CANFrame(CANID(id=5123), dlc=5, data=68656c6c6f, timestamp=3407821044
>>> hello.get_data()
b'hello'
>>> world
CANFrame(CANID(id=5124), dlc=5, data=776f726c64, timestamp=3407821220
>>> world.get_data()
b'world'
```
Monitor & print all frames for troubleshooting/low traffic only. Micropython takes a few ms to print frames and the buffer can pretty easily become saturated and start dropping frames.\
```>>> cp.mon()```

### Wireshark
Summarize [Dr. Tindell's CANPico Wireshark video](https://www.youtube.com/watch?v=Yga9kKK_UjQ)\
Bus monitor for high-traffic applications. Consists of client (CANPico) code/functions in `canpico.py` and host code `canpcap.py`. Client creates a min instance that receives a block of bytes instead of a list of CAN frame instances, then send to host using min over USB serial. Host waits for min handler and decodes bytes into pcapng format which wireshark can handle.\

Client...\
```>>> cp.minmon()```\
Host...\
```$ python3 canpcap.py -p /dev/ttyACM1 | wireshark -i -```

## CANable
I violated the field engineer's cardinal rule (always buy three - one to use, one to lose, one to steal) and only purchased one CANPico. Needed something for benchtesting and was in a hurry so picked up a CANable/[UCAN board off amazon]([https://www.amazon.com/FYSETC-STM32F072-Interface-Candlelight-Firmware-Pack](https://www.amazon.com/FYSETC-STM32F072-Interface-Candlelight-Firmware-Pack/dp/B0BPY5HY6C/ref=sr_1_3).
* [CANable Getting Started Guide](https://canable.io/getting-started.html)
* 
## Ford Passive Anti-Theft System (PATS)
### From the 2014 F-150 Workshop Manual (rev 10/25/2013)
The Passive Anti-Theft System (PATS) is controlled by the Body Control Module (BCM). There are 2 main
checks that PATS carries out before allowing the engine to start. If either of these checks fail, PATS does
not allow the engine to start and STARTING SYSTEM FAULT is displayed in the message center. These
two checks are the BCM verifying the PCM ID to make sure it matches the PCM ID stored in memory and to
verify that a programmed key was used to turn the ignition to the RUN or START position.

The first check is initiated by the BCM waking up the PCM by supplying voltage on the wake up control
circuit. The BCM activates the wake up control circuit when:
the driver door is opened.

* a remote start request is received (if equipped with factory remote start).
* the brake pedal is pressed.
* a key is inserted into the ignition.
* or when the ignition is in the RUN or START position.

Once the PCM is awake, the BCM sends the PCM a challenge message over the High Speed Controller
Area Network (HS-CAN). When the PCM receives the challenge message, it generates a response and
sends it back to the BCM . If the response from the PCM does not match the response in the BCM memory,
this first check fails and the engine will not start.

Once the PCM ID has been verified, PATS performs the second check to make sure a programmed key is in
the ignition. When the BCM determines the ignition has been turned to the RUN or START position, it
generates a challenge message. It sends the challenge message to the PATS transceiver on the transmit
(TX) circuit. The transceiver in turn reads the key and generates a response message that is sent back to
the BCM on the receive (RX) circuit. If the message received from the transceiver does not match a key
stored in the BCM memory, the engine will not start.

If both the PCM ID and key verification pass, PATS is enabled and allows the engine to start. PATS cannot
disable an engine that has already been started.
