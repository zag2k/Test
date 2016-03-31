# RPiWebCam

#### Prep micro SD card (16GB) with latest version of Raspbian Debian Wheezy ####
- Download ZIP image from http://www.raspberrypi.org/downloads/
  - http://raspbian.org/
  - http://downloads.raspberrypi.org/raspbian/release_notes.txt
- Extract the image from the ZIP
- Write image to the SD card using Win32DiskImager
  - http://www.raspberrypi.org/documentation/installation/installing-images/README.md
  - http://www.raspberrypi.org/documentation/installation/installing-images/windows.md

#### Power Up for First Time ####
- Insert SD card, hook up to power, ethernet, monitor, keyboard/mouse for initial setup and to get on network. Can remote into from then on.
- Power up

#### Expand SD Card ####
- On first boot, it boots into raspi-config, where there is an option for this

#### Update Time Zone ####
- Also update time zone in raspi-config (Internationalisation Options)

#### Update Keyboard Layout ####
- in raspi-config (Internationalisation Options)
- Change language to English (US)

#### Reboot ####
- Close out of raspi-config and reboot

#### Change Default Administrator Password ####
- http://www.raspberrypi.org/documentation/linux/usage/users.md
  - passwd
  - tsRP1WebCam15

#### Update hostname ####
(so that it shows up in dhcp client table of router)
http://www.howtogeek.com/167195/how-to-change-your-raspberry-pi-or-other-linux-devices-hostname/
```
sudo nano /etc/hosts
```
  - Replace "raspberrypi" in last line with "RPiWebCamLV"
```
sudo nano /etc/hostname
```
  - Replace "raspberrypi" with "RPiWebCamLV"
```
sudo /etc/init.d/hostname.sh
```
  - To commit the changes
```
sudo reboot
```	
*When logging in, need to remember to change the domain to RPiWebCamLV*

#### Configure Static IP Address ####
http://www.modmypi.com/blog/tutorial-how-to-give-your-raspberry-pi-a-static-ip-address
```
sudo nano /etc/network/interfaces
```	

> auto lo<br>
> iface lo inet loopback<br>
> iface eth0 inet static<br>
> address 192.168.1.81<br>
> netmask 255.255.255.0<br>
> network 192.168.1.0<br>
> broadcast 192.168.1.255<br>
> gateway 192.168.1.1<br>
> <br>
> allow-hotplug wlan0<br>
> iface wlan0 inet manunal<br>
> wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf<br>
> iface default inet dhcp

```
sudo reboot
```
```
sudo ifconfig
```
- (to verify)

Make note of IP Address to be able to run headless:
192.168.1.82

#### (To use DHCP - like when setting up the first time on a new network) ####
```
sudo nano /etc/network/interfaces
```

> auto lo<br>
> <br>
> iface lo inet loopback<br>
> 
> iface eth0 inet dhcp<br>
> <br>
> allow-hotplug wlan0<br>
> iface wlan0 inet manunal<br>
> wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf<br>
> iface default inet dhcp

Check DHCP client table on router to find out what IP was assigned

#### (Install/Configure Wifi Adapter if Applicable) ####
```
sudo nano /etc/network/interfaces
```

**NothingButNet**
> auto lo<br>
> <br>
> iface lo inet loopback<br>
> iface eth0 inet dhcp<br>
> <br>
> allow-hotplug wlan0<br>
> auto wlan0<br>
> iface wlan0 inet dhcp<br>
>       wpa-scan-ssid 1<br>
>       wpa-ap-scan 1<br>
>       wpa-key-mgmt WPA-PSK<br>
>       wpa-proto RSN WPA<br>
>       wpa-pairwise CCMP TKIP<br>
>       wpa-group CCMP TKIP<br>
>       wpa-ssid "NothingButNet"<br>
>       wpa-psk 7a6640a72a4b76d6168c0f8c7f690755dda93d6556dc809be9bf3ff82d6375c6<br>
> <br>
> iface default inet dhcp
	
**NothingButLakeNet**
> auto lo<br>
> <br>
> iface lo inet loopback<br>
> iface eth0 inet dhcp<br>
> <br>
> allow-hotplug wlan0<br>
> auto wlan0<br>
> iface wlan0 inet dhcp<br>
>       wpa-scan-ssid 1<br>
>       wpa-ap-scan 1<br>
>       wpa-key-mgmt WPA-PSK<br>
>       wpa-proto RSN WPA<br>
>       wpa-pairwise CCMP TKIP<br>
>       wpa-group CCMP TKIP<br>
>       wpa-ssid "NothingButLakeNet"<br>
>       wpa-psk 78e11a31c30a8d30e1f6a6698e3a9cf637094888a91d0c2219b77015105b69fd<br>
> <br>
> iface default inet dhcp

#### Update Raspbian ####
```
sudo apt-get update
sudo apt-get upgrade
```

#### (Optional? Not really needed?) Enable remote desktop ####
http://www.raspberrypiblog.com/2012/10/how-to-setup-remote-desktop-from.html
```
sudo apt-get install xrdp
```

#### Shutdown and Prepare to Run Headless ####
```
sudo shutdown -h now
```
Remove monitor, keyboard/mouse

Test remote desktop
Test ssh connection

#### Install Camera Module ####
http://www.raspberrypi.org/wp-content/uploads/2013/07/RaspiCam-Documentation.pdf
	
Shutdown and install camera to board
Boot up
Enable camera support
```
sudo raspi-config
```
- Enable camera support

Reboot

