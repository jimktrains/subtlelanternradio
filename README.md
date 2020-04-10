# Subtle Lantern Radio

_Lighting the way forward._

This is a project to develop an open, libre standard for radios to use,
allowing for extensibility, configurability, control, and
experimentation by the operator.

What's presented here is a baseline for the initial version.


The Standard Software should allow radios of different makes and models
to easily share software and allow users to add new functionality.
Vendors should feel welcomed to provide and compete on, among other
things:

* hardware-backed GNU Radio blocks
* user interface design (provided that the user is able to replace it)
* features
* performance
* the radio hardware itself

not to mention competing on the price or quality the radio hardware.

## Minimal Required Software

* Linux Kernel
* `inittab` service startup
* Bourne Shell (perhaps via busybox)
* TCL/TK
* GNU Radio, latest stable
    * python, latest stable also compatible with GNU Radio
* nix Package Manager
* All user input should appear and be processable through devfs/kernel "input
  protocol" (e.g. `/dev/input/eventX`). The mapping of physical device to event
  should be contained in the manual.
* All radio functions must be controllable via devfs or devfs and
  documented in the manual.
* Kernel drivers must be free/libre software.
    * The radio hardware itself may have closed-source firmware, but the
      control system must be able to talk to that hardware via open
      software.
    * Closed-source software may be provided as applications, e.g. the user
      interface, so long as they are not required to use the radio.

### Environment

* `slr` user
    * Initial password must be unique per device
* Manual must be available on disk
    * A text-only version, along side a traditional PDF, would be greatly
     appreciated.

### USB

The following must be able to be provided via USB when connected:

* A serial console with baud=115200 databits=8 parity=no stop=1
* USB mass storage class devices exposing all storage partitions
   * The main partition must be writable
   * Read-only recovery partitions may be provided
* A 8-bit 44.1k PCM audio output terminal channel per receiver
* A 8-bit 44.1k PCM audio input terminal channel per transmitter

Additional serial parameters may be provided at the vendors digression.
These parameters must be accessible and discernible via the device
descriptor.

Additional audio channels formats may be provided at the vendors
digression. These channels must be accessible and discernible via the
device descriptor.

Data channels (e.g. I/Q data streamed over 2 channels, "s-meter" over 1
channel) may be provided at the vendors digression as an appropriate USB
Device class or as a serial console stream. These channels must be
accessible and discernible via the device descriptor. Format details
should be described in the manual.

### Recovery

If a recovery partition is provided, there must be a means of
reflashing the main partition an radio firmware via:

* bootloader/UEFI via the serial console and must not require network access.
* hardware button held on startup

If a recovery partition is not provided, some form or removable storage
which can boot the system must be present. This may include a USB-A port
that can be booted from.

### Display

If a textual display is provided it must be exposed as a devfs device. 

If a graphical display is provided an X11 server should be provided.
This includes low-res pixel-addressable displays.

### Networking

If an Ethernet or WiFi is provided, the following software must be
installed:

* NTP Client
* DHCP Client
* sshd with sftp
* iptables
* Web Server on port 80
    * should default to only allow connections from the local network
    * Radio Documentation
    * SLR Documentation

### Examples

#### devfs Config

**TODO:** Perhaps spec out what should be the minimal standard here?

* /sys/devices/radios/
   * case
       * 0
          * model
          * serial_number
   * rx
       * 0
          * frequency - frequency in Hertz
          * squelch - 0-100
          * mode - one of the available modes
          * available_modes - new line delimeted list of modes, e.g. CW,
            AM, FM, USB, LSB, MT63-2000L, Olivia-8-500, FreeDV-700D
          * output_device - file (inclduing named pipes and devfs
            devices) to route decoded audio or data to
          * tone
              * type - tone type, if used otherwise empty, e.g. CTCSS, DCS, DTSS
              * freq - tone frequency, e.g. 131.8
          * model
          * serial_number
       * 1
          * frequency
          * power
          * mode
          * available_modes
          * input_device
          * tone
              * type
              * freq
          * model
          * serial_number
   * tx
       * 0
          * frequency
          * power
          * mode
          * available_modes
          * input_device
          * tone
              * type
              * freq
          * model
          * serial_number
  
## Minimal Required Hardware

* USB-C or Micro-USB port
   * If the device has a battery, the USB port should be capable of
     charging the device.

## Possible Path Forward

* Build a minimal Ubuntu System image

    * Python
    * ncurses
    * JACK
      * ALSA
    * GNU Radio

* Build a Fauxdio (faux radio) driver

  I'd like to test the rest of the system without having to deal with a
  real device just yet.

  https://www.apriorit.com/dev-blog/195-simple-driver-for-linux-os may
  be a good starting point

  The Fauxdio should provide data to build the rest of the system
  against. I'd image leveraging a seperate GNU Radio instance or other
  external programs for much of this, especially the I/Q encoding. For
  instance,
      * Repeat back "transmitted" I/Q sequences after a delay
      * Adjustably adding noise to received data
      * Audio modes (e.g. AM and FM) couldencode predictable data, e.g.
        current time or settings via DTMF or TTS
      * Data modes (e.g. AFSK1200, PSK31) could encode predictable data,
        e.g. current time or settings

* Become very familar with ALSA (and maybe JACK) and how to configure
  them.

* Build out a minimal TUI 
    * Use the keyboard as input
    * Strive to use only a minimal subset of keys, such as would be
      found on a radio in order to think more about the UI.
        * W,S - Volume
        * R,F - Squelch
        * Enter - Enter/misc button
        * 0-9ABCD - 16-key keypad
        * ZXCV - Misc buttons

* Get this working on a SBC
    * Board Requiremens
        * At least 1 USB Device port
        * At least 1 USB Host port
        * Must be able to run Debain
    * Possible options
        * ODROID-C1+

* Work on the USB Device requirements.
    * http://www.linux-usb.org/gadget/
    * http://www.staroceans.org/kernel-and-driver/Programming%20Guide%20for%20Linux%20USB%20device%20drivers.pdf
    * https://www.kernel.org/doc/html/v4.13/driver-api/usb/writing\_usb\_driver.html

* Obtain and get working with the UI and sensible devfs endpoints a
  HackRF or LimeSDR (or my TM-V7 if its data port isn't busted) (or even
  an RTL-SDR for an RX-only radio, but it'd give I/Q data and the TM-V7
  won't)
