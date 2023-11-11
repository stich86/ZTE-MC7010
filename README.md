# This is a repository with all informations about ZTE MC7010 (Outdoor 5G CPE) family

The [MC7010](https://ztedevices.com/en-gl/mc70102/) CPE is a 4G/5G outdoor unit produced by ZTE with these specs:

- Network support: 5G SA/NSA Sub-6G/mmWave + 4G LTE Networks TDD/FDD
- Chipset: Qualcomm 5G SDX55M Platform
- CPU: 1x Cortex A7 up to 1.4GHz
- RAM: 256MB
- NAND: 512MB
- LED: Power, Network, LAN, Signal RSSI (three leds)
- 4G LTE Cat: 20
- 5G Specs:
    - Max Speed: download up to 3.8Gbps, upload up to 331Mbps
    - Bandwidth: Sub-6G@100Mhz, mmWave@800Mhz
    - Modulation: Sub-6G DL/UL 256QAM, mmWave DL/UL 64QAM
- Dual Mode: Bridge or Router, there is no embedded Wi-Fi
- Network connectivity: 2.5GbE interface with PoE 802.3af/at (bundled injector)
- Other connectivity:
    - USB Type-C with CDROM+RNDIS emulation. It's also used for diagnostic and firmware flashing
    - BLE: Used with *"Installer Helper App"* ([Android](https://play.google.com/store/apps/details?id=com.zte.fwainstallwizard&hl=it&gl=US&pli=1)/[iOS](https://apps.apple.com/tr/app/install-helper/id1541447686)) to point the CPE when installed outside
    - GPS: Some HW revs has also embedded GPS used by ISP to lock device connection on a specific position, it can be disabled. Doesn't seems to work on 7010D revs from my test
- Dock or Wall-Kit: based on the hardware version, you will have also a dock to use the unit inside

# Useful Stuff

- [Hardware Revision](hwrev.md)
- [Partition & Filesystem Info](fs.md)
- [LTE/NR5G CA Combos](cacombo.md)
- [Play with EDL tools and partitions](edl.md)
- [Enter 'Emergency Download Mode' (EDL) if your unit is bricked](enter_edl_brick.md)

⚠️ Certain links in this repo will lead to other repositories which are not under my control.
You accept that I have no control over and accepts no liability in respect of materials, products or services available externally of this repo. ⚠️

Any help is really appreciated, feel free to open a PR to fix or add informations 😊
