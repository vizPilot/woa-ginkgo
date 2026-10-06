<img align="right" src="https://raw.githubusercontent.com/vizPilot/woa-ginkgo/main/media/Xiaomi-Redmi-Note-8T.png" width="350" alt="Windows 11 running on ginkgo">

# Running Windows on the Xiaomi Redmi Note 8/8T

## Partitioning your device

### Prerequisites
- A brain (most important of all)
- Latest version of MIUI installed
- [TWRP](https://github.com/graphiks/woa-raphael/releases/download/raphael-partitioning/twrp.img)

- [ADB & Fastboot](https://developer.android.com/studio/releases/platform-tools)

- [Parted](https://github.com/graphiks/woa-raphael/releases/download/raphael-partitioning/parted)

### Notes
> [!WARNING]  
> If you ever delete any partitions via diskpart, Windows will send a UFS command which would erase the entire UFS storage!
> 
> All your data will be erased! Backup now if needed.
> 
> Do not run the same command twice.
> 
> DO NOT REBOOT YOUR PHONE if you think you made a mistake, ask for help in the [Telegram chat](https://t.me/woaginkgo).
> 
>
> Do not run all commands at once, execute them in order!
>
> YOU CAN BREAK YOUR DEVICE WITH THE COMMANDS BELOW IF YOU DO THEM WRONG!!!

> [!IMPORTANT]
> Make sure you use the provided TWRP Recovery throughout this whole tutorial as this will make it easier for you.
> 
> If you don't use it and you face any errors, consider it your fault and consider yourself alone if you try asking for support as you have deferred from the main guide.

##### Flash TWRP recovery
> Open a CMD window inside the platform-tools folder, then (while your phone is in fastboot mode) run
```cmd
fastboot flash recovery path\to\twrp.img reboot recovery
```

#### Backing up important files
Use TWRP now to back up your Modem and EFS partition (as well as anything else if you have important data). Move this backup to a safe place (e.g your PC) as the next steps will wipe your data.

> [!warning]
> **IF YOU PROCEED WITHOUT BACKING UP MODEM AND EFS, YOU ARE ON YOUR OWN IF YOU MESS UP**

### Partitioning guide
> Your Xiaomi Redmi Note 8/8T may have different storage sizes. This guide uses the values of the 64GB model as an example. When relevant, the guide will mention if other values can or should be used.

##### Extending the partition limit
```cmd
adb shell sgdisk --resize-table=128 /dev/block/mmcblk0
```

##### Preparing for partitioning
> Download the parted file and move it in the platform-tools folder, then run
```cmd
adb push parted /cache/ && adb shell "chmod 755 /cache/parted" && adb shell /cache/parted /dev/block/mmcblk0
```

##### Printing the current table partition:
> Parted will print the list of partitions, userdata should be the last partition in the list.
```cmd
print
```

##### Resizing userdata
> Replace $ with the number of the userdata partition
>
> If it asks you if you are okay with data loss, type yes
```cmd
resizepart $
```
> Parted will now ask you for the end value.
> You can choose the size you want, as long as it is lower than the value it provides to you. In this example we resize it to 32GB
```cmd
End? [121GB]? 32GB
```
Note: 60GB is parted telling us the maximum end value we can select.

##### Checking free space
```cmd
p free
```

##### Creating ESP partition
> 32GB is the **End** of the **userdata** partition in this example and 32.5GB is the end of the ESP partition we will be creating, which will be 500MB in size

> Replace 32GB with the actual value you used when resizing the partition, then add 0.5 to this value and use it for the second value
```cmd
mkpart esp fat32 32GB 32.5GB
```

> Replace "$" with your ESP partition number, usually 32
```cmd
set $ esp on
```

##### Creating Windows partition
> In this example 58.5GB is the end value of your phone's total storage. Replace this with the actual end value you see when executing "p free"

> 32.5GB in this example is the end of ESP, replace this with your actual value accordingly as well
```cmd
mkpart win ntfs 32.5GB 58.5GB
```

##### Exit parted
```cmd
quit
```
> Ignore "Information: You may need to update /etc/fstab."
##### Formatting data
Format all data in TWRP, or Android will not boot.
( Go to Wipe > Format data > type yes )

#### Check if Android still starts
Just restart the phone, and see if Android still works


## [Next step: Installing Windows](/guide/install-2.md)
