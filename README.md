# ASUS K501U Ubuntu Post Install Set Up

This is the steps I followed to get Ubuntu 16.04 configured to start Java programming after it was installed. I am happy to say, that after taking the actions below I seem to have a pretty decent little machine.

_NOTE_

At several points in this process I ended up in a place where, after a reboot, when I tried to log in it would accept my password but then not take me to the Desktop. Instead it would make a 'beep' like an error would happen and prompt for the password again.

For me to fix this I pressed Ctrl + F1 to get to the terminal. I then logged into the terminal and ran the following:

```shell
sudo apt-get purge nvidia*
sudo reboot
```

This got me back logging back in at least once. In another case I was not using Nvidia, so this did not work. I ended up redoing the install. This journey was not without its frustrations.

## Flashing the BIOS

I had to deal with a loud CPU fan that seemed to never shut off (see below). One way to attempt to fix this is I flashed the Bios.

Here is where to get the latest BIOS:
https://www.asus.com/support/Download/3/416/0/2/dFPFCR1MvzCTTcgs/8/

To figure out what version Bios you have, press F12 in the Bios. The version will appear in the lower right

Here are the steps I did to successfully Flash the Bios.

1. Download the BIOS upgrade from the link
2. Take a USB and format it with FAT (not FAT32) and give it only 1 partition
2. I read larger USB will not work, I personally used an old 512 MB for this
4. Put the extracted file on a USB
5. Plug in the USB, then turn on the computer, press the F2 key to get into BIOS on start up
6. Set primary Boot device to be this drive
7. Press F10 to Save and Exit
8. The system will just got straight into the Bios again
9. Use the Start Easy Flash function on the Advanced page of the BIOS Setup Utility. Follow the instructions shown
10. Locate the latest BIOS file from the USB and start updating (flashing) the BIOS.
11. You must Restore Defaults on the Exit page after updating (flashing) the BIOS.

Here is document from ASUS outlining the steps:
https://www.asus.com/support/faq/1008859

## Getting Wifi to work

After the install, I was not able to Enable the Wifi card. This was fixed by running this command:

```shell
options asus_nb_wmi wapf=1" | sudo tee /etc/modprobe.d/asus_nb_wmi.conf
```

## Dealing with Loud Fan

Seems like the fan is always running on this laptop. Flashing the Bios really helped. Here are some other things I did.

I installed tlp:

http://linrunner.de/en/tlp/docs/tlp-linux-advanced-power-management.html

After installing it ran:

```shell
tlp start
sudo reboot
```
I did have to enable insecure boot for this to work.

I did find some useful tools for diagnosis of fan noise and system state.

Here is a good way to see fan speeds and temperatures.

```shell
sudo apt install lm-sensors

luke@luke-K501UX:~$ sensors
acpitz-virtual-0
Adapter: Virtual device
temp1:        +40.0°C  (crit = +103.0°C)

coretemp-isa-0000
Adapter: ISA adapter
Physical id 0:  +43.0°C  (high = +100.0°C, crit = +100.0°C)
Core 0:         +43.0°C  (high = +100.0°C, crit = +100.0°C)
Core 1:         +41.0°C  (high = +100.0°C, crit = +100.0°C)

asus-isa-0000
Adapter: ISA adapter
cpu_fan:     4100 RPM
temp1:        +40.0°C  

```

To control fans, the following can be installed

```shell
sudo apt-get install fancontrol
```

The following Post explains how fancontrol can be used to put the fan on a more manual control and specify the ranges it operates. I have not gone this far at present, the Bios flash seemed to have helped as it now runs intermitently. However, should it start to run more frequently, I will be implementing this approach

http://askubuntu.com/questions/22108/how-to-control-fan-speed

## Fixing CPU Low Frequency

If dealing with low freqeuncy (ie: 200 MHz !!!). A potential fix is, based Linux mylogin-K501UB 4.4.0-34-generic #53-Ubuntu x86_64 x86_64 x86_64 GNU/Linux:

1. Disable intel_pstate driver on boot

Edit ""/etc/default/grub" file.

```shell
sudo vim /etc/default/grub
```
Change GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" to GRUB_CMDLINE_LINUX_DEFAULT="intel_pstate=disable quiet splash" 

Then:

```shell
sudo update-grub
```
After reboot change governor to performance

```shell
sudo sudo cpupower frequency-set -g performance
```
Specific frequency may be set by:

```shell
cpupower frequency-set -d 2300000 -u 2300000
```

Related link https://wiki.archlinux.org/index.php/CPU_frequency_scaling


## Nvidia Card Configuration

At first I found the text fuzzy, so I installed Nvidia drivers and then tweaked the settings. But this resulted in getting looked out the desk top (see first note). For now I going to see if I can make do with it.

However, should someone wish to try the drivers:

```shell
sudo apt-get install gksu
```

Then:
```shell
gksudo nvidia-settings
```

This will pull up a UI to fine tune the Graphic Card setting. Note: To run this you will need to use the NVidia drivers (3rd party on Linux).

Here is a tutorial with more information on that:
https://sites.google.com/site/easylinuxtipsproject/display

## Fixing Vi

Ubuntu comes with a really stripped down version of Vi. Run the following to install something better.

```shell
sudo apt-get install vim-gnome
```

Whats cool is, if vi is typed in after running this command, its the full version. Not to mention the system now has Vim.

## Installing Java

I work a fair amount with enterprises in Canada. To stay consistent with their environments I like to use the Oracle JDK. I downloaded it from here:
http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

### Configuring to use Java
Add a script (I usually call mine java.sh) like the following to /etc/profile.d

```shell
#!/bin/bash
export JAVA_HOME=/media/luke/data/tools/jdk1.8.0_91
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib
```

### Download STS

So STS is my fav IDE. And here is where to get it:

wget dist.springsource.com/release/STS/3.7.3.RELEASE/dist/e4.6/spring-tool-suite-3.7.3.RELEASE-e4.6-linux-gtk-x86_64.tar.gz

### Setting Up Postgres

This is a helpful link:

https://help.ubuntu.com/lts/serverguide/postgresql.html

### Set Up Git and Maven

```shell
sudo apt-get update
sudo apt-get install git
sudo apt-get install maven
```

To get git working properly I needed to execute:

```shell
git config --global --edit
```
Then remove the helper section, which included a reference to the osxkeychain. I think this happened when I followed Mac instructions accidentally. This is the correct was to cache the password in Linux:

https://help.github.com/articles/caching-your-github-password-in-git/#platform-linux

To get going with Github, this is a good tutorial:
http://lifehacker.com/5983680/how-the-heck-do-i-use-github



### Download Atom

Atom (the editor not the DC hero) is great for editing text with some awesome packages.

```shell
wget https://atom.io/download/deb
```

### Adding Compiz

On my Mac I love using Shiftit to position windows. It seems Compiz has a similar concept.

```shell
sudo apt-get install compizconfig-settings-manager compiz-plugins-extra
```
Here is a little tutorial on how to use some of the features:
http://www.howtogeek.com/167674/how-to-enable-and-tweak-ubuntus-flashy-graphical-effects/

I hope this does not blow up my Desktop (ie: unable to log in). Although, so far its worth it. With a few clicks I can position a terminal session, my code and a browser running it to all evenly fill up the screen so all can be viewed at once. Very see. Time will tell on this...

Thats it for now. More details to come as I fine tune the system.

