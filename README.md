# MacOS on Thinkpad Yoga 12
Hackintosh MacOS Mojave on Thinkpad Yoga 12 with ACPI hotpatches

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
If you get error message "secure flash athentication failed" while updating the BIOS, you should disable first "rollback prevention " in BIOS settings.

**Note: my installation guide will be based on Bios version 1.20.**


### 2. BIOS settings

- Disable Computrace
- Disable Vtd / Vtx
- Enable CSM
- Enable USB3

### 3. Know your hardware

|Compenent|Reference|
|---|---|
|CPU|Intel Core i-5 5200U|
|RAM|8GB Ram|
|GFX|Intel HD Graphics 5500|
|Sound|Intel HD Audio (Conexant CX22752 Audio Driver)|
|Display|12.5" FHD IPS LCD (VendorID: 30e4 - ProductID: 437) [LG Display LP125WF2-SPB2](http://www.panelook.com/LP125WF2-SPB2_LG%20Display_12.5_LCM_overview_23372.html)|
|Wifi|Intel Wifi -> Should be replaced by a Broadcom compatible WLAN card|
|Touch panel|Synaptics Touch Digitizer V04 (USB Internal port)|
|Stylus digitizer|FHD 1920x1080 (12.5 英寸)|
|Sensor Hub|ST Sensor Hub: gyroscope, Accelerometer, rotation detection, luminosity detection..|
|SD Card reader|Realtek RTS5227 PCI Express Card Reader|
|Camera|CON4B2E72 Integrated Camera (USB Internal port)|
|Extension Port|Onelink Dock port for extension module [Onelink Pro Dock](https://support.lenovo.com/us/en/solutions/pd029981)|


#### What will work:
CPU, Graphic Card, Power Management, Battery, Sleep, Camera, Screen (FHD), brightness, HiDPI, Keyboard, Trackpad and Trackpoint, Sound, USB ports, HDMI port, HDMI Audio-Out, Onelink Pro Dock (USB2/USB3, DisplayPort, DVI, Ethernet).

#### What will not work:
- Touchscreen
- ST Sensor Hub: gyroscope, Accelerometer, rotation detection, luminosity detection..

#### What will partially work:
- Tablet digitzer (with stylus): recognized as a mouse with button
- SD Card reader: a driver is under development, but not yet stable enough

#### WIFI :
Default Intel Wifi M.2 card is replaced by a Broadcom BCM94352Z card.
Thinkpad Yoga 12 M.2 wifi port is E keyed, so it's compatible with both A+E and E keys cards. So you can either use in it:

- Dell DW1560 (BCM94352Z) which is M.2 **"A+E" keyed**.
- Lenovo BCM94352Z (**FRU 04X6020**) which is M.2 **"E" keyed**.

So if you find a good price opportunity on Lenovo's BCM94352Z card, you can go for it on Yoga 12 ;) *This is not the case for every laptops*

##### WIFI alternative :
While waiting for the internal wifi replacement card to arrive, I've used a USB wifi key TP-Link TL-WN725N. Mine is TL-WN725N **V2** (and V3 does also work). It works pretty well, while it's limited to Wifi-N (one band) at 150Mbps and occupy one of the two USB port available on this laptop. Note: for V2 of this adapter TP-Link recommnends the specific V2 of its driver. But the V2 driver is unstable as hell, use instead V3 driver even for a V2 adapter (it works pretty well). Driver URL: https://www.tp-link.com/us/download/TL-WN725N_V3.html



## Installation

....

## Post-Installation

### 1. Tools needed

- Clover Installer
- Clover configurator
- MacIasl
- iasl

...

### 2. Kexts used

- ACPIBatteryManager.kext
- AppleBacklightFixup.kext
- HibernationFixup.kext
- IntelMausiEthernet.kext
- Lilu.kext
- SMCBatteryManager.kext
- SMCProcessor.kext
- USBInjectAll.kext
- VirtualSMC.kext
- VoodooHDA.kext
- VoodooPS2Controller.kext
- WhateverGreen.kext
- Sinetek-rtsx kext for PCI SD-Card reader. Sources: https://github.com/syscl/Sinetek-rtsx (fork of https://github.com/sinetek/Sinetek-rtsx ). I've used the build from this repository: https://github.com/Maxcj/Hasee-OS-X
It works somehow... (note I've taken the release 2018.11.05

### 2. Hotpaches

It consitsts of a combinaison of ACPI replacements done in Clover plist along with additional custom SDST files.

#### 1. USB power management, port limiting and fix instant wake-up

Adapted from this guide: https://www.tonymacx86.com/threads/guide-usb-power-property-injection-for-sierra-and-later.222266/

##### 1. Fix Embedded Controller reference

In config.plist rename EC0 to EC, in order AppleBusPowerController will get a proper access to embedded controller:

```
<dict>
	<key>Comment</key>
	<string>change EC0 to EC</string>
	<key>Disabled</key>
	<false/>
	<key>Find</key>
	<data>RUMwXw==</data>
	<key>Replace</key>
	<data>RUNfXw==</data>
</dict>
```
##### 2. Inject port limits and kUSB power management

USB controller is 8086_9cb1 with following ports:

- HS01: Left port, external (USB2)
- HS02: Right port, external (USB2)
- HS03: Onelink Pro Dock (USB2)
- HS04: Bluetooth, internal (USB2)
- HS05: Synaptic Touch Digitizer, internal (USB2)
- HS06: Integrated camera, internal (USB2)
- HS07: Not used
- HS08: ISD-V4 (tablet), internal (USB2)
- HS09: Not used
- HS10: Not used
- HS11: Not used
- SSP1: Left port, external (USB3)
- SSP2: Right port, external (USB3)
- SSP3: Onelink Pro Dock (USB3)
- SSP4: Not used
- SSP5: Not used

A SSDT hotpatch is used to:

- to enable only those used HS01,HS02,HS03,HS04,HS05,HS06,HS08,SSP1,SSP2,SSP3 with appropriate port values (external/internal). *Note: if you don't plan to use OneLink Dock, you can also disable HS03 and SSP3*
- to inject proper value for AppleBusPowerController
```
"kUSBSleepPortCurrentLimit", 2100,
"kUSBSleepPowerSupply", 2600,
"kUSBWakePortCurrentLimit", 2100,
"kUSBWakePowerSupply", 3200,
```
- to inject proper AAPL values for \\_SB.PCI0.XHC
```
"AAPL,current-available", Buffer() { 0x34, 0x08, 0, 0 },
"AAPL,current-extra", Buffer() { 0x98, 0x08, 0, 0, },
"AAPL,current-extra-in-sleep", Buffer() { 0x40, 0x06, 0, 0, },
"AAPL,device-internal", 0x02,
"AAPL,max-port-current-in-sleep", Buffer() { 0x34, 0x08, 0, 0 },
```


##### 3. Fix Power Call
```
<dict>
	<key>Comment</key>
	<string>change Method(GPRW,2,N) to XPRW, pair with SSDT-GPRW.aml</string>
	<key>Disabled</key>
	<false/>
	<key>Find</key>
	<data>
	FEUIR1BSVwI=
	</data>
	<key>Replace</key>
	<data>
	FEUIWFBSVwI=
	</data>
</dict>
```
and compile [SSDT-GPRW.dsl](https://github.com/RehabMan/OS-X-Clover-Laptop-Config/blob/master/hotpatch/SSDT-GPRW.dsl) to SSDT-GPRW.aml with MaciASL and add it to /Clover/ACPI/Patched/

#### 2. Fix LED blinking after waking up from sleep

In config.plist, replace method "WAKH"
14 4E 41 57 41 4B 48 09

by method "WAKO"
14 4E 41 57 41 4B 4F 09

```
<dict>
      <key>Comment</key>
      <string>change Method(WAKH to WAKO</string>
      <key>Disabled</key>
      <false/>
      <key>Find</key>
      <data>
      FE5BV0FLSAk=
      </data>
      <key>Replace</key>
      <data>
      FE5BV0FLTwk=
      </data>
</dict>
```
Then add and call an additional SSDT-LED.aml file that create a new method WAKH which calls the LED ON status \\_SI._SST (0x00) on wake up

#### 3. Brightness Hotkeys

```
<dict>
	<key>Comment</key>
	<string>change Method(_Q14,0,S) to XQ14</string>
	<key>Disabled</key>
	<false/>
	<key>Find</key>
	<data>
	X1ExNA==
	</data>
	<key>Replace</key>
	<data>
	WFExNA==
	</data>
</dict>
<dict>
	<key>Comment</key>
	<string>change Method(_Q15,0,S) to XQ15</string>
	<key>Disabled</key>
	<false/>
	<key>Find</key>
	<data>
	X1ExNQ==
	</data>
	<key>Replace</key>
	<data>
	WFExNQ==
	</data>
</dict>
```


### 2. HiDPI

Reference guide: https://www.tonymacx86.com/threads/adding-using-hidpi-custom-resolutions.133254/

#### 1. Enable HiDPI

```
sudo defaults write /Library/Preferences/com.apple.windowserver DisplayResolutionEnabled -bool YES
sudo defaults delete /Library/Preferences/com.apple.windowserver DisplayResolutionDisabled
```
#### 2. Generate a display override plist

I don't have eagle eyes anymore to read confortably at 1920x1080 pixels on a 12.5" screen... So I tried to use scaled resolutions, but vthen erything is blurry. That's why I've used HiDPI feature to get crisp and clear scaled resolutions.

My custom override folder is set for 1920x1080 native resolution + 1600x900 scaled HiDPI + 1366x768 scaled HiDPI.

You can either copy my custom override folder [DisplayVendorID-30e4](https://github.com/gartempe/MacOS-Thinkpad-Yoga-12/tree/master/HiDPI) (copy the whole "DisplayVendorID-30e4" folder) in /System/Library/Displays/Contents/Resources/Overrides/ or generate your own at https://comsysto.github.io/Display-Override-PropertyList-File-Parser-and-Generator-with-HiDPI-Support-For-Scaled-Resolutions/ (also read the doc at the bottom of the page).

If you want to generate your own resolutions override, the LCD panel references in Yoga 12 are :
- VendorID: 30e4
- ProductID: 437

Or this automated script: https://github.com/xzhih/one-key-hidpi

#### 3. Use RDM to switch resolution

https://github.com/avibrazil/RDM

### 2. SMBIOS



### 2. CPU Low Frequency Mode

By default in Macbook Air 7.1 and 7.2 CPU lowest CPU frequency is 1300Mhz, while the i5-5200U can go lower to 800Mhz on idle or basic load. This means, as-is, your CPU will consume too much power (and so drain battery) when on low frequency.
To fix that, you can patch the frequency vectors.

> Notice: The LFM frequency in Mac-9F18E312C5C2BF0B.plist is set to 1300 MHz!
	This restriction can be lifted with help of freqVectorsEdit.sh from:
	https://github.com/Piker-Alpha/freqVectorsEdit.sh

-> to check: https://github.com/nguyenlc1993/mac-os-k501l/wiki/Postinstall-optimization

Actually, I included 2 dummy kexts in the installation pack namely AppleGraphicsPowerManagement_K501LX.kext and X86PlatformPlugin_K501LX.kext, which contain the modified power profiles. You just have to install these two kexts to get the effect, thus no need to modify the original kexts.



Keyboard special keys

For reference: Apple Key Codes https://eastmanreference.com/complete-list-of-applescript-key-codes

Done with https://github.com/RehabMan/OS-X-ACPI-Debug

Mute (f1):
ApplePS2Keyboard: sending key e020=4a down
ACPI: EC _Q43

-----------
- Volume - *(on F2)*: ApplePS2Keyboard: sending key e02e=49 down
- Volume + *(on F3)*: ApplePS2Keyboard: sending key e030=48 down
- Mic Mute *(on F4)*: ACPI EC _Q6A
- Brightness Down *(on F5)*: ACPI EC _Q15
- Brightness Up *(on F6)*: ACPI EC _Q14
- Miror *(on f7)* : ApplePS2Keyboard sending key e05b=3a down and after ApplePS2Keyboard sending key 19=23 down = mapped by default to ⌥P
- WIFI *(on F8)*: ACPI EC _Q64
- Settings *(on F9)*: ACPI EC _Q5A
- Search *(on F10)*: ACPI EC _Q5B
- Menu *(on F11)*: ACPI EC _Q5C
- Apps *(on F12)*: ACPI EC _Q5D
- Lock screen rotation (on side): This button also send a two signals at the same time : PS2 scan code e017 (F15) / ACPI EC _Q36

-> disabling _Q36 method, and mapping e017=64 (F13) to avoid duplicate button for Brightness Up

-------

power plug on: ACPIDebug: "EC _Q26 enter"
power plug off: ACPIDebug: "EC _Q27 enter"

---

Win key : ?

---------

fn+Space (keyboard light):
ACPI: EC _Q1F

-------------
### Sound

Sound is reported to be Conexant CX22752 (aka Conexant CX20752) from [Lenovo Win8 driver page](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-yoga-series-laptops/thinkpad-yoga-12/downloads/ds102058) or Conexant CX22751 (aka Conexant CX20751) from Lenovo Win10 driver page.

Hopefully Conexant CX20751/20752 is supported by [AppleALC kext](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs). And the sound device in DSDT is already HDEF without patching anything. The layout-id to use for 20751/20752 is either 3 or 28.

So it is just needed to install AppleALC.kext in /Library/Extensions/ , along with Lilu.
And configure in config.plist:

- ACPI/DSDT/Fixes/FixHPET -> Yes
- Devices/Audio/Inject -> 28

*No need for VoodooHDA or CodecCommander.*

#### HDMI SOUND OUTPUT

OK, I get HDMI Audio-Out working, but no control over the volume on that output ?

> It's not a bug, it's a feature !

This is how Apple/MacOS think it should be : no control on the volume for this kind of audio output, sound is sent at full volume and you should control the volume directly on your monitor...

There are external tools to enable sound volume control (from the system) for HDMI output. What they do basically it to create an additional virtual audio device (with controlable volume) and you send system audio to it, then it routes the sound the desired output. It acts like a proxy with audio routing.

- https://github.com/mattingalls/Soundflower (free) [tutorial](https://www.mattmontag.com/music/enable-mac-volume-control-for-hdmi-and-displayport-audio-devices) / [Lifehacker post](https://lifehacker.com/how-to-enable-mac-volume-control-for-hdmi-or-displaypor-1793607216)
- https://staticz.com/soundcontrol/ (not free: 10$ License)

The caveat with these solutions above: expect some sound distortion and/or some delay, CPU usage and memory footprint increase, some issues with audio output/control after wake.... So it's up to you.

-----------

### ICC profile

LCD panel is LG Display LP125WF2-SPB2

By default with ICC profile generated automatically by Apple, the display is a bit yellowish

Here is an ICC profile that gets more accurate colors: https://github.com/soleblaze/icc/raw/master/lp125wf2-spb2.icc
Found here: https://github.com/soleblaze/icc

Another profile found here (but more redish): redish https://www.thinkscopes.com/2014/08/24/color-profiles-for-current-thinkpads/


-----------

### Subpixel antialiasing

On macOS Mojave nukes subpixel antialiasing, making your non retina monitor appear blurry.

While this is considered as a "refinement" on Apple retina displays, it is clearly a downgrade regarding font rendering on FHD panel.

-> Solution: open the terminal

1. Re-enable subpixel rendering
```
defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO
```

2. Fine tune font smoothing

To read the actual setting:
```
defaults read NSGlobalDomain AppleFontSmoothing
```

Then you can set it to 1, 2, 3 or 4. Test the setting that suits you the best (I've used 3 on mine).
```
defaults write NSGlobalDomain AppleFontSmoothing -int 3
```
You'll need to either reboot your laptop or close the session an log on again, in order to see any change.

Sources: [tonymacx86 post](https://www.tonymacx86.com/threads/re-enable-subpixel-rendering.261087/) and [reddit post](https://www.reddit.com/r/apple/comments/8wpk18/macos_mojave_nukes_subpixel_antialiasing_making/)

-----------

Update to 10.14.1

after update, when all set and done, restarted with a fatal osxaptio error.

Had to update my old OsxAptioFixDrv-64.efi to OsxAptioFix3Drv-64.efi.

------------

Unknown PCI devices:

- pci8086,9ca4 : Intel PCH Thermal Reporting
- pci8086,1604 : Intel Broadwell-U Host Bridge -OPI

---------------
Stylus (Tablet). Recognized as Mouse.

In IORegistryExplorer:

- VendorID: 0x56a
- ProductId: 0xec
- USB Product Name: ISD-V4
- USB Vendor Name : Tablet

In System Information:

- Identifiant du fournisseur :	0x056a  (WACOM Co., Ltd.)
- Fabricant :	Tablet

---------------

Note about the ThinkPad OneLink Pro Dock https://support.lenovo.com/us/en/solutions/pd029981

![OneLink Pro Dock](https://raw.githubusercontent.com/gartempe/MacOS-Thinkpad-Yoga-12/master/images/onelinkprodock.jpg)

If you plan to use your Thinkpad Yoga 12 regularly on a desk, this dock is very convenient. If you get the opportunity to buy one for cheap *(I bought mine for 25 euros on Ebay)*, go for it ;)

It adds the follwoing features totally working with MacOS Mojave: main power replicator, Ethernet Gigabit, additional sound output, powered USB Hub with 2 USB2 ports and 4 USB3 ports, DisplayPort, DVI port. And everything is hotplug.

*Note: I've tested everything except DVI port, but as FB Patcher reports 4 framebuffers including one disabled (set as dummy), I'm pretty sure it's possible to enable the framebuffer for DVI port.*


Sleep:

https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.175801/

```
sudo pmset -a hibernatemode 0
sudo rm /var/vm/sleepimage
sudo mkdir /var/vm/sleepimage
```

```
sudo pmset -a standby 0
sudo pmset -a autopoweroff 0
```
