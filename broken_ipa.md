
# Fix broken IPA acceleration after repack

After some tests on new ZTE units (like **MC888** or **MC889**), I've found that when these are put in Router mode (using repacked firmware), the packet acceleration (**IPA**) is broken, and all traffic is software accelerated.

This issue seems related to a permission problem when the `mount-copybind` script should mount the `IPACM_cfg.xml` file from the filesystem `/etc_rw`. The script checks if the `stat` of the files are the same, and if not, it overwrites the default configuration into `/etc_rw`. This causes `ipacm` to read the wrong LAN address and break the acceleration.

In the `/etc/data/ipa` directory, there is the stock Qualcomm configuration using the standard subnet `192.168.225.0/24`, while ZTE uses its custom scripts and binaries to avoid using `/` as Read-Write. When you extract the `rootfs` UBI volume, even with the `-k` flag of `ubireader` or the `--preserve` flag of `ubidump`, the `IPACM_cfg.xml` file in `/etc/data/ipa` gets incorrect permissions. This file should be owned by the `radio` user and group, with permissions set to `755`.

So before repacking the `rootfs`, be sure to change the ownership and permissions of this file using these commands:

```
chown 1001:1001 PATHetc/data/ipa/IPACM_cfg.xml
chmod 755 /etc/data/ipa/IPACM_cfg.xml

```
Please note that **UID\GID 1001** are the user `radio` on the CPE

I want to give a big thanks to my friend [@awvarez](https://github.com/avwarez) who helped me find and fix this problem :)
