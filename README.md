# Subtle Lantern Radio

Showing the way forward.

This is a project to develop an open, libre standalone radio. 

Initially a desktop model, but eventually a portable/HT model as well.

The desktop/mobile model (_The Crossing Watchman_) is made being designed with batter-powered operation in
mind (_The Switchman_).

What's presented here is a baseline for the initial version.

## Software to Standardize

The Standard Software should allow radios of different makes and models to
easily share software and allow users to add new functionality. Ideally this
would become a standardized document and multiple vendors would support it.
This would be considered a minimal implementation. Vendors may have additional
inputs and blocks available.

### Input GNU Radio Blocks

* slr\_var\_frequency
* slr\_var\_volume
* slr\_input\_numeric
* slr\_input\_radiobutton
* slr\_input\_checkbox
* slr\_input\_text
* slr\_radio\_source
* slr\_iq\_source
* slr\_audio\_source
* slr\_text\_source

### Output GNU Radio Blocks

* slr\_gui\_constellation\_sink
* slr\_gui\_freq\_domain\_sink
* slr\_gui\_time\_domain\_sink
* slr\_gui\_histo\_sink
* slr\_gui\_scope\_sink
* slr\_gui\_waterfall\_sink
* slr\_radio\_sink
* slr\_iq\_sink
* slr\_audio\_sink
* slr\_text\_sink

### Environment

* Linux Kernel
* `slr` user
* `inittab` service startup
* Bourne Shell
* ntpd
* dhcpd
* sshd with sftp
* python3
  * alsaaudio
* QT
* GNU Radio
* Apache 2.4
  * Radio and SLR Documentation servered on port 80
* nix Package Manager
* an X server and a tiling window manager
* some protocol for controlling a tiling window manager
  * Maybe based on the i3 IPC protocol? https://i3wm.org/docs/ipc.html

### Filesystem Layout

* /etc/slr/modes/grc/ - contains the modes to display and use for rx and tx as GNU Radio files
* /etc/slr/modes/settings/ - contains the last settings for each mode
* /etc/slr/ui/ - contains the state of the UI
* /etc/slr/memory/ - contains a file per memory slot
* /usr/local/bin/slr/system-menu/ - Modules for the system menu
* /usr/local/bin/slr/system-menu/file\_output - UI for writing IQ and wav data to a file
* /usr/local/bin/slr/system-menu/network\_output - UI for writing IQ and wav data to the network
* /usr/local/bin/slr/system-menu/config - UI for configuring the base system
* /usr/local/bin/slr/system-menu/packages - UI for packages management
* /usr/local/bin/slr/system-menu/plots - UI for the Plots (e.g. waterfall, quadrature, frequency domain, time domain)
* /usr/local/bin/slr/ui - Default Root UI and event listener
* /usr/local/bin/slr/radio\_details - Default UI module for the radio details portion of the screen (e.g. frequency and modes) 
* /usr/local/bin/slr/system\_menu - Container UI module for the system menu
* /etc/slr/system-menu/ - contains the state of the system menu items; filenames same as the application

Along with the normal place to place config files for certain applications or the kernel, e.g.

* /home/slr/.ssh/authorized\_keys - ssh authorized keys
* /etc/network/interfaces - network interface configruation
* /usr/share/gnuradio/grc/blocks/ - blocks available to gnu radio
* /etc/apache24/ - apache configs

### Control API

Service management is done through the init system and `inittab`.

Radios should be able to be configured via `sysfs` and raw data
accessible via `devfs`.

Sound is controlled via ALSA.

### Standard Inputs

Inputs should appear as a keyboard with an input type of `EV_KEY` (except for volume, which uses `EV_ABS`) and an
event code from `input-event-codes.h`.

| Description           | Event Code                         |
| --------------------- | ---------------------------------- |
| Radio Primary Mode    | KEY\_SCROLLDOWN, KEY\_SCROLLUP     |
| Step Size             | KEY\_ZOOMIN, KEY\_ZOOMOUT          |
| System Function       | KEY\_PAGEUP, KEY\_PAGEDOWN         |
| Select                | KEY\_ENTER                         |
| Tuner                 | KEY\_CHANNELUP, KEY\_CHANNELDOWN   |
| Momentary scan        | KEY\_PLAY                          |
| Scan                  | KEY\_PREVIOUSSONG, KEY\_NEXTSONG   |
| Hold                  | KEY\_PAUSE                         |
| Volume                | ABS\_VOLUME                        |

If there are multiple radios, the following is also required.

| Description           | Event Code              |
| --------------------- | ----------              |
| Switch Radio          | KEY\_FORWARD, KEY\_BACK |

#### Optional Inputs

| Description           | Event Code
| --------------------- | ---------------------------------- |
| 4x4 Numpad            | KEY\_NUMERIC\_0-9\*#ABCD           |
| Function              | KEY\_LEFTSHIFT                     |
| Memory                | KEY\_MEMO                          |

### Future Work

It might be useful to bring GNU Radio fully or partially into the kernel
to reduce the amount of copying in and out of kernel memory. We'll have to
see if this becomes a bottleneck or not before considering it furter.

## _The Crossing Watchman_ Mobile Radio

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

### Configurable

### Extendible

* Modes should be flows that can be loaded into gnuradio.

#### Radio Modes

* CW
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
* i3 with i3-msg for controll
