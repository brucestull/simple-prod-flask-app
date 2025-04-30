# Install and Run WSL

1. List available WSL distributions

```ps
PS C:\Users\FlynntKnapp\Programming> wsl --list --online
The following is a list of valid distributions that can be installed.
Install using 'wsl.exe --install <Distro>'.

NAME                            FRIENDLY NAME
AlmaLinux-8                     AlmaLinux OS 8
AlmaLinux-9                     AlmaLinux OS 9
AlmaLinux-Kitten-10             AlmaLinux OS Kitten 10
Debian                          Debian GNU/Linux
FedoraLinux-42                  Fedora Linux 42
SUSE-Linux-Enterprise-15-SP5    SUSE Linux Enterprise 15 SP5
SUSE-Linux-Enterprise-15-SP6    SUSE Linux Enterprise 15 SP6
Ubuntu                          Ubuntu
Ubuntu-24.04                    Ubuntu 24.04 LTS
archlinux                       Arch Linux
kali-linux                      Kali Linux Rolling
openSUSE-Tumbleweed             openSUSE Tumbleweed
openSUSE-Leap-15.6              openSUSE Leap 15.6
Ubuntu-18.04                    Ubuntu 18.04 LTS
Ubuntu-20.04                    Ubuntu 20.04 LTS
Ubuntu-22.04                    Ubuntu 22.04 LTS
OracleLinux_7_9                 Oracle Linux 7.9
OracleLinux_8_7                 Oracle Linux 8.7
OracleLinux_9_1                 Oracle Linux 9.1
PS C:\Users\FlynntKnapp\Programming>
```

2. Install `Ubuntu` distribution

```ps
PS C:\Users\FlynntKnapp\Programming> wsl --install -d Ubuntu
Downloading: Ubuntu
Installing: Ubuntu
Distribution successfully installed. It can be launched via 'wsl.exe -d Ubuntu'
PS C:\Users\FlynntKnapp\Programming>
```

3. Verify Ubuntu is installed

```ps
PS C:\Users\FlynntKnapp\Programming> wsl --list --verbose
  NAME                 STATE           VERSION
* docker-desktop       Running         2
  NewSimpleFlaskApp    Stopped         2
  Ubuntu               Stopped         2
PS C:\Users\FlynntKnapp\Programming>
```

4. Run WSL Ubuntu

```ps
PS C:\Users\FlynntKnapp\Programming> wsl.exe -d Ubuntu
Provisioning the new WSL instance Ubuntu
This might take a while...
Create a default Unix user account: flynntknapp
New password:
Retype new password:
passwd: password updated successfully
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 5.15.167.4-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Wed Apr 30 17:52:26 EDT 2025

  System load:  0.39                Processes:             32
  Usage of /:   0.1% of 1006.85GB   Users logged in:       0
  Memory usage: 4%                  IPv4 address for eth0: 172.21.189.255
  Swap usage:   0%


This message is shown once a day. To disable it please create the
/home/flynntknapp/.hushlogin file.
flynntknapp@DELL-DESKTOP:/mnt/c/Users/FlynntKnapp/Programming$
```
