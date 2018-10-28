# MacOS-Thinkpad-Yoga-12
Hackintosh MacOS Mojave on Thinkpad Yoga 12 with ACPI hotpatches

------------------
## Pre-Installation

### Warning - Achtung - Attention : check you BIOS version !!!

There is a know bug in embeded controller, where **CPU fan stop triggering**. This has been fixed with Bios version 1.20.

*I've experimented this bug (with BIOS 1.03 JEET47WW), and the CPU rises up quickly to 99°C at full load (even with short load). This is extremly dangerous for the health and lifespan of your laptop.*

**So you should update BIOS to version 1.20 at minimum !**

<1.20>
- (New) Updated Fan table
- (Fix) Fixed an issue that Fan stops working even when temperature reaching trigger point.

Note about BIOS: 
<1.08>
- (New) Removed authentication of WLAN cards.

Source: Summary of Changes at https://download.lenovo.com/pccbbs/mobiles/jeuj84ww.txt

So if you want to be able to replace the Wifi card with a MacOS compatible card, Bios version 1.08 or above is mandatory.

---------------------
