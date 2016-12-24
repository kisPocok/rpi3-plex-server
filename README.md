# Install Plex Media Server on Raspberry PI 3

It's a step-by-step manual. You have to follow the sequence. It takes 1h to complete (upgrades are slow). Our next goal is to install the software necessary for our Pi.

### Upgrade your system

Now we are going to upgrade the distribution, Raspbian, software. To ensure that our operating system is fully up to date. In the terminal type.

```shell
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get update && sudo apt-get dist-upgrade
```

Once completed and the terminal is returned to you enter the next command to install the HTTPS transport package to enable access to HTTPS content over the Internet.

```shell
sudo apt-get install apt-transport-https -y --force-yes
```

Next we shall download a key that will authenticate our downloads to ensure they are safe

```shell
wget -O - https://dev2day.de/pms/dev2day-pms.gpg.key | sudo apt-key add -  
```

With the key downloaded we now add a new repository to our list, this repository is provided by dev2day.de who has worked on the packages that will power our Plex install.

In the terminal type

```shell
echo "deb https://dev2day.de/pms/ jessie main" | sudo tee /etc/apt/sources.list.d/pms.list
```

We now update our list again, to ensure that we now have the latest updates for the Plex Media Server.

```shell
sudo apt-get update
```

Lastly we install the Plex Media Server software.

```shell
sudo apt-get install -t jessie plexmediaserver -y
```

The install will only take a few minutes and once completed the Plex Media Server will automatically start as a background service on your Pi.

At this point it is worth rebooting your Raspberry Pi 3 as proceeding without a reboot caused a bug when trying to configure the Plex server via the browser.

To reboot in the terminal type.

```shell
sudo reboot
```

### Connect to WiFi (optional)

Alternative sollution for plug-in an Ethernet cable.

RPI 3 comes buit-in WiFi adapter. The easiest way to find your home network is to scan it:

```
sudo iwlist wlan0 scan | grep ESSID
```

If you found it you have edit the following file to be able to connect the network.

```shell
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

Add the following text. Obviously change the ssid and psk to match the name of your router and the password you use to get online.

```shell
network={
ssid="SSID"
psk="WIFI PASSWORD"
}
```

Save your work by pressing CTRL + O then press Enter. Next Press CTRL + X to exit the editor.

Disable then enable wifi

```shell
sudo ifdown wlan0
sudo ifup wlan0
```

You can check your IP address by typing

```shell
ifconfig wlan0
```

Your IP address should pop up for the interface wlan0, which is our WIFI card.

The best test to check that this works is to reboot your Raspberry Pi and see if it auto connects to the WIFI. To reboot type.

```shell
sudo reboot
```

### Fix IP address of the Pi

To ensure that we can discover our Pi on a network we will now fix the IP address of the Pi.

First we need to find out the IP address, in the terminal type.

```shell
hostname -I
```

<u>Write down your IP address.</u> Now let's fix the IP address, in the terminal type

```shell
sudo nano /boot/cmdline.txt
```

At the very end of the line we will add the IP address that we earlier wrote down. So add

```shell
ip=YOUR_IP_ADDRESS
```

With the IP entered press CTRL + O, then Enter to save your changes. Now press CTRL + X to close the file.

On the other hand, you should set up fix IP for RPI in your router. Find your RPI's mac address by typing

```
ifconfig
```

There will be more then 0 mac address there, you have to find <u>wlan0</u>'s address. 

Go to your Router's administration panel then find "DHCP Static IP Configuration" or equivalent. Set up your rule (mac address «» ip). It might be necessary to restart your router.

Now reboot your Pi to make the changes permanent.

```shell
sudo reboot
```

With your Pi rebooted we shall now test that we can access the Plex server. On your laptop or desktop PC open a web browser and type your IP address followed by `:32400/web` so for example I typed.

Example: `http://192.168.1.1:32400/web`

### Mount network storage to RPI

Properly Mount USB Storage on Raspberry Pi

http://www.htpcguides.com/properly-mount-usb-storage-raspberry-pi/


Create a mounting directory

```shell
sudo mkdir /mnt/library
```

Mount network storage. Use proper credentials. Storage's IP, username and password.

```shell
sudo mount //192.168.1.2/Data /mnt/library cifs user=REMOTE_USERNAME,pass=REMOTE_PASS,rw,uid=1000,iocharset=utf8,sec=ntlm 0 0
```
or use the new version:
```shell
sudo mount -t cifs -o iocharset=utf8,noperm,file_mode=0666,dir_mode=0777,user=REMOTE_USERNAME,pass=REMOTE_PASS,rw,uid=1000,sec=ntlm //192.168.1.2/Data /mnt/library
```

Restart your RPI again.

```shell
sudo reboot
```

Drive should be mounted to RPI. Boom.

### Configurate Plex

1. Open Plex Server in your browser: http://192.168.1.1:32400/web
2. Log in with your Plex account.
3. Add your mounted library in Plex settings `/mnt/library` 
4. Play movies on your Plex Client. Enjoy.

### Add custom domain

Use local domain name instead of ip. It will be available in your private network only. Follow this guide:

1. Log in to router's administration site.
2. Find "DNS Configuration" or equivalent.
3. Create new rule, by adding a domain name (ie.: mediaplayer) and IP pair (which is obviusly the RPI's address).
4. Do not use dots in the domain (it doesn't work for me)
5. Open http://mediaplayer:32400/web in your browser.

*TODO: redirect 32400 to 80 port.*

**Congratulation, you are done!**



##### Original article, sources, see more:

* https://www.element14.com/community/community/raspberry-pi/raspberrypi_projects/blog/2016/03/11/a-more-powerful-plex-media-server-using-raspberry-pi-3

* http://www.htpcguides.com/properly-mount-usb-storage-raspberry-pi/
