---
title: "Ubuntu Tips"
description: "Useful commands and tips for Ubuntu, especially for someone who has a bad memory."
date: 2024-02-01T23:17:36+08:00
image:
math:
license:
hidden: false
comments: true
draft: false
categories:
- Skills
tags:
- Utility
---

# Useful commands

## Ubuntu

##### apt: Advanced Package Tool

```pwsh
sudo apt install build-essential
sudo apt-get update
sudo apt-get upgrade
sudo apt full-upgrade
sudo apt-get install <package>
```

*we need a cleanup sometimes...*

```pwsh
sudo apt-list --installed // list all installed packages
sudo apt-get remove <package>
sudo apt autoremove
sudo apt-get clean
sudo journalctl --vacuum-time=nd // n > 0, remove system logs
```

##### wget: The non-interactive network downloader

```pwsh
wget <url>
```

##### htop: interactive process viewer

```pwsh
free -h // show memory usage in human readable format, built-in
htop // show all processes
```

##### curl: transfer a URL

```pwsh
curl -O <url>
```

##### scp: OpenSSH secure file copy (remote file copy program)

```pwsh
scp [-C] [-i /path/to/your/pem] [-r] <user>@<host>:<folder> <local_folder> // remote to local
scp [-C] [-i /path/to/your/pem] [-r] <local_folder> <user>@<host>:<folder> // local to remote
```

*if file path contains space, use `"' '"`` to wrap it*

```pwsh
scp ... <user>@<host>:"'/path/to/file which contains space'"
```

*if network broken, add `-C` to resume transfer*

*use pem file to login, add `-i /path/to/your/pem`*

*transfer a folder, add `-r`*

*if network isn't stable, use `rsync` instead.
`rsync` is a fast and extraordinarily versatile file copying tool.learn when needed.*

##### tar: The GNU version of the tar archiving utility

```pwsh
tar -xvf <file>
tar -cvf <file> <dir>
```

##### zip: package and compress (archive) files into zip file

```pwsh
zip <file> <dir>
```

##### unzip: list, test and extract compressed files in a ZIP archive

```pwsh
unzip <file>
```

##### permission denied when running a script

```pwsh
ls -l [file] // check permission
chmod +x ./[file] // add execute permission
```

#### remote server management

##### RAM less than it should be

cloud server providers usually apply some RAM into kdump, which is used for debugging and error logging. we can disable
it to get more RAM.
in ubuntu system, we can disable it by:

```pwsh
sudo vim /etc/default/grub
```

we could see a bunch of lines,change the following lines value into 0:

```pwsh
GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_CMDLINE_LINUX=""
```

then run:

```pwsh
sudo update-grub
sudo reboot
```

relogin and check RAM again.it should be fine now.

##### Screen: screen manager with VT100/ANSI terminal emulation

Normally, when we run a script in a remote server, we need to keep the terminal open. If we close the connection, and
login again,we won't find the running script even using fg command. Screen can help us solve this problem.

Screen is a full-screen window manager that multiplexes a physical terminal between several processes, typically
interactive shells.

```pwsh
screen -S <name> // create a screen
screen -ls // list all screens
screen -r <name> // resume a screen
screen -d <name> // detach a screen
```

*if we want to run a script in a screen, we can run `screen -S <name> ./<script>`*
