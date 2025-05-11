munin-plugin-gpsd
=================

This is provided in the hope that it is useful.

I am not interested in extending or maintaining it, but may accept
self-explanatory PRs.

If you significantly improve upon it, host the result elsewhere, and/or wish to
maintain it, then please let me know and I'll take this repo down.


Known working setup
-------------------

The following describes a known-working setup.


Raspberry Pi 5 (8GB)
:  - <https://www.amazon.co.uk/dp/B0CK2FCG1K>
   - Base OS is standard Debian distribution with Pi repositories added.
   - Found to be stable despite fluctuating environment (temperature and humidity).
   - Firmware `config.txt`:

            dtparam=uart0=on
            dtoverlay=pps-gpio,gpiopin=18
            enable_uart=1
            init_uart_baud=115200


Amazon Basics Micro SDXC Memory Card with Full Size Adapter, A2, U3, Read Speed up to 100 MB/s, 128 GB, Black
:  - <https://www.amazon.co.uk/dp/B08TJRVWV1>
   - Used only for initial provisioning, before switching to SSD.


GeeekPi P33 M.2 NVME M-Key PoE+ Hat for Raspberry Pi 5, with Official Pi 5 Active Cooler and Aluminum Case, Support M.2 NVMe SSD 2230 2242 2260 2280
:  - <https://www.amazon.co.uk/dp/B0DMW98LBR>
   - **Optional**, but useful if installing in remote location to optimise GPS coverage.
   - PoE+ (tested with power from Cisco SG250-10P fanless switch over ~15m CAT6).
   - Fan noisy when running, but good endurance.
   - Temperature and fan speed can be probed by installing `lm-sensors` package.
   - Case has plenty of headroom for the GPS module above the Pi hat.
   - M.2 NVMe port can be booted from.
   - GPIO pins extended through the board via an extender.
   - Pins of the GPIO extender cropped to leave only those used by the GPS module, without modifying the Pi itself.


Crucial P3 Plus SSD 2TB M.2 NVMe PCIe Gen4 Internal SSD, Up to 5000MB/s, Laptop & Desktop (PC) Compatible, Solid State Drive - CT2000P3PSSD801
:  - <https://www.amazon.co.uk/dp/B0BYW8FLKN>
   - **Optional**, but since the POE+ hat provides SSD it is a good use of the port.
   - Various gymnastics to use SSD as boot device and remove MMC.


AITRIP GT-U7 GPS Module GPS Receiver Navigation Satellite with EEPROM Compatible with 6M 51 Microcontroller STM32 UO R3+ IPEX Active GPS Antenna for Arduino Drone Raspberry Pi Flight (2PCS)
:  - <https://www.amazon.co.uk/dp/B0982PH73K>
   - Pinout matches GPIO header on Pi 5.
   - Found that the provided antenna worked in attic or on window ledge, but not away from windows.
   - Even within the attic it will briefly lose fix at certain times of day.


DollaTek Waterproof GPS Active Antenna 28dB 3 meters SMA port for NEO-6M U-BLOX Car
:  - <https://www.amazon.co.uk/dp/B07DJ3ZQM1>
   - Replaced provided antenna, providing > 10dB additional gain.
   - Worked indoors, including away from window.
   - Works flawlessly. Has never lost fix.


Pardarsey 4 Pcs RF U.FL (IPEX/IPX) Mini PCI to SMA Female Pigtail Antenna Wi-Fi Coaxial RG-178 Low Loss Cable (4 inch (10 cm))
:  - <https://www.amazon.co.uk/dp/B09YY7VG3K>
   - Adapts from female SMA to male U.FL.


GPS software
:  - Used <https://github.com/philrandal/gpsctl> to set baud rate to 115200 in EEPROM:

            gpsctl -a -B 115200; gpsctl --save-config

:  - gpsd running with `-n -s 115200` configured in `/etc/default/gpsd`.
   - GPS monitoring with <https://github.com/terryburton/munin-plugin-gpsd>, which requires the `python3-gps` package.


NTP software
:  - NTPsec from packages, configured with:

            refclock shm unit 0 time1 <ADJUST> noselect refid GPS
            refclock shm unit 1 prefer minpoll 0 refid PPS

:  - `time1` adjustment set based on delay reported by `TOFF` output of `gpsmon`.
