# Partition Layout & Filesystem Information

Here is the partition layout together with filesystem information about all MC7010 units:

***MC7010-7010CA-7010(mmWave)-7010(China)***
| Dev    | Size     | Erase Size | Name          |
|--------|----------|------------|---------------|
| mtd0   | 00280000 | 00040000   | "sbl"         |
| mtd1   | 00280000 | 00040000   | "mibib"       |
| mtd2   | 00b00000 | 00040000   | "efs2"        |
| mtd3   | 00600000 | 00040000   | "efs2bak"     |
| mtd4   | 001c0000 | 00040000   | "tz"          |
| mtd5   | 00100000 | 00040000   | "tz_devcfg"   |
| mtd6   | 00180000 | 00040000   | "ddr"         |
| mtd7   | 00100000 | 00040000   | "apdp"        |
| mtd8   | 00100000 | 00040000   | "xbl_config"  |
| mtd9   | 00100000 | 00040000   | "multi_image" |
| mtd10  | 00100000 | 00040000   | "aop"         |
| mtd11  | 00100000 | 00040000   | "qhee"        |
| mtd12  | 00100000 | 00040000   | "abl"         |
| mtd13  | 00280000 | 00040000   | "uefi"        |
| mtd14  | 00180000 | 00040000   | "toolsfv"     |
| mtd15  | 00180000 | 00040000   | "loader_sti"  |
| mtd16  | 00d00000 | 00040000   | "boot"        |
| mtd17  | 00100000 | 00040000   | "scrub"       |
| mtd18  | 06640000 | 00040000   | "modem"       |
| mtd19  | 001c0000 | 00040000   | "misc"        |
| mtd20  | 00180000 | 00040000   | "devinfo"     |
| mtd21  | 00d00000 | 00040000   | "recovery"    |
| mtd22  | 001c0000 | 00040000   | "fota"        |
| mtd23  | 02b00000 | 00040000   | "recoveryfs"  |
| mtd24  | 00100000 | 00040000   | "sec"         |
| mtd25  | 08700000 | 00040000   | "zterw"       |
| mtd26  | 0a100000 | 00040000   | "system"      |

***MC7010D***
|   Dev  |   Size   | Erase Size |      Name     |
|--------|----------|------------|---------------|
| mtd0   | 00280000 | 00040000   | "sbl"         |
| mtd1   | 00280000 | 00040000   | "mibib"       |
| mtd2   | 00b00000 | 00040000   | "efs2"        |
| mtd3   | 00600000 | 00040000   | "efs2bak"     |
| mtd4   | 001c0000 | 00040000   | "tz"          |
| mtd5   | 00100000 | 00040000   | "tz_devcfg"   |
| mtd6   | 00180000 | 00040000   | "ddr"         |
| mtd7   | 00100000 | 00040000   | "apdp"        |
| mtd8   | 00100000 | 00040000   | "multi_image" |
| mtd9   | 00100000 | 00040000   | "aop"         |
| mtd10  | 00100000 | 00040000   | "qhee"        |
| mtd11  | 00100000 | 00040000   | "abl"         |
| mtd12  | 00280000 | 00040000   | "uefi"        |
| mtd13  | 00180000 | 00040000   | "toolsfv"     |
| mtd14  | 00180000 | 00040000   | "loader_sti"  |
| mtd15  | 00d00000 | 00040000   | "boot"        |
| mtd16  | 00100000 | 00040000   | "scrub"       |
| mtd17  | 06640000 | 00040000   | "modem"       |
| mtd18  | 001c0000 | 00040000   | "misc"        |
| mtd19  | 00180000 | 00040000   | "devinfo"     |
| mtd20  | 00d00000 | 00040000   | "recovery"    |
| mtd21  | 001c0000 | 00040000   | "fota"        |
| mtd22  | 02b00000 | 00040000   | "recoveryfs"  |
| mtd23: | 00100000 | 00040000   | "sec"         |
| mtd24: | 08680000 | 00040000   | "zterw"       |
| mtd25: | 000c0000 | 00040000   | "ipa_fw"      |
| mtd26: | 00100000 | 00040000   | "usb_qti"     |
| mtd27: | 0a0c0000 | 00040000   | "system"      |

The most important partitions that usually need to be swapped between different firmwares are: ***efs2, uefi, modem, boot and system***:

