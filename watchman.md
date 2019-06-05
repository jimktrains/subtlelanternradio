# _The Crossing Watchman_ Mobile Radio

## Radio

For the first revision, a commercial SDR (e.g. HackRF or LimeSDR) will be
used as the transceiver.

## System-on-a-Chip

For the first revision, a commercial System-on-a-Chip supporting Linux
(e.g. Raspberry-Pi or ODROID) will run the control software, GNU Radio, and 
SDR driver.

## Front Panel

### Horrible Sketch

![Horrible Sketch](./front-panel.png)

### Displays

* ~5" Transflective Display
  * Low Power + Visible in Sunlight
  * Fast Refresh
  * Hard to find?

### Audio

* ⅛" Headphone Jack
* ⅛" Microphone Jack

### Digital

* 2 USB-A jacks

### Buttons/Switches

* 4x4 SPST Momentary Keypad (0-9\*#ABCD) (DTMF when PTT active)
* SPST Momentary Button - Function
* SPST Momentary Button - Memory

### Knobs

* Knob w/ detentes w/ button
  * Radio Primary Function / Step-Size or Memory
  * System Function / Select
  * Tune / Scan
* Knob w/o detentes w/ button
  * Audio Gain (Volume) / Squelch

## Back panel

* Antenna 
  * 2? (SMA or PL259)? connectors
* Digital
  * 2x Ethernet (part of a 3 port switch; last port goes to device)

## Radio Modes

* CW
* AM
* USB
* LSB
* FM
* FreeDV
* FT8
* PSK31
* RTTY 45.45baud / 170Hz

## System Modes

* Write Raw Data to USB device
  * IQ Data (WAV format, stereo L=I R=Q)
  * Audio Data (WAV format, stereo L=Rx R=Tx)
* Write to network
  * Stream IQ (multicast/destination:port)
  * Stream Audio (multicast/destination:port)
  * Stream Data (multicast/destination:port)
* Config
  * Ethernet
  * Services
  * Load SSH authorized\_keys from USB drive
  * Enable NTP
  * Sync time from GPS
* Attenuation
  * Amount
* Power Output
  * Amount
* Packages 
  * Install nix package
* Date/Time
* Display
  * Enable Constellations
  * Enable Freq Domain
  * Enable Histogram
  * Enable Time Domain
  * Enable Waterfall

## Environment

* BusyBox
  * init
  * ntp
* OpenSSH

