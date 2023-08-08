# ***TODAY ONLY - Uninstall is broken in 1.4.6, fixed in 1.4.7<br>the PPA may take 24 hours to update***

# Mainline Kernels
A tool for installing [Ubuntu Mainline Kernel Builds](https://wiki.ubuntu.com/Kernel/MainlineBuilds) onto debian-based distributions.

![Main window screenshot](6.4_boot_hang_note.jpg)

![Main window screenshot](main_window.png)

Sort by the Lock column to collect all the locked kernels together
![sort by locked](sort_by_locked.png)

Sort by the Status column to collect all the installed kernels together
![sort by status](sort_by_status.png)

Sort by the Notes column to see all kernels with any remarks
![sort_by_notes](sort_by_notes.png)

## About
mainline is a fork of [ukuu](https://github.com/teejee2008/ukuu)  

## Changes
* Changed name from "ukuu" to "mainline"
* Removed all GRUB / bootloader options
* Removed all donate buttons, links, dialogs
* Removed all unused & unrelated code
* Removed all TeeJee lib code
* Better cache management
* Rewrote all exec() commands not to use temp files, temp bash scripts, and temp directories
* Rewrote the desktop notification scripts to be more reliable
* Reduced dependencies
* Per-kernel user notes
* Pinning/locking kernels
* Verify .deb file downloads with the sha256 hashes in the CHECKSUMS files
* Customizable external commands for the terminal window and for root access
* Support the alternative flavors besides "generic", ex: lowlatency, generic-64k, server, virtual, etc

## Features
* Download the list of available kernels from the [Ubuntu Mainline PPA](http://kernel.ubuntu.com/~kernel-ppa/mainline/)
* Display, install, and uninstall mainline-ppa kernels conveniently, gui and cli
* For each kernel the associated headers & modules packages are downloaded, installed, or uninstalled together
* .deb file downloads are verified with the sha256 hashes from the CHECKSUMS files
* Optionally monitor and send desktop notifications when new kernels become available

## Not Features
* Care if the kernels run or boot or work well or are compatible with your system. The kernel deb packages are produced by someone else, and with no warranty. When they work, great, when they don't, don't use them. This app intentionally does not even touch a single grub or bootloader file itself. All it does is download .deb packages that the ubuntu kernel team produces, and runs dpkg to install them the same way you would manually.

See the [comment by "setuid"](https://ubuntuhandbook.org/index.php/2020/08/mainline-install-latest-kernel-ubuntu-linux-mint/):  
> Note that these kernel packages are missing quite a bit that would be needed on most systems, and many dkms modules and other tools won’t work with them (NVIDIA drivers, VMware modules, etc.).
> 
> These packages also will not install nor boot on ARM64 (RPi4 for example), despite being spun for those architectures because they lack DTBs and correctly aligned headers.
> 
> On AMD64, you’ll find that the cloud tools and tools packages are missing, and installing them would try to bring in libssl3 and an incompatible libc6 from a newer release of Ubuntu, which will most-certainly break userland.
> 
> You have been warned.

This is all true.

Additionally the kernels are unsigned, and so they won't boot on a system with secure boot enabled.

The libssl & libc issue they mention were transient issues that naturally passed as time went on and most users systems caught up, but new examples of the same kind of issue are bound to appear again from time to time as the kernel.ubuntu.com team updates their build environment ahead of most users.

That said, I have been running these daily for several years. I just don't need any nvidia or vmware kernel modules nor do I use secure boot.

# Install
The [PPA](https://code.launchpad.net/~cappelikan/+archive/ubuntu/ppa) is kindly maintained by [cappelikan](https://github.com/cappelikan)  
```
sudo add-apt-repository ppa:cappelikan/ppa
sudo apt update
sudo apt install mainline
```
There are also .deb packages in [releases](../../releases/latest), generated by `make release_deb`  

# Build
```
sudo apt install libgee-0.8-dev libjson-glib-dev libvte-2.91-dev valac aria2 lsb-release make gettext dpkg-dev
git clone https://github.com/bkw777/mainline.git
cd mainline
make
sudo make install
```

# Usage
Look for System -> Mainline Kernels in your desktop's Applications/Start menu.

Otherwise:  
CLI
```
$ mainline --help
$ mainline
```
GUI
```
$ mainline-gtk
```
Note that neither of those commands invoked sudo or pkexec or other su-alike.  
The app runs as the user and uses pkexec internally just for the dpkg command.

## Buttons
**\[ Install \]** - downloads and installs the selected kernel

**\[ Uninstall \]** - uninstalls the selected kernel(1)

**\[ PPA \]** - launches your default https:// handler to the web page for the selected kernel  
If no kernels are selected (when first launching the app before clicking on any) launches the main page listing all the kernels.  
Use this to see the build summary and CHANGES files.

**\[ Uninstall Old \]** - uninstalls all installed kernels below the latest installed version(1)

**\[ Reload \]** - deletes, re-downloads, and re-reads the local cached copies of all the index.html's from the mainline-ppa web site, and regenerates the displayed list.  

**\[ Settings \]** - access the [settings](settings.md) dialog to configure various options

**\[ About \]** - basic info and credits

**\[ Exit \]** - order pizza

(1) The currently running kernel and any locked kernels are protected and ignored.

## Pinning / Locking
The Lock checkboxes serve as both whitelist and blacklist.

A locked kernel will be frozen in whatever state it was in when you locked it.  
If it was installed, it will now stay installed.  
If it was not installed, it will now stay uninstalled.

All forms of install/uninstall commands & methods are affected.  
The gui "Install" and "Uninstall" buttons are inactive on that kernel.  
The cli "--install" and "--uninstall" commands ignore that kernel.  
The gui "Uninstall Old" button and the cli "--uninstall-old" command ignore that kernel.  
The cli "--install-latest" and "--notify" for the background notification ignore that kernel.  
The kernel is still visible, you can still write notes and pull up the PPA info page and toggle the lock to unlock it.

This can be handy to keep a stock distribution kernel from being uninstalled by "Uninstall Old", or prevent a known buggy kernel from being installed by "--install-latest" and prevent "--notify" from generating a notification to install it.

## Notes
Clicking on the Notes field allows to attach arbitrary note text to a kernel.

## Display Sorting
All column headers are clickable to re-sort the list.  
The "Kernel" coulumn sorts by the special kernel version number sort where "1.2.3-rc3" is higher than "1.2.3-rc2", yet "1.2.3" is higher than "1.2.3-rc3".  
Sorting on the "Lock" column is a way to see all locked kernels together.  
Sorting on the "Status" column is a way to see all installed kernels together.  
Sorting on the "Notes" column is a way to see all kernels that have any notes together.  

# Help / FAQ

* [MainlineBuilds WIKI](https://wiki.ubuntu.com/Kernel/MainlineBuilds)

* General debugging  
  The `-v` or `-v #` option, or the environment variable `VERBOSE=#`, enables increasing levels of verbosity.  
  Example: `$ mainline-gtk -v 3` or `$ VERBOSE=3 mainline-gtk`  
  The -v option may also be used alone or repeated. The default with no `-v` is the same as `-v 1`.  
  Each additional `-v` is like adding 1. ie: `-v -v -v` is like `-v 4` or `VERBOSE=4`  
  0 = silence all output  
  1 = normal output (default)  
  2 = same as --debug  
  3 = more verbose  
  4 = even more  
  5+ mostly just for uncommenting things in the code and recompiling, not really useful in the release builds
  
  A few lines of output are printed before the commandline has been parsed, so `-v 0` doesn't silence them.  
  The environment variable is read earlier in the process and can silence all output.  
  `VERBOSE=0 mainline --install-latest --yes`

  The exit value is also meaningful.  
  `VERBOSE=0 ;mainline --install-latest --yes && mainline --uninstall-old --yes`  

* If **Uninstall Old** doesn't remove some distribution kernel packages  
  Use your normal package manager like apt or synaptic to remove the parent meta-package:  
  `$ sudo apt remove linux-image-generic`  
  Then **Uninstall Old** should successfully remove everything.  

* Secure Boot  
  Possibly useful, I have not tried:  
  https://github.com/M-P-P-C/Signing-a-Linux-Kernel-for-Secure-Boot

* Kernel versions 5.15.7+ and libssl3  
  [Install libssl3](../../wiki/Install-libssl3)

* Missing kernels  
  Only viable installable kernels are shown by default. Failed or incomplete builds for your platform/arch are not shown unless the "Hide Invalid" setting is un-selected.  
  If you think the list is missing a kernel, press the "PPA" button to jump to the mainline-ppa web site where the .deb packages come from, and look at the build results for the missing kernel, and you will usually find that it is a failed or incomplete build for your arch (ex: amd64), and can not be installed.

# TODO & WIP
* Replace Process.spawn_async_with_pipes("aria2c ...",...) with libcurl  
* Make the background process for notifications detect when the user logs out of the desktop session and exit itself  
* Move the notification/dbus code from the current shell script into the app and make an "applet" mode  
* Combine the gtk and cli apps into one, or, make the gtk app into a pure front-end for the cli app, either way  
* Replace the commandline parser  
* Toggles to show/hide the rc or invalid kernels in the main ui instead of going to settings  
* Right-click menu for more functions for a given kernel, such as reloading the cache just for a single kernel to check for new build status etc, without adding 18 buttons all over the ui.  
* Properly handle when a kernel has multiple builds like 5.16

# hints  
* cron job to always have the latest kernel installed.  
  If you install this, you should disable the notifications in settings.
```
$ sudo -i
# cat >/etc/cron.d/mainline <<-%EOF
	# Check for new kernels on kernel.ubuntu.com every 4 hours, randomize over ~2.8 hours
	1 */4 * * * root sleep ${RANDOM:0:4} ;VERBOSE=0 ;mainline --install-latest --yes && { mainline --uninstall-old --yes ;for a in /home/*/.Xauthority ;do echo [[ -s $a ]] && DISPLAY=:0.0 XAUTHORITY=$a notify-send -t 0 -a mainline -i mainline "Mainline Kernels" "New kernel installed" ;done ; }
%EOF
```

* external terminal app

`sudo apt install cool-retro-term`  

![settings](settings.jpg)

![cool-retro-term](cool-retro-term.jpg)