| Partition Name | Description                                                                                                                                                                                                             |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **efs2**       | Contains all baseband configuration (IMEI, BB settings and so on). Be careful and make a backup of the whole partition using Qualcomm Tool (QPST to backup as QCN file).                                                |
| **uefi**       | Contains the [RexOS](https://en.wikipedia.org/wiki/REX_OS) system that is loaded by the baseband. It will read all DSP firmwares from the modem partition (AKA NON-HLOS) to initialize all radio stuff.                 |
| **modem**      | Contains all DSP firmwares loaded by UEFI.                                                                                                                                                                              |
| **boot**       | It's the Linux Kernel used by the AP processor to load embedded drivers and start everything from Root FS                                                                                                               |
| **zterw**      | It's used by Root FS to store all settings that should be persistent across reboots. When you factory reset the CPE, by using either the physical or WebUI button, the volumes inside this ***UBI*** will be formatted. |
| **system**     | It's the Linux Root FS where all binaries are stored and ran at boot after kernel startup.                                                                                                                              |

**system** & **modem** partitions are created using ***UBIFS*** on top of an ***UBI*** image layout. Both can be accessed in read-write mode after having enabled SSH or TELNET on a stock unit, so changes on the filesystem are possible.

**system** contains 2 volumes:

| Volume Name | Description                                                                                              |
|-------------|----------------------------------------------------------------------------------------------------------|
| ***rootfs***      | Contains all Linux and ZTE executables, it can modified to add sshd/telnetd and other tools        |
| ***zte_data***    | Contains EFS default configuration used after the device has been reset; web server pages; default AND custom parameters used by ZTE binaries, like admin password, IP, CPE mode and so on |

These two volumes can be extracted using [ubidump](https://github.com/nlitsme/ubidump) and [ubireader](https://github.com/onekey-sec/ubi_reader) tools, here is a little break down on how to extract and repack them:

⚠️ *Run all commands as **root** because there are some special files (like /dev directory) that need to be created* ⚠️

- Install the *ubireader* and *ubidump* tools (see the link above for a how-to guide)
- Copy the **sdxprairie-sysfs.ubi** file into a directory, then run `ubidump --savedir . --preserve sdxprairie-sysfs.ubi`
- This will create two folders, with path `{rootfs|ztedata}`
- Run `ubireader_display_info sdxprairie-sysfs.ubi | grep Sequence` again on **sdxprairie-sysfs.ubi** and take note of the big number after the string (called *ID_FROM_UBI_DISPLAY_INFO* from now on), this is to be used when ***UBI*** image is repacked. 
- All modifications are to be done inside the {rootfs|ztedata} folders (like changing password hash, adding scripts, adding tools and so on)
- Re-create the two ***UBIFS*** with these commands:
    - **rootfs**: `/usr/sbin/mkfs.ubifs -m 4096 -e 253952 -c 2146 -x lzo -f 8 -k r5 -p 1 -l 4 -F -r rootfs rootfs_vol.ubifs`
    - **ztedata**: `/usr/sbin/mkfs.ubifs -m 4096 -e 253952 -c 68 -x lzo -f 8 -k r5 -p 1 -l 4 -F -r ztedata ztedata_vol.ubifs`
      
      <sup>Little known fact: During my testing, I attempted to understand why ***UBIFS*** would get corrupted when changing any files after flashing it in ***EDL Mode*** instead of ***fastboot***. After researching ***UBI*** manpages, I used the `-F` flag to prevent this issue. For more information, you can visit this [link](http://www.linux-mtd.infradead.org/faq/ubifs.html#L_free_space_fixup)</sup>
- Now create a file called **ubi.ini** with this content:
  
```
[rootfs]
mode = ubi
image = rootfs_vol.ubifs
vol_type = dynamic
vol_id = 0
vol_name = rootfs
vol_alignment = 1
vol_size = 141705216

[ztedata]
mode = ubi
image = ztedata_vol.ubifs
vol_type = dynamic
vol_flags = autoresize
vol_id = 1
vol_name = ztedata
vol_alignment = 1
vol_size = 8888320
```
- Finally create the new **sdxprairie-sysfs.ubi** using `/usr/sbin/ubinize -p 262144 -m 4096 -O 4096 -s 4096 -x 1 -Q ID_FROM_UBI_DISPLAY_INFO -o mod_sdxprairie-sysfs.ubi ubi.ini`
- This file now can be replaced in your QFIL package and flashed on the unit

If you want to modify ***rootfs\ztedata*** without having to use QFIL to flash the firmware every time, [erasing the boot partiton](edl.md#force-cpe-to-boot-in-fastboot) is possible, so the **abl** (Android Boot Loader) will automatically start in ***fastboot mode***, making it possible to boot your own kernel or to flash other partitions without needing to put the CPE in ***EDL mode***. For instance, to reflash the system partition (when you are on ***fastboot mode***), simply run these commands:

```
fastboot erase system
fastboot flash system  mod_sdxprairie-sysfs.ubi
```

When finished, boot the kernel with the command: `fastboot boot sdxprairie-boot.img`.

If everything is fine, just reboot the unit and permanently flash the kernel with the command: `fastboot flash boot sdxprairie-boot.img` and then `fastboot boot sdxprairie-boot.img`.

# Important notes about update capabilities

If you make any changes to the **rootfs** or **ztedata** folders, any **dFOTA** (Delta Firmware Over The Air) update from **ZTE** will fail. This is because, during the process, all data is binary patched. Before applying the patch, the system compares the CRC of the old file with the new one. If even a single file doesn't match, the update will stop and won't proceed. To preserve the update capability, you need to repack the firmware as it is, apply the updates, dump it again, and then repack the updated version with your modifications.
