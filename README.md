**Sleep will not work with High Sierra. If this is important for you, I suggest using Sierra. Their respective CLOVER folders are in this repo.**

**Note that the High Sierra config is literally full of crap, not sure what's safe to be removed as I haven't created it from the ground but rather forked this and fixed the things that I needed (sleep and USB3).**

**As @[Tomygames](https://github.com/Tomygames) suggested at [here](https://github.com/NTT123/Hackintosh-HP-Z420-MacOS-High-Sierra-10.13/issues/5#issuecomment-368083010). You need to "[a]ctivate Legacy ACPI CPU Tables in BIOS".**

**Some detailed instructions to install macOS provided by @spellbound can be found [here](https://github.com/NTT123/Hackintosh-HP-Z420-MacOS-High-Sierra-10.13/issues/5#issuecomment-399620392).** 

How to install MacOS Sierra 10.12.6 on HP Z420 Workstation.

Overall, we will use Unibeast to create a USB Installer.

From another computer running macOS, download the macOS Sierra Installer (go to App Store to download).

Then, we use the recent Unibeast (7.x.x for Sierra and 8.x.x for High Sierra) to create a bootable USB. However, we **have to** use kexts from **CustoMac Essential** package and put these kexts to /Volumes/EFI/EFI/CLOVER/kexts/Other/ directory (mount your EFI partition first!)

### Here the Tricks

Note: I put my `/Volumes/EFI/EFI/CLOVER` directory and all kexts we need in the repo.

- Install the lastest CLOVER (e.g., v2k4)

- We have to use flag `npci=0x2000` all the time. (I also use `dart=1`).

- Z420 crashes randomly when booting, we can fix that with flag `cpus=1`. However, this makes our PC slow (only 1 CPU!!), therefore, we fix this problem by installing `VoodooTSCSync.kext` at `/System/Library/Extensions`. Remember to fix permission. E.g.
`chmod -R 755 /System/Library/Extensions/* && chown -R root:wheel /System/Library/Extensions/* && kextcache -system-caches.`

- We got problem with USB ports (therefore, cannot install from USB). We fix it by:
  + First, Use [Clover Configurator](https://mackie100projects.altervista.org/download-clover-configurator/) to mount EFI and patch ACPI (Look at "List of Patches" in Acpi menu and apply All Of Them).
  + Install `USBInjectAll.kext` to inject all USB ports!

- HP Z420 crashes with many versions of FakeSMC and NullCPUPowerManagement.kext, so please **use those** in the repo!!!

- At first, when we don't have Nvidia WEB drivers, we need to disable NVidia kext by flag `nv_disable=1`. We later can install lastest Nvidia Web driver for 10.12.3
(https://images.nvidia.com/mac/pkg/367/WebDriver-367.15.10.35f01.pkg), and we will not need that flag anymore.

- Use SMBIOS of MacPro5,1 or MacPro6,1. If using the latter, use this DSDT patch to prevent black screen:
```
<dict>
	<key>Comment</key>
	<string>change SLT2 to GFX1 (avoid blackout when using MacPro6,1 SMBIOS)</string>
	<key>Disabled</key>
	<false/>
	<key>Find</key>
	<data>
	U0xUMg==
	</data>
	<key>Replace</key>
	<data>
	R0ZYMQ==
	</data>
</dict>
```
It is included in the CLOVER folder. Generate the SMBIOS with Clover Configurator.

- Sleep does not work with High Sierra. Read [here](https://www.tonymacx86.com/threads/hp-z420-first-attempt.211479/page-3). If this is important for you, use Sierra.

- The generated SSDT (for the Intel Xeon E5-1620) and the patched DSDT might not work for you. Generate SSDT with ssdtPRGen.sh. Generate DSDT with Clover (F4) and patch it with MacIASL. See link above.

- If using High Sierra, the installed drive will automatically be formatted as APFS, Apple's new filesystem protocol. In order to make this show up in Clover, the `HFSPlus.efi` must be in the `/EFI/CLOVER/drivers64UEFI`. It is included in the 10.13.1 folder.

- USB 3 is working with the `GenericUSBXHCI` kext for both Sierra and High Sierra. It is included in both Clover folders in this repo.