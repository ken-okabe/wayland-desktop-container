# wayland-desktop-container
### How to set up your nested Wayland Desktop Environment with systemd-nspawn container, like VirtualBox


This tutorial walks you through setting up Wayland Desktop Environment with linux systemd-nspawn container on your computer. This is similar to VMware Workstation or VirtualBox, but linux only with minimal overhead performance.

## A Quick Look at the final result

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_025052.png)

## Features and benefits

✓ Hardware independent container OS by the hardware abstraction with extremely efficient, minimal performance overhead method by systemd-nspawn container technology  
✓ 100% portable among systemd enabled linux hosts, easy backup and Recovery  
✓ Direct rendering works such as 3D Desktop effects  
✓ Video and Sound works  
✓ Network works out of the box  
✓ Infrequent reboot operations for the host OS and the hardware, very fast container virtual boot, poweroff, reboot

## Summary of How to

1. Launch `kwin_wayland` window,  
that is nested on your current desktop environment.

2. Boot your container OS with `systemd-nspawn`

3. (a) Launch a Desktop Environment such as `XFCE` or `LXQT` on the targted `kwin_wayland` window.  
(b) Simply prepare your favorite launcher app like [synapse](https://launchpad.net/synapse-project) or [xfce4-panel](http://packages.ubuntu.com/xenial/xfce4-panel) alone for a minimal setup.

## Walk through

### HostOS with minimal applications

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_020953.png)

The hostOS can be any linuxOS with systemd and the desktop environment can be either Wayland or legacy X11.

Alghough, Wayland hostOS is obviously preferable, the situation is still immature. As of March 2017, only Fedora 25 sports Wayland-based GNOME session as the default over the X11-based one, but the other distros does not. The latest version of KDE-Plasma is stable with X11/Xorg, but unstable with Wayland.

Probably, if you use GNOME for the host environment, go for Wayland, but if Plasma or other DE, be conservative to use X11/Xorg for stability.

This method works very well on both conditions, and personally, I use Arch-linux with KDE-Plasma(X11/Xorg).

### Install `systemd-nspawn` and `kwin_wayland`

Some distro such as Arch already has `systemd-nspawn`, but others such as Ubuntu does not.

#### systemd-nspawn
[Binary package “systemd-container” in ubuntu xenial](https://launchpad.net/ubuntu/xenial/+package/systemd-container)

#### kwin-wayland
[Binary package “kwin-wayland” in ubuntu xenial](https://launchpad.net/ubuntu/xenial/+package/kwin-wayland)

Arch probably has `kwin_wayland` in `xorg-server-xwayland` package.

### Launch kwin_wayland window

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_021150.png)

`KWin` is known as one of the most feature complete and most stable window managers.
This is a direct rendering enabled `wayland` window space managed by `KWin`, and nested on your current desktop environment.

> [Starting a nested KWin](https://community.kde.org/KWin/Wayland#Starting_a_nested_KWin) @KWin/Wayland -  KDE Community Wiki  
Since 5.3 it is possible to start a nested KWin instance under either X11 or Wayland:

```bash
export $(dbus-launch); \
kwin_wayland --xwayland &;
```

`export (dbus-launch);` for `fish` shell



### Boot your containerOS

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_064041.png)

```
sudo systemd-nspawn \
-bD /YOUR_MACHINE_ROOT_DIRECTORY \
--volatile=no \
--bind-ro=/YOUR_HOME/.Xauthority:/root/.Xauthority \
--bind=/run/user/1000 \
--bind=/tmp/.X11-unix \
--bind=/dev/shm \
--bind=/dev/dri \
--bind=/run/dbus/system_bus_socket \
--bind=/YOUR_DATA_DIRECTORY
```

Bind `/YOUR_DATA_DIRECTORY` of the hostOS to the containerOS, so that you can share the data directory between both, at the same time, your containerOS can stay as small and clean as possible and good for portability and backup/restore.

##### typically, you build your container distro OS from minimal/server OS images.

Remember, **you do not need to instal X11/Xorg display server, or wayland for containerOS** since `kwin_wayland` window plays the role.

### Launch a DesktopEnvironment (XFCE)  on the targted `kwin_wayland` window.  

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_064335.png)

Remember, KWin is already running, and it's a feature complete and powerful WindowManager. You can launch and switch tasks with KWin via shortcut-keys, or prepare your favorite launcher app like `synaps` or `xfce4-panel` for a minimal setup.

However, if we need more user friendly Desktop Environments, just install and launch XFCE or LXQT that can run along with KWin.

```bash
export DISPLAY=:1; \
export XAUTHORITY=~/.Xauthority; \
export XDG_RUNTIME_DIR=/run/user/1000; \
export CLUTTER_BACKEND=x11; \
export QT_X11_NO_MITSHM=1; \
startxfce4;
```

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_064448.png)


### Maximize and remove the frame of the kwin_wayland window as default

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_022156.png)
![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_022156.png)

Probably, you want to remove the frame of the containerOS, this is how to on Plasma.


## Final result

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_025052.png)
![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_090100.png)

Confirm XFCE environment recognizes that running on XWAYLAND display.

[XWayland](https://wiki.archlinux.org/index.php/Wayland#XWayland) implements a compatibility layer to seamlessly run legacy X11 applications on Wayland.

So far, more like exceptionally, if you install [GUI libraries of wayland](https://wiki.archlinux.org/index.php/wayland#GUI_libraries), with a certain flag, you can see the GUI applications run natively on wayland.

![](https://raw.githubusercontent.com/wiki/kenokabe/wayland-desktop-container/images/Screenshot_20170318_093932.png)

The left is `kate` window with Xorg/X11 compatiblity mode.  
The right is the window with wayland native mode.

As you can see the native wayland app does not reflect the current window theme and the XFCE panel does not show the app task, and you cannot tell the difference of the performance as long as you use the normal applications of PC.

So, probably there's not much reason to pursuit wayland native mode app.
but the situation can be different for 3D games, and significantly different on small devices such as Raspberry Pi.


### What you may remove from the container OS

- linux kernels with various drivers
- /etc/fstab
- NetworkManager.service

### HostOS and ContainerOS interaction

You cannot Copy&Paste between HostOS and ContainerOS.  
You may consider to use [GoogleKeep](https://play.google.com/store/apps/details?id=com.google.android.keep&hl=en) to share contents between HostOS and ContainerOS, and of course, you shold have shared directories via systemd-nspawn bind.


### (Optional) legacy X11/Xorg
 Althogh this tutorial focuses on wayland nested window, [Xephyr](https://wiki.archlinux.org/index.php/Xephyr) (a nested X server that runs as an X application) has been around for a long time.

Unlike `kwin_wayland`, `Xepher` is not optimized for direct rendering and KWin Window manager is not bundled, so if you run KWin or other direct rendering composer on top of `Xepher`, things is going slow and inefficient, therefore, not recommended.

```bash
Xephyr -ac -screen 1200x700 -resizeable -reset :1 &;
```        
