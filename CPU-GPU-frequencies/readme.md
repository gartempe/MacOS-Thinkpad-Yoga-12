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
