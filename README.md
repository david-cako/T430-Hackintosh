# T430-Hackintosh
Fully functional Lenovo T430 Hackintosh using discrete NVS5400M graphics (1600x900) and running El Capitan 10.11.5.

Based on [kartoffelsalat's El Capitan guide](http://www.tonymacx86.com/threads/guide-lenovo-t430-el-capitan.175935/), however with modifications to provide NVS5400M support.

<sup>By fully functional, I mean other than onboard WiFi, VGA, and the trackpoint, but who's counting.</sup>

![desktop screenshot](http://i.imgur.com/W3EPbnW.jpg)

###Main headaches:

- It was not possible to boot whatsoever on `MacbookPro9,2` SMBIOS with discrete graphics. `iMac13,1` (iMac, Late 2012) SMBIOS is necessary.  It was chosen due to the GT640M being standard on that generation, the nearest relative I could find to the NVS5400M (effectively a rebadged GT630M) on stock Apple hardware.

- ####There are two different AppleHDA kexts in kartoffelsalat's post.  One does not work on 10.11.5.

###Install procedures:

####USB drive prep:
  
- Go through the [standard UniBeast process](http://www.tonymacx86.com/threads/unibeast-install-os-x-el-capitan-on-any-supported-intel-based-pc.172672/) to create a bootable OSX installer.

- Replace `/EFI/CLOVER/config.plist` with the one included in this repo.

- Add `DSDT.aml` to `/EFI/CLOVER/ACPI/patched`

- Replace `/EFI/CLOVER/drivers64UEFI/VBoxHfs-64.efi` with `HFSPlus.efi` (HFSPlus is Apple's proprietary filesystem driver. It will not be supplied in this repo.)

- Move kexts from `/EFI/CLOVER/kext/10.11` to `/EFI/CLOVER/kext/other`

- Delete all folders in `/EFI/CLOVER/kext/` except `other`

- Copy Clover installer, Kext Wizard, and any needed drivers (USB WiFi is a good start) to root of USB drive for post-installation

- Copy kexts included in this repo to root of USB drive
  
- Copy any needed drivers

####BIOS configuration:

- Boot to BIOS, `[F1]`

- Ensure `Startup > Boot > Boot Mode` is set to `Both`, `UEFI First` and `CSM Enabled`

- Enable `Security > Memory Protection`

- Disable `Security > Virtualization`

####Mac OS installation:

- Boot to startup menu, `[F12]`, and choose your USB drive

- Choose Mac OS partition in Clover

- Open `Disk Utility` from menubar, formatting target drive to `Mac OS Journaled`.

- Install Mac OS

####Post installation:

- Boot from USB once more, however this time choosing your hard drive installation in Clover

- Install Clover on boot drive
  - Install for UEFI Motherboards
  - Install in ESP
  - Check `Drivers64UEFI > OsxAptioFix2Drv-64`

- Mount EFI partition
  - `mkdir /Volumes/efi`
  - `diskutil list`, taking note of the EFI partition on the Mac OS hard drive (not the USB drive)
  - `sudo mount -t msdos `**`/dev/[EFI partition]`**` /Volumes/efi`

- Copy `config.plist` to `/EFI/CLOVER`

- Copy `DSDT.aml` to `/EFI/CLOVER/ACPI/patched`

- Replace `/EFI/CLOVER/drivers64UEFI/VBoxHfs-64.efi` with `HFSPlus.efi`

- Use Kext Wizard to install kexts included in this repo

- Repair permissions and update cache: `sudo touch /System/Library/Extensions && sudo kextcache -u /`

- Reboot

You now have a fully functional Thinkpad T430 Hackintosh.