Disable camera led
- http://www.raspberrypi-spy.co.uk/2013/05/how-to-disable-the-red-led-on-the-pi-camera-module/
Add the following line to the config.txt (at the bottom)
```
disable_camera_led=1
```
```
sudo nano /boot/config.txt
```
Reboot
```
sudo reboot
```

#### Install samba to be able to transfer files across the network ####
- http://raspberrypihq.com/how-to-share-a-folder-with-a-windows-computer-from-a-raspberry-pi/
```
sudo apt-get install samba samba-common-bin
```
- Create folder to share (see next step for creating project folder)

#### Create a dedicated folder for the project ####
- Typically in your home folder (/home/pi/RPiWebCam)
- Make writeable
```
mkdir RPiWebCam
chmod 777 RPiWebCam
```
- Share it
```
update /etc/samba/smb.conf
```		
- Add the following to the bottom:
> [RPiWebCam]<br>
> comment=RPiWebCam<br>
> path=/home/pi/RPiWebCam<br>
> browseable=Yes<br>
> writeable=Yes<br>
> only guest=no<br>
> create mask=0777<br>
> directory mask=0777<br>
> public=no<br>

- To let samba know that the user "pi" is a network user, run
```
sudo smbpasswd -a pi
```			
- Enter pi's password twice
(remember to change this if ever change password in the future)

- Test the share
```
\\192.168.1.82\RPiWebCam
```		
- Login with:
```
Raspberrypi\pi
<pi's password>
```

#### Test camera ####
```
raspistill -o ~/RPiWebCam/test.jpg
```
- View image at 
```
\\192.168.1.82\RPiWebCam
```
- If image is upside down because of camera orientation
```
raspistill -vf -hf -o ~/share/test2.jpg
raspistill -vf -hf -o /home/pi/RPiWebCam/Test/test.jpg
```	
- Raspistill documentation:
http://www.raspberrypi.org/documentation/usage/camera/raspicam/raspistill.md

#### Install PyEphem for calculating sunrise/sunset ####
http://rhodesmill.org/pyephem/
```
sudo apt-get install python-dev
sudo apt-get install python-pip
sudo pip install pyephem
```

#### Install GPAC in order to use MP4Box to wrap H264 video data in an MP4 container file ####
http://gpac.wp.mines-telecom.fr/mp4box/
```
sudo apt-get install -y gpac
```

#### Install mencoder for generating timelapse videos ####
http://www.mplayerhq.hu/DOCS/man/en/mplayer.1.txt
```
sudo apt-get install mencoder
```

#### Copy camera scripts and folders to /home/pi/RPiWebCam folder ####

#### Update config.txt for this camera ####

Depending on orientation of camera, set the following values in config.txt
```
default_vflip = True
default_hflip = True
```

#### Install Google Drive Uploader ####
https://developers.google.com/drive/web/quickstart/quickstart-python
```
sudo pip install --upgrade google-api-python-client
```	
- Test:
```
python /home/pi/RPiWebCam/Firmware/gdrive_uploader.py
```

#### Install ImageMagick ####
http://raspi.tv/2014/overlaying-text-and-graphics-on-a-photo-and-tweeting-it-pt-5-twitter-app-series

```
sudo apt-get update (update package list)
sudo apt-get install imagemagick (install program)
````
Test:

#### Set up cron jobs ####
```
crontab -e
```	
- See crontab.txt in project folder for lines that need to be added

#### Set up SmartThings Integration ####
https://github.com/nicholaswilde/smartthings/tree/master/device-types/raspberry-pi
https://code.google.com/p/webiopi/wiki/INSTALL?tm=6
<br>
**Install WebIOPi**
- Install psutil
```
sudo pip install psutil
```		
```
$ cd /home/pi/RPiWebCam/WebIOPi-0.7.1
```		
By default, WebIOPi installs itself using Python 3. Unfortunately Python 3 is not widely adopted and many developers prefer to work in Python 2.7. Installing WebIOPi using Python 2.7 is easy, we just have to remove " python3" from the fourth line setup.sh:
```
$ sudo ./setup.sh
```		
```
$ sudo service webiopi restart
```		
- Reboot your Raspberry Pi.
'''
$ sudo reboot
'''
Make sure  raspberrypi.py is in /RPIWebCam/Firmware/
- Add the script to the WebIOPi configuration file. See this for reference.
```
$ sudo nano /etc/webiopi/config
```
> ...<br>
> [SCRIPTS]<br>
> RPiWebCam = /home/pi/RPIWebCam/Firmware/raspberrypi.py<br>
> ...<br>
		
- Save the configuration file and restart the  webiopi  service
```
$ sudo service webiopi restart
```
- To setup your system to start webiopi at boot:
```
$ sudo update-rc.d webiopi defaults
```

#### Change WebIOPi password ####
```
sudo webiopi-passwd
````	
Login: webiopi
Password: tsRP1WebCam15


#### Install ST device type/device ####
Use camera's ip address and webiopi login above


#### Clean up test files used during setup ####

----

## Common commands ##
Reboot
```
sudo reboot
```	
Shutdown
```
sudo shutdown -h now
```	
Get Ip address
```
hostname -I
```
Determine free disk space:
```
df -h
```
To list processes
```
ps -ef
```
To kill a process
```
sudo killall motion
```
To start/stop webiopi
```
$ sudo /etc/init.d/webiopi start
$ sudo /etc/init.d/webiopi stop
```
To manually run webiopi from console to debug something:
```
sudo webiopi -d -c /etc/webiopi/config
```
