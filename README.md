# ASUS K501U Ubuntu Post Install Set Up

This is the steps I followed to get Ubuntu 16.04 configured to start Java programming after it was installed.

## Getting Wifi to work

After the install, the Wifi did not work. This was fixed by running this command:

```shell
options asus_nb_wmi wapf=1" | sudo tee /etc/modprobe.d/asus_nb_wmi.conf
```

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

