##ARS-E DAEMON AUTOMATIC REGISTRATION SERVICE EXTENDABLE  

###Running Installations  
[OH2CH - Finland](http://oh2ch.org/trbo/state.php)  
[KM4NNO - USA](http://)
[KM4NNO/XE1 - Mexico](http://)

###Installation Examples / Demos
[W8FSM Rasberry Pi Video](http://youtu.be/j7ItqeQou4k)
[KD8EYF Video](http://youtu.be/85EdiW7mbXQ)  
[KD8EYF Beagle Bone Control Station](http://i.imgur.com/9Uu0T.jpg)  


##2013-04-14 Updates
  Created 'updates' branch with following updates. Will test and merge to master. 
  Thanks to anonymous  

* fix tx msg charset encode
* can send group message
* fix gps negative lat lon, maybe work evrywhere now
* allow configure station symbol for every station
* reverse GeoCode address with "Who callsign" command
* send msgs given in files by other program
* less extra channel loading, do set ars_ping_interval to 0

##11-08-2015 Updates by KM4NNO
# Branch created with:
# * Send SMS.
# * Get weather thru Weather Underground.
# * Send short Email.
# * Ping to know arsed is running.
# * GPIO ports enabled to control peripherals (not tested yet).
# * You can set symbol for each radio.
# * Step by step setup instructions.


##Install Instructions  
Assumes a Clean install of Debian Squeeze / Wheezy

# For installing Raspbian using MAC:
# From the terminal run:

diskutil list
# replace disk1 with the disk(Number assigned to your SD card)
diskutil unmountDisk /dev/disk1
sudo dd bs=1m if='your path/Linux Debian/2015-02-16-raspbian-wheezy.img' of=/dev/rdisk1

# Launch raspi-config from the command line:

sudo raspi-config

# and select:
# 1. Expand the File System

# This will make all unused(free) space on your SD card available for use. If you are using one of the NOOBs images, you will receive a message stating that expansion is not necessary.
# While you are at it, you may wish to set some of the other options such as your regional settings. A reboot, or restart is required once you are finished, and exit.

# Update system and install prereqs

sudo apt-get update  
sudo apt-get install openssh-server git libyaml-tiny-perl libdate-calc-perl libjson-perl  libtest-pod-coverage-perl  

# Assign a static(fixed) IP address:

# Recommended static networking config in /etc/network/interfaces
# Assuming Radio IP of 192.168.10.1 and PC ip of 192.168.10.2

sudo nano /etc/network/interfaces

# (using your keyboard edit the following: change the line that reads..)

iface eth0 inet dhcp

# (so that it now reads...)

#iface eth0 inet dhcp
iface eth0 inet static
    address 192.168.1.64
    netmask 255.255.255.0
    network 192.168.1.0
    broadcast 192.168.1.255
    gateway 192.168.1.254
iface usb0 inet static
    address 192.168.10.2
    netmask 255.255.255.0
    up route add -net 12.0.0.0/8 gw 192.168.10.1
    down route del -net 12.0.0.0/8 gw 192.168.10.1


# Download and install the Ham APRS module

mkdir ~/src  
cd ~/src  
wget http://search.cpan.org/CPAN/authors/id/H/HE/HESSU/Ham-APRS-FAP-1.18.tar.gz  
tar -zxvf ./Ham-APRS-FAP-1.18.tar.gz  
cd Ham-APRS-FAP-1.18  
perl Makefile.PL  
make  
sudo make install 
cd ..  

# Install to access the Google geocoding API:
sudo perl -MCPAN -eshell
install Bundle::LWP

# Install to access the Underground Weather:
sudo cpan Weather::Underground

# Install to access e-mail sending:
sudo cpan -fi Email::Send::SMTP::Gmail
sudo cpan Email::MIME

# Install to access Bulk SMS:
sudo cpan App::cpanminus
sudo cpan LWP::UserAgent
sudo cpan HTTP::Request
sudo cpan LWP::Protocol::https	Errors

# Install to access GPIO ports:
cd ~/src
sudo cpan YAML
wget http://www.airspayce.com/mikem/bcm2835/bcm2835-1.45.tar.gz
tar zxvf bcm2835-1.45.tar.gz
cd bcm2835-1.45
./configure
make
sudo make check
sudo make install
sudo cpan Device::BCM2835
cd ..

# The next file will contain your unique setup, you have to edit the file:
#Edit the config file by hand to include the DMR radio users you want to listen for the mi5 network config is include as an example of what we did in michigan:
cp configs/arsed.mi5.conf /etc/arsed.conf
sudo nano /etc/arsed.conf

# Install the TRBO-NET arsed program

cd ~/src
git clone https://github.com/KD8EYF/TRBO-NET.git
cd ~/src/TRBO-NET/
perl Makefile.PL
make
sudo make install

# Install Apache webserver 

sudo apt-get install apache2 libapache2-mod-php5
sudo cp ~/src/TRBO-NET/web/* /var/www/


# Connect turbo radio to Linux box using USB  
# Device should enumerate and automatically create a network interface. If not check kernel support  



# RUN THE PROGRAM

arsed



# Set arsed to run at startup:

sudo nano /etc/rc.local

#Add the following 2 lines before “exit 0"

# Auto run arsed as pi user.
su - pi -c "arsed &"



###How To Use
# - send text message to gateway radio's ID: it has 'who' command to list registered radios ('w' for short).  
# - send 'aprs <callsign> <message>' to send text message to APRS-IS  

# once radio starts sending positions, they will be sent to the APRS-IS too  !

# View the WebStatus  
# - http:/[IP ADDRESS OF SERVER]/state.php  

##Support
#Please Feel free to email me, i will be more then happy to help get your installation configured.

