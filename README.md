# Gallium OS on Lenovo ThinkPad 13 Chromebook

It's a challenge to get Linux to run on Lenovo ThinkPad 13 Chromebook. Gallium OS is created especially for Chromebooks. But there are still issues on Lenovo ThinkPad 13 Chromebook. The biggest challenge is to get audio working. 

## Installing Gallium OS on Thinkpad
Lenovo ThinkPad 13 Chromebook is locked, so you cannot install Linux easily on it. But there is a workaround provided by the MrChromebox.tech.

You have to use [ChromeOS Firmware Utility Script](https://mrchromebox.tech/#fwscript). This tool performs two simple tasks: it sets the crossystem boot flag necessary to enable Legacy Boot mode, and it installs an RW_LEGACY firmware update appropriate for the device.

After updating the RW_LEGACY firmware, Legacy Boot Mode can be accessed via [CTRL+L] on the Developer Mode boot screen. It can also be set as the default by changing the GBB Flags via 'Set Boot Options' feature below.

Detailed instructions are provided on  [ChromeOS Firmware Utility Script](https://mrchromebox.tech/#fwscript) page.

Once you have updated the firmware, you can boot book from Gallium OS live USB device.

Boot your Chromebook into Gallium OS Live and follow instructions to install Gallium OS.

There is also an interesting more detailed article on linuxinsider.com:

- [Got a Screwdriver? GalliumOS Can Turn Chromebooks Into Linux Boxes](https://www.linuxinsider.com/story/85667.html).

### Requirements

- 8GB USB drive or SD Card
- [Gallium OS](https://galliumos.org) .iso image. [Download page](https://galliumos.org/download).
- [Balena Etcher](https://www.balena.io/etcher/) to create bootable USB disk / SD Card
- Few hours of free time

## Fixing ThinkPad 13 Chromebook
Once you have installed Gallium OS on your ThinkPad 13 Chromebook, most of hardware should work fine, even special buttons for dimming screen brightness, adjusting audio etc.

There are few issues left:

- SD Card is not mounted immediately
- No audio

### Extended Fat

To use extended FAT drives, install exfat drivers.

```
sudo apt install exfat-fuse exfat-utils
```

### Audio

To fix audio issues we have to upgrade Gallium OS to the latest testing version, which contains required fixes ThinkPad 13 Chromebook. For this purposes we have to enable related repos.

```
sudo galliumos-repodist --enable prerelease
sudo galliumos-repodist --enable testing
```
To check that repos are enabled use this command:

```
galliumos-repodist --status
```
Once you have repos enabled, you can upgrade your Chromebook. This upgrade adds latest fixes for [Skylake Chromebooks](https://github.com/GalliumOS/galliumos-skylake). Upgrading will take some time, so prepares some coffee or tea forehand.

You might encounter possible missing firmware for module i915 notices. To avoid these you can download missing items here:

- [i915 firmware](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915)

Add downloaded modules into **/lib/firmware/i915** directory.

```
sudo galliumos-update
```
Check you kernel and Gallium OS version:

```
uname -r
lsb_release -a
```
And when chromebook is upgraded to the latest Gallium OS, you need to reboot it.

```
reboot
```
Once you have rebooted chromebook, several tasks are needed to complete to fix audio.

```
cd /usr/share/alsa/ucm/
ls -l
```
You should have folder **sklnau8825adi** in directory /usr/share/alsa/ucm/.

As ThinkPad 13 Chromebook is Sentry book, but sklnau8825adi is for Caroline and Chell, some mods are needed. Sentry, Lars, Asuka and Cave require **sklnau8825max** instead of sklnau8825adi. For this purpose we need to duplicate sklnau8825adi as sklnau8825max.

```
cp -a /usr/share/alsa/ucm/sklnau8825adi /usr/share/alsa/ucm/sklnau8825max
```
Now we have to rename sklnau8825adi.conf to sklnau8825max.conf

```
mv /usr/share/alsa/ucm/sklnau8825max/sklnau8825adi.conf /usr/share/alsa/ucm/sklnau8825max/sklnau8825max.conf
```
Lets install **gedit** text editor for next task, which is editing HiFi.conf.

```
sudo apt install gedit
sudo gedit /usr/share/alsa/ucm/sklnau8825max/HiFi.conf
```
Search for **sklnau8825adi** and replace all occurences with **sklnau8825max** and save changed file. Reboot your chromebook now.

```
reboot
```
For next you have to update kernel version, as Gallium OS 2.1 comes with an older version of kernel, which does not support sklnau8825max driver.

Install Ukuu to update kernel.

```
sudo add-apt-repository ppa:teejee2008/ppa
```
```
sudo apt update && sudo apt install ukuu
```
Reboot your book after kernel update.

Once you have rebooted your chromebook, open Terminal again and load sklnau8825max.

```
sudo alsaucm -c sklnau8825max set _verb HiFi
```
And reboot again.

```
reboot
```
To switch outputs Speaker/Headphones:

```
sudo alsaucm -c sklnau8825max set _verb HiFi set _enadev Speaker
```
```
sudo alsaucm -c sklnau8825max set _verb HiFi set _enadev Headphone
```
To check your hardware status:

```
sudo apt install evtest
evtest
```


