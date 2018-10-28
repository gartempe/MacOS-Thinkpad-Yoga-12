# MacOS on Thinkpad Yoga 12
Hackintosh MacOS Mojave on Thinkpad Yoga 12 with ACPI hotpatches

------------------
## Pre-Installation

### 1. Warning - Achtung - Attention : check you BIOS version !!!

There is a known bug in embeded controller, where **CPU fan stop triggering**. This has been fixed with Bios version 1.20.

*I've experimented this bug (with BIOS 1.03 JEET47WW), and the CPU rises up quickly to 99°C at full load (even with short load). This is extremly dangerous for the health and lifespan of your laptop.*

**So you should update BIOS to version 1.20 at minimum !**

BIOS Summary of Changes at https://download.lenovo.com/pccbbs/mobiles/jeuj84ww.txt

<1.20>
- (New) Updated Fan table
- (Fix) Fixed an issue that Fan stops working even when temperature reaching trigger point.

...

<1.08>
- (New) Removed authentication of WLAN cards.


So if you want to be able to replace the Wifi card with a MacOS compatible card, Bios version 1.08 or above is mandatory.

--> Version 1.20 or later recommended.

Due to so many horror stories and laptop bricked by BIOS update done from within Windows, I highly recommand to update BIOS only from bootable media (CD or USB thumb). Here you can find ISO file to download: https://support.lenovo.com/fr/fr/downloads/ds102287

*Note: my installation guide will be based on Bios version 1.20.*

---------------------

### 2. BIOS settings

- Disable Computrace
- Disable Vtd / Vtx
- Enable CSM
- Enable US3

### 3. Know your hardware
