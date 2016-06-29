# T430-Hackintosh
####Fully functional Lenovo T430 Hackintosh with discrete NVS5400M graphics (1600x900) and running El Capitan 10.11.5. 

<sup>By fully functional, I mean other than onboard WiFi, VGA, card reader, and brightness control, but who's counting.</sup>

Based on [kartoffelsalat's El Capitan guide](http://www.tonymacx86.com/threads/guide-lenovo-t430-el-capitan.175935/), however with modifications to provide NVS5400M support.

My aim with this guide is to leave nothing to the imagination.  I feel like there's a certain subset of the internet such as DD-WRT and Hackintoshing where 90% of the information is cryptic bullshit.  This guide should be trivial to follow if you have basic experience installing operating systems and using terminal.

![desktop screenshot](http://i.imgur.com/DLfnx5D.jpg)

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

- Copy [Clover installer](https://sourceforge.net/projects/cloverefiboot/), [Kext Wizard](http://mac.softpedia.com/get/Utilities/Kext-Wizard.shtml), and any needed drivers to root of USB drive for post-installation. USB WiFi is a good start; I am using [this](https://www.amazon.com/Edimax-EW-7811Un-150Mbps-Raspberry-Supports/dp/B003MTTJOY) as it fits very nicely into the recessed USB3.0 slots on the left of the computer.

- Copy kexts included in this repo to root of USB drive
  
- Copy any needed drivers

####BIOS configuration:

- Boot to BIOS, `[F1]`

- Set `Config > Display > Graphics Device` to `Discrete Graphics` and disable `OS Detection`.  Switchable graphics are not supported with Hackintosh.  The special sauce in this particular machine, and perhaps the only way discrete graphics will work on any modern laptop, is that there is a hard setting in the BIOS to override Optimus.

- Ensure `Startup > Boot > Boot Mode` is set to `Both`, `UEFI First` and `CSM Enabled`

- Enable `Security > Memory Protection`

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

- Copy `org.rehabman.voodoo.driver.Daemon.plist` to `/Library/LaunchDaemons`

- Copy `VoodooPS2Daemon` to `/usr/bin`

- Install USB WiFi driver

- Repair permissions and update cache: `sudo touch /System/Library/Extensions && sudo kextcache -u /`

- Reboot

####Set up shortcuts (adds three-finger-swipe):

macOS unsurprisingly has *very Unix* shortcut key configuration that makes you wonder why something like this is so hard for Microsoft to figure out.

It's legitimately as simple as opening a `System Preferences > Keyboard` dialogue, and manually typing in **ANY** menu bar command you want executed when you type a certain key combination in an application or globally.  The fact that Automator can add its scripts to your menubar under "Services" means you can effectively bind a key combination to do literally anything.

VoodooPS2 also binds three-finger-swipe to `ctrl-cmd-[DIRECTION]`, so we can bind it to back and forward (and whatever you want up and down to do) in our web browser.

Here's a cheatsheet (keep in mind that I already created the "launch terminal" Service in Automator):

![keyboard-shortcuts-menu](http://i.imgur.com/spnR3wt.png)

---

You now have a fully functional Thinkpad T430 Hackintosh to install Google web apps and Microsoft text editors on.  

The ideal scenario for me is having a Unix or Unix-like operating system on my laptop that still has an attractive, user-friendly desktop environment.  macOS fits the bill.  Dual booting Windows offers unlimited versatility, even when I'm not at home with my main Windows computer.

###Quirks:

- The iMac SMBIOS configuration removes "on battery" Energy Saver preferences.  This is easily resolved with the [`pmset`](http://ss64.com/osx/pmset.html) command line utility.  `sudo pmset -g disk` to view all current Energy Saver configuration.  `sudo pmset -b displaysleep 15` is what I needed, which sets the display sleep timeout to 15 minutes on battery [`-b`]. 

- You will have a 3rd party WiFi manager in your menu bar to configure your USB WiFi.

- If you want to use the trackpoint, you have to set the cursor sensitivity to minimum which effectively renders your touchpad useless.  You can press `PrtSc` to disable the touchpad.

- To be continued...
