# xppen-tools builds

Public CI builds of `xppen-tools`. USE AT YOUR OWN RISK. As XDA would put it: "Your warranty is now void. I am not responsible for bricked devices, dead SD cards, or you getting fired because your alarm app failed."

Firmware dumps: <https://github.com/NiceAesth/xppen-dumps>

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

Linux may also need udev rules for USB and hidraw access.

## Linux udev rules

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
