# Subtle Lantern Radio

Showing the way forward.

This is a project to develop an open, libre standalone radio. 

Initially a desktop model, but eventually a portable/HT model as well.

The desktop model is made being designed with batter-powered operation in
mind.

What's presented here is a baseline for the initial version.

There are two items being presented here simultaneously:

* A stand-alone mobile radio
* The interface for software that is replaceable and upgradable.


## Internal Configuration

### Radio

For the first revision, a commercial SDR (e.g. HackRF or LimeSDR) will be
used as the transceiver.

### System-on-a-Chip

For the first revision, a commercial System-on-a-Chip supporting Linux
(e.g. Raspberry-Pi or ODROID) will run the control software, GNU Radio, and 
SDR driver.

## Front Panel

### Horrible Sketch

![Horrible Sketch](./front-panel.png)

### Displays

* ~3.5" Transflective Display
  * Low Power + Visible in Sunlight
  * Fast Refresh

### Audio

* 1 TRS Headphone Jack
* 1 TRS Microphone Jack

### Digital

* 2 USB-A jacks

### Buttons/Switches

* 4x4 SPST Momentary Keypad (0-9\*#ABCD) (DTMF when PTT active)
* DTSP Switch - Numeric vs DTMF pad
* SPST Momentary Button - Function
* SPST Momentary Button - Memory

### Knobs

* Knob w/ detentes w/ button
  * Radio Primary Function / Step-Size or Memory
  * System Function / Select
  * Tune / Scan
* Knob w/o detentes
  * Audio Gain (Volume)
  * Squelch

## Back panel

* Antenna 
  * 3? (SMA or PL259)? connectors
* Digital
  * Ethernet

## Radio Modes

### Configurable

* Potentially just gnuradio files in folders? `$MENU/<mode>.grc`

### Extendible

* Modes should be flows that can be loaded into gnuradio.

#### Radio Modes

* AM
* USB
* LSB
* FM
* FreeDV
* FT8
* PSK31
* RTTY 45.45 / 170Hz

## System Modes

* Write Raw Data to USB device
  * IQ Data (WAV format, stereo L=I R=Q)
  * Audio Data (WAV format, stereo L=Rx R=Tx)
* Ethernet
  * DHCP/Static (IP/Subnet)
  * Stream IQ (multicast/destination:port)
  * Stream Audio (multicast/destination:port)
  * Enable SSH
  * Load SSH authorized\_keys from USB drive
* Attenuation
  * Amount
* Power Output
  * Amount

## Notes

* Device can be configured over SFTP
* Device can act as an SSH server (`data`, `iq`, `control`, `system`, `logs` as the ssh command bring up those respective shells)
