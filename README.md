# OpenWRT with MTK Drivers for the Xiaomi/Redmi AX3200/AX6S

Currently only the SNAPSHOT Build is built/provided under Releases -----------------------------------------------> 
and tested because thats what I'm running.
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
If you don't know what is meant with that look at the sample config below:
```
config wifi-device 'radio0'
	option type 'mac80211'
	option phy 'wl0'
	option band '2g'
	option channel 'auto'
	option htmode 'HT40'
	option country 'DE'
	option cell_density '0'

config wifi-iface 'default_radio0'
	option device 'radio0'
	option network 'lan'
	option mode 'ap'
	option ssid 'ssid_name'
	option encryption 'sae-mixed'
	option key 'ssid_key'
	option ocv '0'

config wifi-device 'radio1'
	option type 'mac80211'
	option phy 'wl1'
	option band '5g'
	option channel '44'
	option htmode 'HE80'
	option beacon_int '101'
	option cell_density '0'
	option he_bss_color '8'
	option he_su_beamformee '1'
	option country 'DE'

config wifi-iface 'default_radio1'
	option device 'radio1'
	option network 'lan'
	option mode 'ap'
	option ssid 'ssid_name_5g'
	option encryption 'sae-mixed'
	option key 'ssid_key'
	option ocv '0'

```
