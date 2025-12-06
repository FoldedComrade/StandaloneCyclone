#To Do
* Review here: 

#Ref
* [Charm.li - Powertrain Management](https://charm.li/Ford/2011/F%20150%202WD%20V6-3.7L/Repair%20and%20Diagnosis/#Powertrain%20Management/)
* [Some dude managed to get the pre-2011 6R80 working standalone w/ an arduino & CAN messages](https://www.msextra.com/forums/viewtopic.php?t=58522&sid=ca48f336f1902cf77b8cb5a48ad65bdc)
* [V sus US Shift Quick 6 Controller from shady website](https://gogobuy.shop/product/2729059419/?cid=e1e029604b270e32d4575283e2b734d5)
# Powertrain Control Module
For 2011+ the PCM controls both the engine and transmission

## Transmission Controls
### Where TF does the vehicle speed come from
ABS Module monitors wheel speed sensors & broadcasts wheel speed on HS-CAN. PCM monitors trans output shaft speed and broadcasts vehicle speed on HS-CAN.

* The PCM receives the vehicle speed signal imput from the Output Shaft Speed (OSS) sensor. (Service Manual Section 413-01 pg 4568
* The PCM has a vehicle speed input networked from the ABS module (Service Manual 419-03 pg 6214)
* 

CAN Messages (service manual pg 6031)
| Broadcast Message | Originating Module | Network Type | Receiving Module(s) |
|-------------------|--------------------|--------------|---------------------|
| Accelerator pedal position | PCM | HS-CAN | - TCCM<br>- ABS module<br>- BCM<br>- RCM |
| Vehicle Speed | PCM | HS-CAN | - ABS module<br>- BCM<br>- IPC<br>- OCSM<br>- PAM<br>- PSCM<br>- RCM<br>-SCCM |
| Vehicle Speed (gateway | BCM | MS-CAN | - APIM<br>- ACM<br>- GPSM<br>- PRB module |
| Wheel Speed data | ABS module | HS-CAN | - TCCM<br>- BCM<br>- IPC<br>- PSCM<br>- PCM |

Diagnostic PID Chart (service manual 307-01 pg 3319)
| PID Name | PID Description | Units |
|----------|-----------------|-------|
| AXLE | Axle Ratio | Ratio |
| GEAR | Gear Commanded by Module | Mode |
| GEAR_OSC# | Gear Commanded by Output State Control | Mode |
| GEAR_RAT | Gear Ratio Commanded | Ratio |
| HRSH_SHFT# | Firm Shift | On/Off |
| IN_GEAR | In Gear-Transmission is applying a load to engine | In Gear |
| ISS_F | Input Shaft Speed Sensor Fault | No Fault/Yes Fault |
| ISS_SRC | Input Shaft Speed Sensor | rpm |
| LOAD | Engine Load | Percentage |
| OSS_F | Output Shaft Speed Reliable | No Fault/Yes Fault |
| OSS_SRC | Unfiltered Output Shaft Speed | rpm |
| TIRESIZE | Tire size | Rev per Mile|
| TRAN_RAT | Transmission Gear ratio | Ratio |
| VSOUT_F | Vehicle Speed Output Status | No Fault/Yes Fault |
| VSS | Vehicle Speed Sensor (VSS) | Speed |

Shift Speeds (Ref Service Manual Section 307-01, pg3154)
| Throttle Position | Shift | Km/H | MPH |
|-----|------|--------|-----|
| Light Throttle | 1-2 | 13-21 | 8-13 |
| TP Voltage | 2-3 | 26-34 | 16-21 |
| 1.25 Volts | 3-4 | 35-43 | 22-27 |
| . | 4-5 | 56-64 | 35-40 |
| . | 5-6 | 71-79 | 44-49 |
| . | 5-6* | 100-105 | 62-65 |
| Closed Throttle | 6-5 | 56-64 | 35-40 |
| . | 5-4 | 40-48 | 25-30 |
| . | 4-3 | 13-21 | 8-13 |
| . | 3-2 | 3-11 | 2-7 |
| . | 2-1 | 2-6 | 1-4 |
| Wide Open Throttle | 1-2 | 45-53 | 28-33 |
| . | 2-3 | 87-95 | 54-59 |
| . | 3-4 | 140-148 | 87-92 |
* Vehicles equipped with a 4.10:1 FDR
(1kph = 0.62 mph)

## Vehicle Speed Output
Powertrain Control Module - Vehicle Speed Output (PCM-VSO)
	The PCM-VSO speed signal subsystem generates vehicle speed information for distribution to those electrical/electronic modules and subsystems that require vehicle speed data. This subsystem senses the transmission output shaft speed with a sensor. The data is processed by the PCM and distributed as a hardwired signal or as a message on the vehicle communication network.

The key features of the PCM-VSO system are to:
-	infer vehicle movement from the OSS sensor signal.
-	convert transmission output shaft rotational information to vehicle speed information.
-	compensate for tire size and axle ratio with a programmed calibration variable.
-	use a transfer case speed sensor (TCSS) for four wheel drive (4WD) applications.
-	distribute vehicle speed information as a multiplexed message and/or an analog signal.

The signal from a non-contact shaft sensor, such as an OSS or TCSS, mounted on the transmission (automatic, manual, or 4WD transfer case) is sensed directly by the PCM. The PCM converts the OSS or TCSS information to 8,000 pulses per mile, based on a tire and axle ratio conversion factor. This conversion factor is programmed into the PCM at the time the vehicle is assembled and can be reprogrammed in the field for servicing changes in the tire size and axle ratio. The PCM transmits the computed vehicle speed and distance traveled information to all vehicle speed signal users on the vehicle. VSO information can be transmitted by a hardwired interface between the vehicle speed signal user and the PCM, or by a speed and odometer data message through the vehicle communication network data link.

The PCM-VSO hardwired signal waveform is a DC square wave with a voltage level of 0 to VBAT. Typical output operating range is 1.3808 Hz per 1 km/h (2.22 Hz per mph).

# Transmission
## Description
The 6R80 transmission has a main assembly which contains:
* Turbine Shaft Speed (TSS) sensor
* Output Shaft Speed (OSS) sensor
* An internal Trnasmission Range (TR) sensor
* Transmission Fluid Temperature (TFT) sensor
* Line Pressure Control (LPC)
* TCC solenoid
* Five electronically controlled shift solenoids

In the event of a system fault, the PCM also provides for Failure Mode Effect Management to maintain maximum functional operation of the transmission with a minimum power reduction. In the event of a total loss of control or electrical power, the basic transmission functions park, reverse, neutral and drive are retained. Also 3rd or 5th gear is retained by the transmissions hydraulic system. The default gear retained is dependent upon the gear selected at the time of a failure.

## Transmission Electronic Control System
from Service Manual Section 307-01 pg 3226

The PCM controls the electronic functions of this transmission. A plastic molded leadframe is bolted to the main control assembly. The leadframe contains the Turbine Shaft Speed (TSS), Output Shaft Speed (OSS), Transmission Fluid Temperature (TFT) and Transmission Range (TR) sensors.

The PCM receives input signals from engine and transmission sensors and uses these inputs to control line pressure, shift time, Torque Converter Clutch (TCC), and shift solenoids. The PCM also provides power and ground for the reverse lamp relay coil and provides a PARK/NEUTRAL start enable signal.

The following is a list of direct engine and driver inputs to the PCM along with module information from the vehicle Controller Area Network (CAN):

Engine speed
* Engine torque
* Engine coolant temperature
* Throttle position
* Accelerator pedal
* Brake pedal
* ABS wheel speed
* Traction control status

If the transmission loses complete electronic control, it operates in a fail-safe mode with:
* Maximum line pressure in all transmission ranges
* Functional PARK, REVERSE and NEUTRAL positions
* Operation in 3rd or 5th gear (depending on the failure conditions) when the selector lever is in the DRIVE, 3, 2 or 1 position
* TCC is released in all transmission ranges and gears

### Turbine Shaft Speed (TSS) Sensor
The TSS sensor is a Hall-effect type sensor that provides a signal to the PCM that changes in frequency as the rotating speed of the forward (1,2,3,4) clutch cylinder varies.

The PCM compares the TSS sensor signal with the engine speed information to determine the amount of slip occurring in the torque converter.

The PCM also compares the TSS sensor signal with the OSS sensor signal to determine the gear ratio provided by the rear planetary gearset.

The PCM uses the TSS sensor signal as an input for its strategies for shifts and TCC operation. The PCM also uses the TSS sensor signal for transmission fault detection and diagnostics.

Refer to the component illustration at the beginning of this procedure for the location of the TSS sensor.

### Output Shaft Speed (OSS) Sensor

The OSS sensor is a Hall-effect type sensor that provides a signal to the PCM that changes in frequency as the rotating speed of the output shaft ring gear varies.

The PCM also compares the OSS sensor signal with the TSS sensor signal to determine the gear ratio provided by the rear planetary gearset.

The PCM uses the OSS sensor signal as an input for its strategies for shifts and TCC operation. The PCM also uses the OSS sensor signal for transmission fault detection and diagnostics.

Refer to the component illustration at the beginning of this procedure for the location of the OSS sensor.

### Transmission Range (TR) Sensor
The TR sensor has a set of Hall-effect sensors that have a pattern of ON/OFF states which are dependant on the PARK, REVERSE, NEUTRAL, DRIVE, 3, 2 or 1 position of the manual valve.

The TR sensor also provides signals for the starting system and the reverse lights.

The PCM uses the TR sensor signal as an input for its strategy for shifting and TCC operation. The PCM also uses the TR sensor signal for transmission fault detection and diagnostics.

Refer to the component illustration at the beginning of this procedure for the location of the TR sensor.

## Transmission Diagnosis and Testing
### Shift Concerns: Timing - Early/Late
211 - ROUTINE
| Possible Component | Reference/Action |
|--------------------|------------------|
| Tire size change, axle ratio change | Verify the vehicle has the original equipment. Refer to the certification label. Changes in the tire size and axle ratio will affect shift timing. |

# Transmission Torque Specs
From Service Manual Section 07-301 pg3154
| Description | Nm | lb-ft | lb-in |
|-------------|----|-------|-------|
| Flexplate inspection cover bolts | 48 | 35 | - |
| Flexplate-to-torque converter nuts | 40 | 30 | - |
| Park pawl pin bolt | 23 | 17 | - |
| Park rod actuating plate | 12 | - | 106 |
| Pump plate assembly-to-pump body bolts | 15 | - | 133 |
| Solenoid bracket bolts | 6 | - | 53 |
| Transmission fluid fill plug | 35 | 26 | - |
| Transmission fluid pan bolts | refer to procedure |
| Transmission insulator and retainer bolts | 90 | 66 | - |
| Transmission insulator and retainer nuts | 103 | 76 | - |
| Transmission-to-engine bolts | 48 | 35 | - |
