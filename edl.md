# Steps to put CPE in EDL Mode (on Linux) and play with partitions

Before starting, be sure you have [Bjoern Kerler's EDL tools](https://github.com/bkerler/edl) and sg3-utils already installed on your machine.

Please use this [prog_firehose.mbn](https://github.com/bkerler/Loaders/blob/a83850577f32faaf4694cda8f00666e0d9d34654/qualcomm/model_generic/sdx55/0000000000000000_d9357db88795b5a8_fhprg.bin) as loader to interact with the CPE.

⚠️ All commands must be run as ***root*** to avoid any issues with permissions ⚠️

Connect the CPE to your PC using a USB-C cable, it's important to use the CPE's PoE adapter during all the procedure to avoid issues with boot. The following output (or similar) should be visible in `dmesg`:

```
[3005700.308747] usb 1-1: new high-speed USB device number 94 using xhci_hcd
[3005700.459171] usb 1-1: New USB device found, idVendor=19d2, idProduct=1225, bcdDevice=56.47
[3005700.459174] usb 1-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[3005700.459175] usb 1-1: Product: ZTE Mobile Broadband
[3005700.459176] usb 1-1: Manufacturer: ZTE,Incorporated
[3005700.459176] usb 1-1: SerialNumber: MF9910ZTED000000
[3005700.462119] usb-storage 1-1:1.0: USB Mass Storage device detected
[3005700.462354] usb-storage 1-1:1.0: Quirks match for vid 19d2 pid 1225: 1
[3005700.462449] scsi host9: usb-storage 1-1:1.0
[3005701.132721] usb 1-1: reset high-speed USB device number 94 using xhci_hcd
[3005703.281522] usb 1-1: USB disconnect, device number 94
[3005704.004472] usb 1-1: new high-speed USB device number 95 using xhci_hcd
[3005704.155441] usb 1-1: New USB device found, idVendor=19d2, idProduct=1405, bcdDevice=56.47
[3005704.155444] usb 1-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[3005704.155444] usb 1-1: Product: ZTE Mobile Broadband
[3005704.155445] usb 1-1: Manufacturer: ZTE,Incorporated
[3005704.155446] usb 1-1: SerialNumber: MF9930ZTED000000
[3005704.160609] cdc_ether 1-1:1.0 usb0: register 'cdc_ether' at usb-0000:00:14.0-1, ZTE CDC Ethernet Device, XX:XX:XX:XX:XX:XX
[3005704.161074] usb-storage 1-1:1.2: USB Mass Storage device detected
[3005704.161327] scsi host9: usb-storage 1-1:1.2
[3005705.189101] scsi 9:0:0:0: CD-ROM            ZTE      USB SCSI CD-ROM  2.31 PQ: 0 ANSI: 2
[3005705.189573] sr 9:0:0:0: Power-on or device reset occurred
[3005705.190636] sr 9:0:0:0: [sr1] scsi-1 drive
[3005705.191124] sr 9:0:0:0: Attached scsi CD-ROM sr0
[3005705.191239] sr 9:0:0:0: Attached scsi generic sg0 type 5
```

Now the CPE has to be switched from *CDROM+RNDIS* to *3 TTY MODE (Diag, Modem, NMEA)*. Doing so is accomplished using this command:

`sg_raw -n /dev/sg0 99 00 00 00 00 00`

In the provided example ***sg0*** was used because no other CDROM is installed, just check the `dmesg` to see which ***sgX*** is created when the CPE is connected to your PC. After executing it, the CPE will switch to DIAG mode and create three ***ttyUSBX*** ports: check the `dmesg` again to see how they are numbered.

Here is an example of successful switch:

```
[3005998.581911] usb 1-1: USB disconnect, device number 95
[3005998.581939] cdc_ether 1-1:1.0 usb0: unregister 'cdc_ether' usb-0000:00:14.0-1, ZTE CDC Ethernet Device
[3005999.216024] usb 1-1: new high-speed USB device number 96 using xhci_hcd
[3005999.366790] usb 1-1: New USB device found, idVendor=19d2, idProduct=0016, bcdDevice=56.47
[3005999.366794] usb 1-1: New USB device strings: Mfr=1, Product=2, SerialNumber=0
[3005999.366795] usb 1-1: Product: ZTE Mobile Broadband
[3005999.366796] usb 1-1: Manufacturer: ZTE,Incorporated
[3005999.373256] option 1-1:1.0: GSM modem (1-port) converter detected
[3005999.373318] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB0
[3005999.373389] option 1-1:1.1: GSM modem (1-port) converter detected
[3005999.373439] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB1
[3005999.373506] option 1-1:1.2: GSM modem (1-port) converter detected
[3005999.373560] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB2
```

Now the CPE can be switched to ***EDL mode***, this is needed to use the aforementioned **edl** tools by Bjoern Kerler. 
Run this command:

`qc_diag.py cmd 4b650100`

Use `lsusb` to check the following entry is available:

```
lsusb | grep 9008
Bus 004 Device 032: ID 05c6:9008 Qualcomm, Inc. Gobi Wireless Modem (QDL mode)
```

If everything worked, the CPE is now in ***EDL mode*** :-)

# Let's play with the edl tools

With the CPE in ***EDL mode***, edl commands can be used to check, dump, erase or write partitions.

Run this command to show the current layout of the firmware's partitions:

`edl printgpt --memory=NAND --loader=/path/to/prog_firehose.mbn`

Output example:

```
Qualcomm Sahara / Firehose Client V3.61 (c) B.Kerler 2018-2023.
main - Using loader /home/user/prog_firehose.mbn ...
main - Waiting for the device
main - Device detected :)
sahara - Protocol version: 2, Version supported: 1
main - Mode detected: sahara
sahara -
------------------------
HWID:              0x000cf0e100000000 (MSM_ID:0x000cf0e1,OEM_ID:0x0000,MODEL_ID:0x0000)
CPU detected:      "SDX55M:CD90-PH809"
PK_HASH:           0xd40eee56f3194665574109a39267724ae7944134cd53cb767e293d3c40497955bc8a4519ff992b031fadc6355015ac87000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
Serial:            0xd0582dd4

sahara - Protocol version: 2, Version supported: 1
sahara - Uploading loader /home/user/prog_firehose.mbn ...
sahara - 32-Bit mode detected.
sahara - Firehose mode detected, uploading...
sahara - Loader successfully uploaded.
main - Trying to connect to firehose loader ...
firehose - INFO: Binary build date: Jan 25 2021 @ 18:29:21
firehose - INFO: Binary build date: Jan 25 2021 @ 18:29:21
firehose - INFO: Chip serial num: 0 (0x0)
firehose - INFO: Supported Functions (15):
firehose - INFO: program
firehose - INFO: read
firehose - INFO: nop
firehose - INFO: patch
firehose - INFO: configure
firehose - INFO: setbootablestoragedrive
firehose - INFO: erase
firehose - INFO: power
firehose - INFO: firmwarewrite
firehose - INFO: getstorageinfo
firehose - INFO: benchmark
firehose - INFO: emmc
firehose - INFO: ufs
firehose - INFO: fixgpt
firehose - INFO: getsha256digest
firehose - INFO: End of supported functions 15
firehose
firehose - [LIB]: Couldn't detect MaxPayloadSizeFromTargetinBytes
firehose
firehose - [LIB]: Couldn't detect TargetName
firehose - TargetName=Unknown
firehose - MemoryName=nand
firehose - Version=1
firehose - Trying to read first storage sector...
firehose - Running configure...
firehose - Storage report:
firehose - total_blocks:2048
firehose - block_size:262144
firehose - page_size:4096
firehose - num_physical:1
firehose - manufacturer_id:44
firehose - serial_num:0
firehose - fw_version:
firehose - mem_type:NAND
firehose - prod_name:
firehose_client - Supported functions:
-----------------
program,read,nop,patch,configure,setbootablestoragedrive,erase,power,firmwarewrite,getstorageinfo,benchmark,emmc,ufs,fixgpt,getsha256digest
firehose - Nand storage detected.
firehose - Scanning for partition table ...
Progress: |██████████| 100.0% Scanning (Sector 0x400 of 0x400, ) 0.00 MB/s
firehose - Found partition table at sector 640 :)
oneplus
oneplus - [LIB]: No module named 'qrcode'
firehose - Nand storage detected.
firehose - Scanning for partition table ...

Parsing Lun 0:
Name                Offset		Length		Attr			Flash
-------------------------------------------------------------
sbl             	00000000	00280000	0xff/0x1/0x0	0
mibib           	00280000	00280000	0xff/0x1/0xff	0
efs2            	00500000	00B00000	0xff/0x1/0xff	0
efs2bak         	01000000	00600000	0xff/0x1/0xff	0
tz              	01600000	001C0000	0xff/0x1/0x0	0
tz_devcfg       	017C0000	00100000	0xff/0x1/0x0	0
ddr             	018C0000	00180000	0xff/0x1/0xff	0
apdp            	01A40000	00100000	0xff/0x1/0x0	0
multi_image     	01B40000	00100000	0xff/0x1/0x0	0
aop             	01C40000	00100000	0xff/0x1/0x0	0
qhee            	01D40000	00100000	0xff/0x1/0x0	0
abl             	01E40000	00100000	0xff/0x1/0x0	0
uefi            	01F40000	00280000	0xff/0x1/0x0	0
toolsfv         	021C0000	00180000	0xff/0x1/0x0	0
loader_sti      	02340000	00180000	0xff/0x1/0x0	0
boot            	024C0000	00D00000	0xff/0x1/0x0	0
scrub           	031C0000	00100000	0xff/0x1/0x0	0
modem           	032C0000	06640000	0xff/0x1/0x0	0
misc            	09900000	001C0000	0xff/0x1/0x0	0
devinfo         	09AC0000	00180000	0xff/0x1/0x0	0
recovery        	09C40000	00D00000	0xff/0x1/0x0	0
fota            	0A940000	001C0000	0xff/0x1/0x0	0
recoveryfs      	0AB00000	02B00000	0xff/0x1/0x0	0
sec             	0D600000	00100000	0xff/0x1/0x0	0
zterw           	0D700000	08680000	0xff/0x1/0x0	0
ipa_fw          	15D80000	000C0000	0xff/0x1/0x0	0
usb_qti         	15E40000	00100000	0xff/0x1/0x0	0
system          	15F40000	0A0C0000	0xff/0x1/0x0	0
```

## Reading, erasing and writing partitions

Each time a partition is modified using EDL, re-writing the **SBL** *(secondary boot loader)* and partition layout (used re-calculate all CRCs) is necessary. To do this, this command can be used (use **SBL1+P-Layout** based on your QFIL package):

⚠️ **IF SBL1 AND PARTITONS LAYOUT ARE ERASED, YOUR UNIT WILL ALWAYS BOOT IN EDL MODE** ⚠️

Erase SBL1+Partition-Layout using this command:
```
edl es 0 639 --memory=NAND --sectorsize=4096 --loader=/path/to/prog_firehose.mbn
edl es 640 1279 --memory=NAND --sectorsize=4096 --loader=/path/to/prog_firehose.mbn
```

Write back SBL1+Partition-Layout using this command:
```
edl ws 640 partition_complete_p4K_b256K.mbn --memory=NAND --sectorsize=4096 --loader=/path/to/prog_firehose.mbn
edl ws 0 sbl1.mbn --memory=NAND --sectorsize=4096 --loader=/path/to/prog_firehose.mbn
```

Read a single partition using this command:
```
edl r system test_system.bin --memory=NAND --loader=/path/to/prog_firehose.mbn
```

Erase a single partition using this command:
```
edl e system --memory=NAND --loader=/path/to/prog_firehose.mbn
```

Write a single partition using this command:
```
edl w system system.bin --memory=NAND --loader=/path/to/prog_firehose.mbn
```

Make a backup of an entire partition using this command:
```
mkdir dump_dir
edl rl dump_dir --memory=NAND --loader=/path/to/prog_firehose.mbn
```
These files cannot be rewritten as is, they need to be refectored.

Reset the unit, making it boot back to normal mode, using this command:
```
edl reset --resetmode=reset --loader=/path/to/prog_firehose.mbn
```

In case the unit is stuck in DIAG mode (3 TTY), use this command to get it out:
```
qc_diag.py cmd 4baa000000
qc_diag.py cmd 290200
```

# Force CPE to boot in FASTBOOT

Use the commands to erase `boot` partition and make CPE boots into ***fastboot*** mode

```
edl e boot --memory=NAND --loader=/path/to/prog_firehose.mbn
edl reset --resetmode=reset --loader=/path/to/prog_firehose.mbn
```

As discussed in the [Partition Layout & Filesystem Information](fs.md) page, [erasing](edl.md#force-cpe-to-boot-in-fastboot) just the ***boot*** partition, so ***fastboot*** can be used to erase and write partitions and also to avoid rewriting **SBL1** each time, is preferred.
