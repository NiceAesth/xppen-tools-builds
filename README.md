# xppen-tools builds

Public CI builds of `xppen-tools` for XP-Pen and UGEE tablets. USE AT YOUR OWN RISK. As XDA would put it: "Your warranty is now void. I am not responsible for bricked devices, dead SD cards, or you getting fired because your alarm app failed."

Firmware dumps + pre-patched versions: <https://github.com/NiceAesth/xppen-dumps>

If your tablet is not in the dump repository, use the commands below to make a backup of your firmware. For extra safety, opening it up is recommended to see if your chip is a Ugee 901, 902, 903 or GD32xxxx. If it is, then it is likely okay to proceed.

The XP-Pen G640 Rev A is currently NOT supported. If you have an updater for this revision or manage to get any form of bootloader dump, contact me and I will add support for it. (plz no useless msgs though)

## Download

Use the `Tip` release for the latest successful build.

- `xppen-tools-linux-x64.tar.gz`
- `xppen-tools-macos-universal.tar.gz`
- `xppen-tools-windows-x64.zip`
- `SHA256SUMS.txt`

`Tip` is a development build and can change at any time.

## Common commands

Show the connected bootloader and device info:

```sh
xppen-tools info
```

Reboot a running tablet into DFU mode:

```sh
xppen-tools dfu
```

Make a verified firmware backup:

```sh
xppen-tools dump backup
```

The only part of the firmware you should ever edit is the `code` section. Anything else will fail to flash and even if it would, you are almost definitely going to brick your tablet.

If you don't have any RE experience use the pre-patched versions in the firmware repo. If your tablet isn't in there wait for one to be added or ask someone that knows what they're doing (I do tablets whenever I feel like it so not me). If you don't know what RE means stick to the pre-patched ones.

Convert an application binary to Intel HEX (format used for flashing after making edits):

```sh
xppen-tools ihex firmware.bin --ic 902 -o firmware.hex
```

`--ic` can be `901`, `902`, or `903`. (you can see which IC you have by running `xppen-tools info`)

Flash a firmware image:

```sh
xppen-tools update firmware.hex
```

Make a backup before using `update`. Do not unplug the tablet while it is writing firmware.

Leave DFU mode without flashing:

```sh
xppen-tools exit-dfu
```

Use `--pid` when you need to select a bootloader. Supported bootloader PIDs are `1227`, `FFF1`, `FFF2`, and `FFF3`.
Slight warning for PID `FFF3` bootloaders, it does not have a DFU exit command. You might be stuck here.

Run `xppen-tools --help` for the full command list and options.

## Linux udev rules

Linux may also need udev rules for USB and hidraw access.

Create `/etc/udev/rules.d/99-xppen.rules`:

```udev
# Runtime device example. Replace 0905 with your tablet PID.
SUBSYSTEM=="usb", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="0905", MODE="0666"
KERNEL=="hidraw*", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="0905", MODE="0666"

# Bootloaders.
SUBSYSTEM=="usb", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="1227", MODE="0666"
KERNEL=="hidraw*", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="1227", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="fff1", MODE="0666"
KERNEL=="hidraw*", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="fff1", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="fff2", MODE="0666"
KERNEL=="hidraw*", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="fff2", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="fff3", MODE="0666"
KERNEL=="hidraw*", ATTRS{idVendor}=="28bd", ATTRS{idProduct}=="fff3", MODE="0666"
```

Reload the rules:

```sh
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Emotional support if you brick your tablet:

<https://github.com/user-attachments/assets/43f74e61-474e-4ea8-9b58-a885c2f6690b>
