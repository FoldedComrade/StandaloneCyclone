#To Do
* Review here: 

#Ref
* [Charm.li - Powertrain Management](https://charm.li/Ford/2011/F%20150%202WD%20V6-3.7L/Repair%20and%20Diagnosis/#Powertrain%20Management/)
* [Some dude managed to get the pre-2011 6R80 working standalone w/ an arduino & CAN messages](https://www.msextra.com/forums/viewtopic.php?t=58522&sid=ca48f336f1902cf77b8cb5a48ad65bdc)
* [V sus US Shift Quick 6 Controller from shady website](https://gogobuy.shop/product/2729059419/?cid=e1e029604b270e32d4575283e2b734d5)
# Powertrain Control Module
For 2011+ the PCM controls both the engine and transmission

# Transmission Controls
Shift Speeds (Ref Service Manual Section 307-01, pg3154)
| Throttle Position | Shift | Km/H | MPH |
|-----|------|--------|-----|
| Light Throttle | 1-2 | 13-21 | 8-13 |
| TP Voltage | 2-3 | 26-34 | 16-21 |
| 1.25 Volts | 3-4 | 35-43 | 22-27 |
| | 4-5 | 56-64 | 35-40 |
| | 5-6 | 71-79 | 44-49 |
| | 5-6* | 100-105 | 62-65 |
| Closed Throttle | 6-5 | 56-64 | 35-40 |
| | 5-4 | 40-48 | 25-30 |
| | 4-3 | 13-21 | 8-13 |
| | 3-2 | 3-11 | 2-7 |
| | 2-1 | 2-6 | 1-4 |
| Wide Open Throttle | 1-2 | 45-53 | 28-33 |
| | 2-3 | 87-95 | 54-59 |
| | 3-4 | 140-148 | 87-92 |
* Vehicles equipped with a 4.10:1 FDR

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
