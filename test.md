# Running DualBoot of Fedora and Android on Xiaomi pad 5

## Installation

-  ```Brain```

- ```PC/Laptop```

- ```Installed android```

- [```Recovery image```](https://github.com/ArKT-7/twrp_device_xiaomi_nabu/releases/download/mod_linux/V4-MODDED-TWRP-LINUX.img)

- [```image of fedora```](https://github.com/pocketblue/pocketblue/releases/tag/43.20251220)

### Opening CMD as an administrator
> [!NOTE]
> Don't know how to start? Unzip the downloaded [```Android platform tools```](https://developer.android.com/studio/releases/platform-tools), then open ```command prompt``` as an administrator and run the following command, replacing `"path\to\platform-tools"` with the actual path of the platform tools folder
```cmd
cd "path\to\platform-tools"
```
> Use this window throughout the entire guide. Do not close it.

> [!Note]
> If your device is not detected in fastboot or recovery mode, you'll have to install USB drivers [using this guide](troubleshooting-en.md#device-is-not-recognized-in-fastboot-or-recovery)

#### Reboot into fastboot mkde
- Boot your NABU into **fastboot mode** by holding down the **`volume down`** button while rebooting with a USB cable connected
- Alternatively, run the below command while booted in Android
```cmd
adb reboot bootloader
```

### Boot the modded recovery
> While in fastboot mode, replace `path\to\recovery.img` with the actual path of the recovery image
```cmd
fastboot boot path\to\recovery.img
```


- print your partition layout
```cmd
adb shell parted /dev/block/sda print
```

- make sure that partition `31` is `userdata`

```cmd
adb shell parted /dev/block/sda print | grep userdata | grep -E '^31'
```

- delete your `userdata` partition, this wipes all you data
```cmd
adb shell sgdisk --delete=31 /dev/block/sda
```

### create `userdata` and `fedora_root` partitions
```cmd
export start=$(adb shell parted -m /dev/block/sda print free | tail -1 | cut -d: -f2)
```
```cmd
adb shell parted -s /dev/block/sda -- mkpart userdata ext4 $start 50%
```
```cmd
adb shell parted -s /dev/block/sda -- mkpart fedora_root ext4 50% 100%
```
### reboot to bootloader
```cmd
adb reboot bootloader
```
### flash `fedora_rootfs.raw` to `fedora_root` partition
```cmd
fastboot flash fedora_root images/fedora_rootfs.raw
```
### erase dtbo
```cmd
fastboot erase dtbo
```

### Boot the modded recovery
> While in fastboot mode, replace `path\to\recovery.img` with the actual path of the recovery image
```cmd
fastboot boot path\to\recovery.img
```


