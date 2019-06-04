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
* QT
* GNU Radio
* nix Package Manager
* some protocol for controlling a tiling window manager
  * Maybe based on the i3 IPC protocol? https://i3wm.org/docs/ipc.html

### Filesystem Layout

* /etc/slr/modes/grc/ - contains the modes to display and use for rx and tx as GNU Radio files
* /etc/slr/modes/settings/ - contains the last settings for each mode
* /etc/slr/ui/ - contains the state of the UI
* /etc/slr/memory/ - contains a file per memory slot
* /home/slr/.ssh/authorized\_keys - ssh authorized keys
* /usr/share/gnuradio/grc/blocks/ - blocks available to gnu radio
* /usr/local/bin/slr/system-menu/ - Applications to run when selected by the system menu
* /etc/slr/system-menu/ - contains the state of the system menu items; filenames same as the application

### Control API

All items relative to `/dev/slr/`.

Service management is done through the init system and `inittab`

* radios - All available radios
* radios/\<number\> - specific radio (radios may expose additional settings as
  files, e.g. bandwidth, oversampling, sample\_rate, if\_gain, &c)
* radios/\<number\>/mode - mode for the radio / name of grc used
* radios/\<number\>/attenuation - attenuation setting for the radio
* radios/\<number\>/gain - gain setting for the radio in dB
* radios/\<number\>/frequency - frequency the radio is set to, in Hz
* radios/\<number\>/step - scanning step size, in Hz
* radios/\<number\>/ranges - frequncy ranges this radio can tune to
* radios/\<number\>/ranges/\<number\> - specific range
* radios/\<number\>/ranges/\<number\>/min - minimal extent of range in Hz, inclusive
* radios/\<number\>/ranges/\<number\>/max - maximal extent of range in Hz, inclusive
* radios/\<number\>/iq - raw IQ data
* nic - All available NICs
* nic/\<number\> - specific NIC
* nic/\<number\>/ipv4 - IPv4 settings
* nic/\<number\>/ipv4/address - IPv4 address
* nic/\<number\>/ipv4/subnet - IPv4 subnet
* nic/\<number\>/ipv6 - IPv6 settings
* nic/\<number\>/ipv6/address - IPv6 address
* nic/\<number\>/ipv6/prefixlen- IPv6 prefix length
* nic/\<number\>/mac - MAC address
* inputs - Inputs
* inputs/selected\_radio - currently selected radio
* inputs/\<number\> - Specific input
* inputs/\<number\>/value - Value of the input
* inputs/\<number\>/name - name of the input
* inputs/\<number\>/type - type of input
* sound - sound control
* sound/volume - global volume
* sound/\<number\> - specific device
* sound/\<number\>/type - type of sound device
* sound/\<number\>/volume - volume
* sound/\<number\>/enabled - enabled

#### Standard Inputs

Events sent to `/dev/input/slr` with an input type of `EV_KEY` (except for volume, which uses `EV_ABS`) and an
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


The audo subsystem also announces events into it's own device (/dev/input/snd/\<number\>) with inputs types of `EV_SW`

| Description           | Event Code                         | Event Value                         |
| --------------------- | ---------------------------------- | -----------                         |
| Push-to-Talk          | SW\_RFKILL\_ALL                    | set when not transmitting           |
| Headphone Inserted    | SW\_HEADPHONE\_INSERT              | set when headphones are plugged in  |
| Micropone Inserted    | SW\_MICROPHONE\_INSERT             | set when a microphone is plugged in |

#### Optional Inputs

| Description           | Event Code
| --------------------- | ---------------------------------- |
| 4x4 Numpad            | KEY\_NUMERIC\_0-9\*#ABCD           |
| Function              | KEY\_LEFTSHIFT                     |
| Memory                | KEY\_MEMO                          |


## _The Crossing Watchman_

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
* DTSP Switch - Numeric vs DTMF pad
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
* Ethernet
  * DHCP/Static (IP/Subnet)
  * Stream IQ (multicast/destination:port)
  * Stream Audio (multicast/destination:port)
  * Stream Data (multicast/destination:port)
  * Enable SSH/SFTP
  * Load SSH authorized\_keys from USB drive
* Attenuation
  * Amount
* Power Output
  * Amount
* Software Updates
  * Install nix package
* Date/Time
  * Enable NTP
  * Sync from GPS
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
