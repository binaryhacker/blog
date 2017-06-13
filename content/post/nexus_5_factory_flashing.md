+++
date = "2017-06-13T08:11:34+05:30"
title = "Flashing Nexus 5 Factory Images"

+++


Recentry while trying to flash the android aosp hammerhead build to my `Nexus 5` device ,my device went into a bootloop . The device just hanged infinatly in the bootanimation . To recover the device to working state , I had to flash the factory binaries to the device . So i did a quick google search and I came to know that google is maintaining all the nexus device factory images in this url https://developers.google.com/android/images . I just downloaded the latest binaries from the website and followed through the instructions given in the link . I was able to rescue my device from bootloop .


The Downloaded package had the following files inside . 

```
tony@terra-> tree
.
|-- bootloader-hammerhead-hhz20h.img
|-- flash-all.bat
|-- flash-all.sh
|-- flash-base.sh
|-- image-hammerhead-m4b30z.zip
`-- radio-hammerhead-m8974a-2.0.50.2.30.img

0 directories, 6 files
```

1. bootloader-hammerhead-hhz20h.img (Bootloader image)
2. flash-all.bat (windows flash script)
3. flash-all.sh  (linux flash script)
4. flash-base.sh
5. image-hammerhead-m4b30z.zip (boot.img , system.img , recovery.img , userdata.img)
6. radio-hammerhead-m8974a-2.0.50.2.30.img 


The steps for flashing the images are mentioned below .

## STEP 1 (Reboot nexus 5 to fastboot mode and unlock the bootloader for flashing)

````bash
adb reboot bootloader
adb oem unlock
````

## STEP 2 (flash the bootloader image and reboot to bootloader)

````bash
fastboot flash bootloader bootloader-hammerhead-hhz20h.img
fastboot reboot-bootloader
````

## STEP 3 (flash the radio image and reboot)
````bash
fastboot flash radio radio-hammerhead-m8974a-2.0.50.2.30.img
fastboot reboot-bootloader
sleep 5
````

## STEP 4 Flash android binaries

````bash
fastboot -w update image-hammerhead-m4b30z.zip
````

