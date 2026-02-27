# NoMachine Installation Guide

## KRIA KR260 + Ubuntu 22.04.5 LTS

---

## Overview

This guide explains how to install and configure **NoMachine** on a **KRIA KR260** board running:

* Ubuntu 22.04.5 LTS
* ARM64 architecture (`aarch64`)
* Headless by default

This setup provides:

* Headless boot (no GUI at startup)
* On-demand remote desktop via NoMachine
* XFCE lightweight desktop environment
* Optional manual HDMI display support

---

# 1. Verify System Architecture

Confirm the system is ARM64:

```bash
uname -m
```

Expected output:

```
aarch64
```

Verify Debian architecture:

```bash
dpkg --print-architecture
```

Expected:

```
arm64
```

---

# 2. Update Base System

```bash
sudo add-apt-repository ppa:xilinx-apps --yes &&
sudo add-apt-repository ppa:ubuntu-xilinx/default --yes &&
sudo add-apt-repository ppa:xilinx-apps/xilinx-drivers --yes &&
sudo add-apt-repository ppa:lely/ppa --yes &&
sudo apt update --yes &&
sudo apt upgrade --yes
```

---

# 3. Configure Headless Boot (Recommended Default)

Set system to boot without GUI:

```bash
sudo systemctl set-default multi-user.target
```

Disable any display manager if present:

```bash
sudo systemctl disable gdm3 --now 2>/dev/null
sudo systemctl disable lightdm --now 2>/dev/null
```

Reboot:

```bash
sudo reboot
```

Verify:

```bash
systemctl get-default
```

Expected:

```
multi-user.target
```

---

# 4. Install XFCE Desktop Environment

```bash
sudo apt install -y xfce4 xfce4-goodies dbus-x11
```

This installs XFCE without enabling graphical boot.

---

# 5. Download NoMachine (ARM64)

```bash
wget --content-disposition "https://downloads.nomachine.com/download/?id=30&platform=linux&distro=arm"
```

Verify the file:

```bash
file nomachine_*_arm64.deb
```

Expected:

```
Debian binary package (format 2.0)
```

---

# 6. Install NoMachine

```bash
sudo dpkg -i nomachine_*_arm64.deb
sudo apt -f install -y
```

Enable and start the service:

```bash
sudo systemctl enable nxserver --now
```

Verify:

```bash
systemctl status nxserver
```

Expected:

```
active (running)
```

---

# 7. Configure NoMachine to Use XFCE

Edit NoMachine configuration:

```bash
sudo nano /usr/NX/etc/node.cfg
```

Find or add the following line:

```
DefaultDesktopCommand "/usr/bin/startxfce4"
```

(Optional but recommended)

```
EnableVirtualDesktop 1
EnablePhysicalDesktop 0
```

Restart NoMachine:

```bash
sudo /usr/NX/bin/nxserver --restart
```

---

# 8. Verify Headless Operation

Before connecting:

```bash
ps aux | grep Xorg
```

There should be no active `Xorg` process.

---

# 9. Connect Using NoMachine Client

On your PC:

1. Install NoMachine Client as per OS from [nomachine.com](https://www.nomachine.com/)  
2. Connect to the KR260 IP address
3. Login with your Ubuntu credentials

Behavior:

* XFCE starts only during the remote session
* X server runs only while connected
* Disconnecting returns the system to headless state

---

# 10. Optional: Manual HDMI Display Support

Install LightDM:

```bash
sudo apt install lightdm
```

Disable auto-start to keep system headless by default:

```bash
sudo systemctl disable lightdm
```

When HDMI display is connected and local GUI is required:

```bash
sudo systemctl start lightdm
```

To return to headless mode:

```bash
sudo systemctl stop lightdm
```

---

# Recommended Performance Tweaks

Disable XFCE compositor (run inside XFCE session):

```bash
xfconf-query -c xfwm4 -p /general/use_compositing -s false
```

---

# Verification Checklist

* System boots headless
* No X server running at idle
* NoMachine service active
* XFCE launches on remote connection
* Optional HDMI works when manually enabled

---

# Final Result

This configuration provides:

* Professional headless embedded setup
* On-demand remote GUI
* Lightweight desktop environment
* Manual control over local display
* Clean and deterministic system behavior

---
