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

---

**Raspberry Pi 5 (8GB)**  
<https://www.amazon.co.uk/dp/B0CK2FCG1K>  
- Base OS is standard Debian distribution with Pi repositories added.  
- Found to be stable despite fluctuating environment (temperature and humidity).  
- Firmware `config.txt`:
  ```ini
  dtparam=uart0=on
  dtoverlay=pps-gpio,gpiopin=18
  enable_uart=1
  init_uart_baud=115200
  ```

---

**Amazon Basics Micro SDXC Memory Card, 128 GB**  
<https://www.amazon.co.uk/dp/B08TJRVWV1>  
- Used only for initial provisioning, before switching to SSD.

---

**GeeekPi P33 M.2 NVME M-Key PoE+ Hat**  
<https://www.amazon.co.uk/dp/B0DMW98LBR>  
- **Optional**, but useful if installing in remote location to optimise GPS coverage.  
- PoE+ (tested with power from Cisco SG250-10P fanless switch over ~15m CAT6).  
- Fan noisy when running, but good endurance.  
- `lm-sensors` can probe temperature and fan speed.  
- Case has headroom for GPS module above the Pi hat.  
- M.2 NVMe port can be booted from.  
- GPIO pins extended through the board via an extender.  
- Pins of the GPIO extender cropped to leave only those used by the GPS module.

---

**Crucial P3 Plus SSD 2TB M.2 NVMe PCIe Gen4**  
<https://www.amazon.co.uk/dp/B0BYW8FLKN>  
- **Optional**, but a good use of the SSD port provided by PoE+ hat.  
- Various steps needed to boot from SSD and remove MMC.

---

**AITRIP GT-U7 GPS Module (2PCS)**  
<https://www.amazon.co.uk/dp/B0982PH73K>  
- Pinout matches GPIO header on Pi 5.  
- Supplied antenna worked in attic or near windows, but not away from windows.  
- Loses fix at certain times of day even indoors.

---

**DollaTek Waterproof GPS Active Antenna 28dB**  
<https://www.amazon.co.uk/dp/B07DJ3ZQM1>  
- Replaced stock antenna, providing >15dB additional gain.  
- Works indoors including away from windows.  
- Works flawlessly and never loses fix.

---

**Pardarsey 4 Pcs RF U.FL (IPEX/IPX) to SMA Pigtail**  
<https://www.amazon.co.uk/dp/B09YY7VG3K>  
- Adapts from female SMA to male U.FL.

---

**GPS software**  
- Used [`gpsctl`](https://github.com/philrandal/gpsctl) to set baud rate to 115200 in EEPROM:
  ```sh
  gpsctl -a -B 115200; gpsctl --save-config
  ```
- `gpsd` running with `-n -s 115200`, configured in `/etc/default/gpsd`.  
- GPS monitoring with [munin-plugin-gpsd](https://github.com/terryburton/munin-plugin-gpsd), which requires the `python3-gps` package.

---

**NTP software**  
- NTPsec from packages, configured with:
  ```
  refclock shm unit 0 time1 <ADJUST> noselect refid GPS
  refclock shm unit 1 prefer minpoll 0 refid PPS
  ```
- `time1` adjustment set based on delay reported by `TOFF` output of `gpsmon`.

---

**Public NTP service**

I provide NTS-enabled NTP server instances at:

  - ntp1.dmz.terryburton.co.uk
  - ntp2.dmz.terryburton.co.uk

Certbot is used to generate the certificates. The Debian NTPsec packages include a deploy hook for Certbot that will handle copying and permissioning the certificate and key files, which can be enabled by setting `NTPSEC_CERTBOT_CERT_NAME` in `/etc/default/ntpsec`.
