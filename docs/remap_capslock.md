# Make Capslock Useful
I find capslock to be a waste of valuable keyboard real estate, 
so the first thing I do after a fresh Arch Linux install is to remap it to be ```esc```
when pressed by itself, and ```ctrl``` with another key.  
Wayland makes this relatively difficult because of security features that prevent grabbing peripherals.
We can do this with a program called [interception-tools](https://gitlab.com/interception/linux/tools)
and the plugin ```interception-caps2esc``` that are both in the community repository.
To understand how this works, read the interception-tools [readme](https://gitlab.com/interception/linux/tools).
```
sudo pacman -S libevdev yaml-cpp interception-tools interception-caps2esc
```

When the install finishes, make a config file for the daemon.  Note that this requires root privilege.
```
sudo nvim /etc/udevmon.yaml
```
Place this in the file:
```
- JOB: intercept -g $DEVNODE | caps2esc -m 1 | uinput -d $DEVNODE
  DEVICE:
    EVENTS:
      EV_KEY: [KEY_CAPSLOCK]
```
Now add the daemon to systemd.
```
sudo nvim /etc/systemd/system/udevmon.service
```
Place this in the file:
```
[Unit]
Description=udevmon
Wants=systemd-udev-settle.service
After=systemd-udev-settle.service

[Service]
ExecStart=/usr/bin/nice -n -20 /usr/bin/udevmon -c /etc/udevmon.yaml

[Install]
WantedBy=multi-user.target
```
Enable the daemon and start it.
```
sudo systemctl enable --now udevmon.service
```
Now your caps lock should be successfully remapped.

---
# Next
[Setup Sway](setup_sway.md)
