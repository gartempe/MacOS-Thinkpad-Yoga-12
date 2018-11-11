This is suited for Core i5-5200U CPU, with Intel HD 5500 GPU on MacBookAir7,2

https://everymac.com/systems/apple/macbook-air/specs/macbook-air-core-i5-1.6-13-early-2015-specs.html


*Note: [Core i5-5200U]{https://ark.intel.com/products/85212/Intel-Core-i5-5200U-Processor-3M-Cache-up-to-2-70-GHz-} has Base Frequency 2.2GHz / Turbo Frequency 2.5Ghz / Max Turbo Frequency 2.7Ghz*


By default the Low Frequency Modes on MacBookAir7,2 would be:

- CPU : 1.3Ghz
- GPU : 350 Mhz

Place these kexts (.kext) in /Library/Extensions/ folder *(not in /S/L/E/ !)* in order to override...

This will add:

- 0.8Ghz frenquency vector for CPU LFM
- proper top frequencies (max base frequency: 2.2Ghz / 1st level Turbo Frequency 2.5Ghz / 2nd level Turbo Frenquency 2.7Ghz)
- 300Mhz GT2Floor/GT3Floor for GPU

*Note: Core i5-5200U has Base Frequency 2.2GHz


I suggest you to backup /System/Library/Extension/

How to check it is working properly ?

CPU

Frequency vector is used:

```
sysctl -n machdep.xcpm.vectors_loaded_count
1
```
Max hard limit is 2700Mhz (27x100):

```
sysctl -n machdep.xcpm.hard_plimit_max_100mhz_ratio
27
```


GPU

Check with command in terminal: ioreg -rlw0 -p IOService -c AGPMController -k Machine | grep Machine

That you get :

- "GT2Floor"=6
- "GT3Floor"=6

```
      "Machine" = {"MacBookAir7,2"={"IGPU"={"BoostPState"=(24,24,24,24),"BoostTime"=(1,1,1,15),"SliceControl"=1,"control-id"=16,"Heuristic"={"ThresholdsForRingOverrideTable2"=(10,100),"ThresholdsForRingOverrideTable1"=(5,15),"ThresholdsForRingOverrideTable0"=(0,10),"EvaluateUpInterval"=31250,"DownStep"=1,"GT3Floor"=6,"StartingPstateForRingTableOverride"=11,"NumOfRingTableOverride"=23,"RingOverrideTable2"=(8,8,8,9,10,11,13,14,15,16,18,19,20,21,23,24,25,26,28,29,30,31,33),"IOBusynessSamplingInterval"=1,"EnableOverride"=1,"UpStep"=2,"RingOverrideTable1"=(8,8,8,9,10,11,13,14,15,16,16,16,16,16,16,16,16,16,16,16,16,16,16),"BusyUpThresholdPercent"=70,"GT2Floor"=6,"NumOfThresholdsForRingTables"=2,"BusyDownThresholdPercent"=50,"ID"=2,"RingOverrideTable0"=(8,8,8,8,8,9,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10),"NumOfRingTables"=3,"sampleInterval"=1000,"EvaluateDownInterval"=31250,"EnableRingTableOverride"=1}}}}
 ```
