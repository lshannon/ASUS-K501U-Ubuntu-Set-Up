# ASUS K501U Ubuntu Post Install Set Up

This is the steps I followed to get Ubuntu 16.04 configured to start Java programming after it was installed.

## Getting Wifi to work

After the install, the Wifi did not work. This was fixed by running this command:

```shell
options asus_nb_wmi wapf=1" | sudo tee /etc/modprobe.d/asus_nb_wmi.conf

```

## Dealing with Loud Fan

Seems like the fan is always running on this laptop. Currently on a quest to fix it.

At presenting I am trying using tlp:
http://linrunner.de/en/tlp/docs/tlp-linux-advanced-power-management.html

After installing it run:

```shell
tlp start
sudo reboot
```

Some useful tools for diagnosis of fan noise and system state.

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

Also 
```shell
sudo apt install fwts

luke@luke-K501UX:~$ fwts fan
Running 1 tests, results appended to results.log
Test: Simple fan tests.                                                     
  Test fan status.                                        4 passed, 1 failed   
  Load system, check CPU fan status.                                           
luke@luke-K501UX:~$ 

```

Now we can make use of some of these programs following this:
https://asreimer.wordpress.com/2015/07/09/asus-z97-eusb-3-1-motherboard-sensors-and-thermal-control-on-opensuse-13-2/

## Flashing the BIOS

Here is where to get the latest BIOS:
https://www.asus.com/support/Download/3/416/0/2/dFPFCR1MvzCTTcgs/8/

1. Download the BIOS upgrade from the link
2. Put the extracted file on a USB partition, using "FAT" file system not fat32 as stated here. Also create a partition on it to put the BIOS file in
3. Plug in the USB, then turn on the computer, press the F2 key to get into BIOS on boot.
4. From the Manual follow these directions:
- Use the Start Easy Flash function on the Advanced page of the BIOS Setup Utility. Follow the instructions shown.
- Locate the latest BIOS file and start updating (flashing) the BIOS.
- You must Restore Defaults on the Exit page after updating (flashing) the BIOS.

Here is document from ASUS outlining the steps:
https://www.asus.com/support/faq/1008859


## Fixing Vi

Ubuntu comes with a really stripped down version of Vi. Run the following to install something better.

```shell
sudo apt-get install vim-gnome
```

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

### Set Up Git and Maven

```shell
sudo apt-get update
sudo apt-get install git
sudo apt-get install maven
```

Thats it for now. More details to come as I fine tune the system.

