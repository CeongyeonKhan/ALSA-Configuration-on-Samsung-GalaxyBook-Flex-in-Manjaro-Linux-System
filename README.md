# ALSA-Configuration-on-Samsung-GalaxyBook-Flex-in-Manjaro-Linux-System
Realtek ALC298 ALSA Configuration on Samsung GalaxyBook Flex in Manjaro Linux System
# Realtek ALC298 ALSA Configuration on Samsung GalaxyBook Flex in Manjaro Linux System

# Maintained by Ceongyeon_Khan for Laptop Patches

# Audio fix in Linux and other Subsystem

# Created by Ceongyeon Khan 2020-12-28

# works for:\
# Samsung NP930QCG-K01CN\
# Samsung NP930QCG-K02CN\
# Samsung NP930QCG-K58A\
# Other Variants of Samsung GalaxyBook Flex 2020

# Hardware Infomation:
# CPU: Intel (R) Core i7-1065G7 @ 1.30GHz 4C8T
# RAM：16GB LPDDR4X 3733MHz
# iGPU: Intel Iris Plus Graphics ICL GT2
--------------------------------------------------------------------------------------
Pre-Installation
Active Kernel required: 5.8.18.8 and higher (Kernel version 5.4.72 is confirmed failure on this platform)
Release version on this platform:
   OS: Manjaro 20.2 Nibia
   Kernel: x86_64 Linux 5.9.11-3-MANJARO
--------------------------------------------------------------------------------------

1.Install sof-firmware (Sound Open Software)
  >>sudo pamac install sof-firmware

2.Install ALSA Tools
  >>sudo pacman -S alsa-tools

3,Configure description file of audio hardware namespace
  >>sudo mkdir /etc/modprobe.d/
  >>sudo touch /etc/modprobe.d/sof.conf
  >>sudo nano /etc/modprobe.d/sof.conf
  \
# Add following line:
    options snd slots=snd_soc_skl_hda_dsp
# Then saving and exit

4.Make snd-hda-intel in blacklist
  >>sudo touch /etc/modprobe.d/blacklist.conf
  >>sudo nano /etc/modprobe.d/blacklist.conf
  \
  # Add following line:
    snd-hda-intel
  # Then saving and exit

5.Backup previous alsa-base
  >>sudo mv /usr/share/alsa-base.conf /etc/modprobe.d/alsa-base.conf.bak

6.Download snd description file:Samsung_NT930QCG-K0xCN⁄K58A.sh

7.Rename snd description file and set permissions as read,write,executable for all
  >>sudo mkdir /home/$YOURUSERNAME/scripts/
  >>sudo mv ./Downloads/Samsung_NT930QCG-K0xCN⁄K58A.sh /home/$YOURUSERNAME/scripts/T0912.sh
  >>sudo chmod a+rwx /home/$YOURUSERNAME/scripts/T0912.sh

8.Create and edit ionsound.service file
  >>sudo nano /etc/systemd/system/ionsound.service
  \
  # Add following text
    [Unit]
    Description=Custom Sound

    [Service]
    Type=idle
    Execstart=/home/$YOURUSERNAME/scripts/T0912.sh
    
    [Install]
    WantedBy=multi-user.target
  # Then saving and exit

9.Set file permissions to allow everyone to read
  >>sudo chmod a+r /etc/systemd/system/ionsound.service

10.Create and edit ionsoundsleep.service file
  >>sudo nano /etc/systemd/system/ionsoundsleep.service
  \
  # Add following text
    [Unit]
    Description=Custom Sound Sleep Fix

    [Service]
    Type=idle
    ExecStart=/home/$YOURUSERNAME/scripts/T0912.sh

    [Install]
    WantedBy=suspend.target

11.Use Systemctl command to activate new service mentioned above
  >>sudo systemctl daemon-reload
  >>sudo systemctl enable ionsound.service
  >>sudo systemctl enable ionsoundsleep.service

12.Deactivate PulseAudio Service
  >>sudo nano /etc/pulse/default.pa
  \
  # Find the following line:
  load-module module-suspend-on-idle
  # Change it to status be commmented:
  ###load-module module-suspend-on-idle

13.Reboot your machine

14.Launch alsamixer in the terminal(Konsole)and modify volumes:
  >>$ alsamixer
  # Press F6 to change audio hardware device and ensure the sof-hda-dsp is listed then select it
  # Increase the volumes in all columns
  # Ensure the gb Gain (Display at the top left corner) never exceeds above 0.00

15.Click the Speaker icon in the system tray and ensure the speaker is not muted, unmute it if it is

16.Configure System Audio settings:
  # Right click the speaker icon in the systemtray
  # Click Configure Audio Volume
  # Click Audio
  # Click Advanced
  # Ensure the output drop-down has Smart Sound Technology Audio Controller Speaker + Headphone

17.Test each channel of sound

18.Several situations the audio will be unabled when after booting, use following command to solve it
  >>sudo amixer -D pulse sset Master toggle
  >>reboot
