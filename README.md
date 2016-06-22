# T430-Hackintosh
Fully functional T430 Hackintosh using discrete NVS5400M graphics and running El Capitan 10.11.5.

Based on [kartoffelsalat's El Capitan guide](http://www.tonymacx86.com/threads/guide-lenovo-t430-el-capitan.175935/), however
with modifications to provide NVS5400M support.

###Main headaches:

- It was not possible to boot whatsoever on `MacbookPro9,2` SMBIOS with discrete graphics. `iMac13,1` (iMac, Late 2012)
SMBIOS is necessary.  It was chosen due to the GT640M being standard on that generation, the nearest relative I could find 
to the NVS5400M (effectively a rebadged GT630M) on stock Apple hardware.

- ####There are two different AppleHDA kexts in kartoffelsalat's post.  One does not work on 10.11.5.

###Install procedures:
