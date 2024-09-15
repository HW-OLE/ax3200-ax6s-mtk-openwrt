# OpenWRT with MTK Drivers for the Xiaomi/Redmi AX3200/AX6S

Currently only the SNAPSHOT Build is built and tested because thats what I'm running.
Updates are upon reqest, if you don't want to build yourself, which is really easy.

To build follow the steps below:
1) Setup the build system as in https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem
2) Clone the OpenWRT Repository
   ```
   git clone https://git.openwrt.org/openwrt/openwrt.git
   ```
3) Add OpenWRT MTK Feeds
   ```
   cd openwrt
   echo "src-git mtk_openwrt_feed https://git01.mediatek.com/openwrt/feeds/mtk-openwrt-feeds" >> feeds.conf.default
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   ```
4) Apply MTK Patches
   ```
   cp -af ./feeds/mtk_openwrt_feed/master/files/* .
   for file in $(find ./feeds/mtk_openwrt_feed/master/patches-base -name "*.patch" | sort); do patch -f -p1 -i ${file}; done
   ```
5) Make Buildconfig
   ```
   make menuconfig
   ```
   Select Mediatekk Ralink ARM -> Target and MT7622 -> Subtarget and Target Profile -> Xiaomi Redmi Router AX6S and remeber to save your config as .config
6) Build
   ```
   make V=s -j$(nproc)
   ```
For optimal performance append the following options to your /etc/config/wireless to your 5GHz radio (Line should start with config wifi-device 'radio1') [Source](https://forum.openwrt.org/t/solved-802-11ax-worse-than-802-11ac-with-mt76-driver/126466/111)
```
option beacon_int '101'
option cell_density '0'
option he_bss_color '8'
option he_su_beamformee '1'
```
