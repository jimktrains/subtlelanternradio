# Subtle Lantern Radio

_Lighting the way forward._

This is a project to develop an open, libre standard for radios to use allowing
for extensibility, configurability, control, and experimentation by the
operator.

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
* python 3.7
  * alsaaudio
* QT
* GNU Radio
* Apache 2.4
  * Radio and SLR Documentation served on port 80
* nix Package Manager
* an X server
* iptables

### UI

The UI will consist of an application capable of loading python modules and
passing QT Frames to the module. It should also read the radio modes and system
modes to present from the file system. When selected, the appropriate radio or
system mode should be load and a frame for it to populate in the UI passed.

### Filesystem Layout

* /etc/slr/modes/ - contains the last settings for each mode
* /etc/slr/ui/ - contains the state of the UI
* /etc/slr/memory/ - contains a file per memory slot
* /usr/local/bin/slr/modes/ - contains the modes as executables (e.g. python gnuradio scripts) to display and use for rx and tx
* /usr/local/bin/slr/system-menu/ - Modules for the system menu
* /usr/local/bin/slr/system-menu/config - UI for configuring the base system
* /usr/local/bin/slr/system-menu/packages - UI for packages management
* /usr/local/bin/slr/radio\_details - Default UI module for the radio details portion of the screen (e.g. frequency and modes) 
* /usr/local/bin/slr/system\_menu - Container UI module for the system menu
* /etc/slr/system-menu/ - contains the state of the system menu items; filenames same as the application

Along with the normal place to place config files for certain applications or the kernel, e.g.

* /home/slr/.ssh/authorized\_keys - ssh authorized keys
* /etc/network/interfaces - network interface configruation
* /usr/share/gnuradio/grc/blocks/ - blocks available to gnu radio
* /etc/apache24/ - apache configs
* /etc/X11/default-display-manager - references the main UI

### Control API

Service management is done through the init system and `inittab`.

Sound is controlled via ALSA.

Radios should be able to be configured via `sysfs` and raw data
accessible via `devfs`. I'd envision that all settings and parameters for the
radio could be read or set by reading or writing to a file for the device in
`sysfs`.


### Standard Inputs

Essentially, the frontpanel should appear as a keyboard and there would be
pre-defined keycodes for standard required and standard optional buttons,
knobs, &c. Implementors are free to add non-standard keys and assign them an
otherwise unused keycode. I'm probably a little premature with the list below,
but it's a conversation starter.

Inputs should appear as a keyboard with an input type of `EV_KEY` (except for
volume, which uses `EV_ABS`) and an event code from `input-event-codes.h`.

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
