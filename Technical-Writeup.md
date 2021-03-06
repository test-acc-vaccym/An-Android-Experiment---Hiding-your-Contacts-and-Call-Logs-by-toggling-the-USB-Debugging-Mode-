
![alt text](https://www.android.com/static/2016/img/logo-android-green_1x.png "Android-Text-Logo")

# Android-Experiments
# (Hiding your Contacts and Call Logs by toggling the USB Debugging Mode) Technical Writeup

# Inventory needed
#### My recommendations are based on the version that I've used to build the experiment.
# ==== ==== ==== ==== ==== ==== ==== ==== ====
[1. Ubuntu Operating System {Recommended Version : 15.10} ](https://www.ubuntu.com/) 
#
[2. OpenSSL for Android {Recommended Version : 1.0.2g} ](https://wiki.openssl.org/index.php/Android)
# 
[3. Android NDK ](http://developer.android.com/ndk/downloads/index.html) 
# 
[4. The Android factory image “hammerhead” for Nexus 5 (GSM/LTE) for MMB29Q](https://dl.google.com/dl/android/aosp/hammerhead-mmb29qfactory-6356d31e.tgz)
#
[5. Files for reference in case you need help for the below section ](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/tree/master/Technical-Writeup-Files) 
# ==== ==== ==== ==== ==== ==== ==== ==== ====

# Here is a high-level view of what you were be doing to achieve this experiment.
# (The image below shows the insides of the boot.img found in your Android Image)
![](Images/Modified-Boot-Image-Diagram.png?raw=true)
# ==== ==== ==== ==== ==== ==== ==== ==== ====
## Let's begin our Journey shall we?

# Part 1- Extracting the boot image from the factory image
![](Images/Journey1.png?raw=true)

#### Once you are done with the Part 1 Journey, here's a simple explanation of what has happened. You have basically unpacked an Android factory image and extracted out the contents in the boot.img file. You should be able to see this 3 files : bootimg.cfg, zImage and initrd.img. 
* The bootimg.cfg file contains the bootsize, pagesize, kerneladdr, ramdiskaddr and other information about the boot image, it is used during the packing of boot image as shown as the green section in the image above.
* The zImage file is a compressed version of the Linux kernel image that is self-extracting as shown in the blue section in the image above.
* The initrd.img file stands for initial ramdisk which is a scheme for loading a temporary root file system into memory, which may be used as part of the Linux startup process as shown in the red section in the image above.

## Let's continue our Journey 

# Part 2- Creating a kernel for Android 6.0.1 Build: MMQ29Q and set SELINUX to permissive mode
* The selinuxfs.c file can be reference from [here](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/tree/master/Technical-Writeup-Files/selinuxfs.c)
![](Images/Journey2.png?raw=true)

# Part 3- Extracting the Initial RAM File System 
![](Images/Journey3.png?raw=true)

# Part 4- Enabling Encryption and Decryption to take place in the system. ( The reason is because in the event whereby it is possible for a user to grab hold of all your data. The hidden data would be encrypted. ) [The encryption and decryption file used](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/blob/master/Technical-Writeup-Files/Enc-Dec.c) 
![](Images/Journey4.png?raw=true)

# Part 5- Creating Storage space for the Swapping of Data. In this experiment, we will be touching on 3 types of data.
* Contacts & Call Logs which is located at /data/data/com.android.providers.contacts/databases/contacts2.db 
* Text Messages which is located at /data/data/com.andrioid.providers.telephony/databases/mmssms.db 
* Calendar which is located at /data/data/com.android.providers.calendar/databases/calendar.db. 
### Therefore, we will be creating 6 different storage space for this 3 types of data) [init.rc reference](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/blob/master/Technical-Writeup-Files/init.rc)
![](Images/Journey5.png?raw=true)

# Part 6- Creation of a trigger to enable swapping of files, performing encryption of files when hiding sensitive files and decryption of files for displaying during the swap. [init.rc reference](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/blob/master/Technical-Writeup-Files/init.rc)
![](Images/Journey6.png?raw=true)

# Part 7- Repack the new Initial RAM File System and Repack the boot.img with the updated kernel and the new Initial RAM File System 
![](Images/Journey7.png?raw=true) 
* [Updated-Kernel](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/blob/master/Technical-Writeup-Files/zImage-dtb)  
* [New Initial RAM File System](https://github.com/negoug/Android-Experiments--Hindering_Forensics-/blob/master/Technical-Writeup-Files/initrd.img)

# Part 8- Replace the boot.img file with new boot.img file that you have created from Part 7.
* Load it into your device using Fastboot with the command: fastboot flash boot boot-new.img 


