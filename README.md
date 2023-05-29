# Raspberry-Pi 3 model A+ as Ubuntu Server

## Download Ubuntu server:
On a suitable SD card (larger memory prefered, I used 16GB) install the ubuntu server system using raspberry pi imager 
https://projects.raspberrypi.org/en/projects/imager-install
**Important:** Use the (Advanced Options) before writing the system, to setup WIFI network to connect to, and setup ssh connection if you want to use raspberry right from your PC (powershell, CMD or whatever).

## How to connect it with SSH:
After raspbery launch for the first time, try to find it's internal ip, you can make this by command: ```ifconfig``` or by simply see DHCP clinte list in your router.
Now from a PC (on the same network) open powershell and write ```ssh <user name you set in PI imager>@<raspberry ip you found>``` like ```ssh Hasanen@192.168.1.1``` and then just take the authentication poccess to establish the connection.

## Connect to new WIFI network:
You can use nmcli package command (install it first with ```sudo apt install network-manager```)
in my case the best one works is wpa_cli, as below:
```
> wpa_cli wlan0
Interactive mode:
> add_network
x                                     //// x is the connection id as integer
> set_network x ssid "Network ssid"
> set_network x psk "Network password" //// if network has password
> set_network x key_mgmt NONE          //// if network has no password (open network)
> set_network x scan_ssid 1            //// if network is hidden ssid
> enable_network x
> save_config
> select_network x
> quit                                 //// Exit after complete
```
Links: https://unix.stackexchange.com/questions/458406/network-manager-works-but-wlan-is-unavailable-for-nmcli
       https://unix.stackexchange.com/questions/665107/how-to-connect-to-open-wifi-networks-using-wpa-cli
Here we go now you have new network added.
you can go again and run ```list_networks``` then you can connect any of the saved networks with ```select_network <id of the network from the list>``` and that's it.

## Netplan:
Now after all the above you will find your raspberry pi connect to the network you set, but it will not save the networks you add (so that we saw ```FAIL``` after ```save_config``` command), cuz the file was used by wpa_supplicant to save configurations in previous versions is not exist any more :) instead their is a completly different package called **Netplan** (a utility for easily configuring networking on a linux system) 

Now let's set networks using Netplan:
1- run: ```ls /etc/netplan/``` to see what configuration files exist (default you will find "50-cloud-init.yaml" file system created will contain the network we setted up in raspberry pi imager).
2- run: ```cat /etc/netplan/*.yaml``` to see the content of any file with this extention in this folder.
3- That file can't be write or edited (as i readed) so we should disable it by create file called "/etc/cloud/cloud.cfg.d/99-custom-networking.cfg" contain: network: {config: disabled}
   by running: ```sudo nano /etc/cloud/cloud.cfg.d/99-custom-networking.cfg``` content: network: {config: disabled}
4- Now create our configuration yaml such as "/etc/netplan/my-new-config.yaml" and i put this content:
```
#This is a configuration yaml for connecting networks H.A.SAHIB 28/5/2023
network:
    version: 2
    wifis:
        renderer: networkd
        wlan0:
            access-points:
                <wifi network>:
                    password: <network password>
                <hidden open wifi network>: 
                    hidden: true
            dhcp4: true
            optional: true
 ```
**Note:** Becarful with indentations and use spaces not tab, you can find the avilable content of the config file in:
https://netplan.readthedocs.io/en/stable/netplan-yaml/

5- We have build our yaml file, so now lets delete the default old one: ```sudo rm /etc/netplan/50-cloud-init.yaml```.
6- Now run: ``` sudo netplan generate``` then ```sudo netplan apply```.
7- ```sudo reboot``` and congradulations things will stuck in memory and being used every time.

