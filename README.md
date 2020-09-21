# Nvidia GPU Fan Controller for linux

A simple **standalone python script** to keep your Nvidia GPUs below a given temperature, e.g.

```
python3 nvidia_fan_controller.py --target-temperature 60
```

This script uses a simple [PID-controller](https://en.wikipedia.org/wiki/PID_controller) to regulate
the fan speed.


## Dependencies

The `nvidia_fan_controller.py` script does not depend on any other python libraries. It does,
however, require the following two command-line utilities:

- `nvidia-settings`
- `nvidia-smi`

Please make sure that these are installed on your system.

Also, make sure that you've enabled manual fan control on your system. This can be done by using the
`nvidia-xconfig` command-line utility:

```
nvidia-xconfig --cool-bits=4
```

To update your `/etc/X11/xorg.conf` manually instead, scroll down to see an example.


## Install as a service

You would probably like to **automatically start** the controller at start-up. Here's how to do that
using `systemd`.

First see if the script runs without any issues, preferably running some benchmark / stress-test on
your GPU to check if the fan speed is properly regulated by the controller. The latter is important
to ensure that your computer won't catch fire 🔥.

Create a systemd service file:

```
python3 nvidia_fan_controller.py --create-service-file
```

This creates a file called `nvidia-fan-controller.service` in the same dir as the main script. Check
the contents of the service file to see if all fields are populated properly.

If all looks good, install the service using the following sequence of commands:

```
sudo cp nvidia-fan-controller.service /etc/systemd/system/
sudo systemctl enable nvidia-fan-controller.service
sudo systemctl daemon-reload
sudo systemctl restart nvidia-fan-controller.service
```


## Uninstall service

If you wish to uninstall the service, run the following commands to undo the installation steps
outlined above:

```
sudo systemctl stop nvidia-fan-controller.service
sudo systemctl disable nvidia-fan-controller.service
sudo systemctl daemon-reload
sudo rm /etc/systemd/system/nvidia-fan-controller.service
```


## Example xorg.conf

Here's the xorg.conf that I use for my system. I've got three GPUs: one GTX 1080Ti and two GTX
1070Ti. This `/etc/X11/xorg.conf` file sets the
[Coolbits](https://wiki.archlinux.org/index.php/NVIDIA/Tips_and_tricks#Overclocking_and_cooling)
option to **4**, which tells the nvidia driver to allow all fan settings to be set manually.

```
Section "ServerLayout"
    Identifier     "Layout0"
    Screen      0  "Screen0" Absolute 0 0
    Screen      1  "Screen1" Absolute 0 0
    Screen      2  "Screen2" Absolute 0 0
EndSection

Section "Monitor"
    Identifier     "Monitor0"
    VendorName     "Unknown"
    ModelName      "Unknown"
    Option         "DPMS"
EndSection

Section "Monitor"
    Identifier     "Monitor1"
    VendorName     "Unknown"
    ModelName      "Unknown"
    Option         "DPMS"
EndSection

Section "Monitor"
    Identifier     "Monitor2"
    VendorName     "Unknown"
    ModelName      "Unknown"
    Option         "DPMS"
EndSection

Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce GTX 1080 Ti"
    BusID          "PCI:1:0:0"
EndSection

Section "Device"
    Identifier     "Device1"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce GTX 1070 Ti"
    BusID          "PCI:2:0:0"
EndSection

Section "Device"
    Identifier     "Device2"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce GTX 1070 Ti"
    BusID          "PCI:3:0:0"
EndSection

Section "Screen"
    Identifier     "Screen0"
    Device         "Device0"
    Monitor        "Monitor0"
    DefaultDepth    24
    Option         "Coolbits" "4"
    SubSection     "Display"
        Depth       24
    EndSubSection
EndSection

Section "Screen"
    Identifier     "Screen1"
    Device         "Device1"
    Monitor        "Monitor1"
    DefaultDepth    24
    Option         "Coolbits" "4"
    SubSection     "Display"
        Depth       24
    EndSubSection
EndSection

Section "Screen"
    Identifier     "Screen2"
    Device         "Device2"
    Monitor        "Monitor2"
    DefaultDepth    24
    Option         "Coolbits" "4"
    SubSection     "Display"
        Depth       24
    EndSubSection
EndSection

```


## Credits

The icon used in the github link preview is
[Fan](https://thenounproject.com/term/fan/2871241) by
[Amethyst Studio](https://thenounproject.com/AmethystStudio/) from the
[Noun Project](https://thenounproject.com/).
