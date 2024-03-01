---
title: "Ubuntu Tips"
description: "Useful commands and tips for Ubuntu, especially for someone who has a bad memory."
date: 2024-02-01T23:17:36+08:00
hidden: false
comments: true
draft: false
categories:
- Skills
tags:
- Utility
---

# Overview of the Linux system

root directory: `/`

| directory                                                                  | description                                                                   |
|----------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| `/bin`                                                                     | essential command binaries or programs                                        |
| `/boot`                                                                    | static files of the boot loader                                               |
| `/dev`                                                                     | device files (not the shorthand of `development`)                             |
| `/etc`                                                                     | host-specific system-wide editable text configuration files (not the `e.tc.`) |
| `/home`                                                                    | home directories for users                                                    |
| `/root`                                                                    | home directory for the root user                                              |
| `/lib`                                                                     | shared libraries dependencies                                                 |
| `/var`                                                                     | variable data files                                                           |
| `/tmp`                                                                     | temporary files                                                               |
| `/usr`                                                                     | user related programs and data                                                |
| `/proc`                                                                    | process information pseudo-filesystem                                         |
| `/sys`                                                                     | sysfs is a virtual file system provided by the Linux kernel                   |
| `/mnt`                                                                     | temporarily mounted filesystems                                               |
| ...                                                                        | ...                                                                           | 

# Useful commands

## Ubuntu

#### list

```Shell
ls // list files and directories
ls -l // list files and directories with details
ls -a // list all files and directories
ls -lh // list files and directories with human readable format
ls -lt // list files and directories by time
touch [file] // create a file
```

#### show file content

```Shell
cat [file] // show file content
less [file] // show file content with page
more [file] // show file content with page, only support `down` 
head -n [file] // show the first n lines of file
tail -n [file] // show the last n lines of file
```

#### edit file

```Shell
cat [file 1] > [file 2] // copy file 1 to file 2
cat [file 1] >> [file 2] // append file 1 to file 2
cat [file 1] [file 2] > [file 3] // combine file 1 and file 2 to file 3
echo "content" > [file] // write content to file
echo "content" >> [file] // append content to file
# the `>` `>>` applies to many commands, e.g., `ls -l > [file]`, which will write the output of `ls -l` to file
```

#### permission denied when running a script

```pwsh
ls -l [file] // check permission
chmod +x ./[file] // add execute permission
```

#### apt: Advanced Package Tool

*a word: I don't think it's advanced... :P*

```pwsh
sudo apt install build-essential
sudo apt update
sudo apt upgrade
sudo apt full-upgrade
sudo apt install <package>
```

*we need a cleanup sometimes...*

```pwsh
sudo apt list --installed // list all installed packages
sudo apt remove <package>
sudo apt autoremove
sudo apt autoclean
sudo apt autopurge
sudo journalctl --vacuum-time=nd // n > 0, remove system logs
aptitude // interactive package manager
```
##### purge removed packages

```pwsh
dpkg -l | grep '^rc' | awk '{print $2}' // list removed packages
dpkg -l | grep '^rc' | awk '{print $2}' | xargs sudo dpkg --purge // purge removed packages
```

##### restart service (usually after modifying configuration file)

```pwsh
source ~/.bashrc // apply changes in .bashrc
sudo systemctl restart [service]
```

##### when having multiple versions of a package

for example, we have multiple versions of `gcc` installed, and we want to switch between them.

```pwsh
sudo update-alternatives --config gcc // list all versions of gcc
sudo update-alternatives --remove-all gcc // remove all versions of gcc(only remove the link, not the package)
```

set gcc-13 as default:

```pwsh
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-12 20
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-13 30
```

##### Ubuntu VM occupied too much space dispite not having much data in it

Windows automatically allocates a certain amount of space for the VM, that means even if you clean up the VM, the space won't be released. To solve this problem, we need to compact the VM.

Launch PowerShell as administrator, and run the following commands: 

```pwsh
cd "%userprofile%\AppData\Local\Packages\CanonicalGroupLimited.<Distro>\LocalState\" // go to the VM folder
wsl --shutdown // shut down the VM
optimize-vhd -Path .\ext4.vhdx -Mode full // compact the VM
```

check the space again, it should be fine now.

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

### add ssh key to remote server

```pwsh
scp [-C] [-i /path/to/your/pem] [-r] <user>@<host>:<folder> <local_folder> // remote to local
scp [-C] [-i /path/to/your/pem] [-r] <local_folder> <user>@<host>:<folder> // local to remote
```

*if file path contains space, use `"' '"`` to wrap it*

```pwsh
scp ... <user>@<host>:"'/path/to/file which contains space'"
```

if network broken, add `-C` to resume transfer.

if you use pem file to log in, add `-i /path/to/your/pem`.

if transfer a folder, add `-r`.

if network isn't stable, use `rsync` instead.
`rsync` is a fast and extraordinarily versatile file copying tool. 

learn when needed.

##### tar: The GNU version of the tar archiving utility

```pwsh
tar -xvf [file]
tar -cvf [file] [dir]
```

##### zip: package and compress (archive) files into zip file

```pwsh
zip [file] [dir]
```

##### unzip: list, test and extract compressed files in a ZIP archive

```pwsh
unzip [file]
```

#### remote server management

##### RAM less than it should be

cloud server providers usually apply some RAM into `kdump`, which is used for debugging and error logging. we can disable
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

re-login and check RAM again.it should be fine now.

##### Screen: screen manager with VT100/ANSI terminal emulation

Normally, when we run a script in a remote server, we need to keep the terminal open. If we close the connection, and
login again,we won't find the running script even using fg command. Screen can help us solve this problem.

Screen is a full-screen window manager that multiplexes a physical terminal between several processes, typically
interactive shells.

```pwsh
screen -S [name] // create a screen
screen -ls // list all screens
screen -r [name] // resume a screen
screen -d [name] // detach a screen
```
can also use <kbd>CTRL</kbd> <kbd>A</kbd> chords to <kbd>D</kbd> to detach a screen.
to scroll in a screen, use <kbd>Ctrl</kbd> + <kbd>A</kbd> chords to <kbd>[</kbd> to enter copy mode, then use <kbd>CTRL</kbd> <kbd>F</kbd>,<kbd>CTRL</kbd> <kbd>B</kbd> to scroll front and back, <kbd>q</kbd> to quit(abort the copy mode).

to run a script in a screen, just `screen -S <name> ./<script>`.

##### install llvm-clang from source

```pwsh
$ git clone --depth 1 --branch llvmorg-12.0.1 https://github.com/llvm/llvm-project.git
$ cmake -S llvm-project/llvm -B llvm-project/build \
        -DCMAKE_BUILD_TYPE=Release \
        -DLLVM_ENABLE_PROJECTS=all \
        -DCMAKE_CXX_COMPILER=clang++ \
        -DCMAKE_C_COMPILER=clang
$ cmake --build llvm-project/build -j8
$ cmake --install llvm-project/build --prefix /usr/local  # or somewhere else
```