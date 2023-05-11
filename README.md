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
>wpa_cli wlan0
Interactive mode:
>add_network
x                                     //// x is the connection id as integer
>set_network x ssid "Network ssid"
>set_network x psk "Network password" //// if network has password
>set_network 0 key_mgmt NONE          //// if network has no password (open network)
>set_network x scan_ssid 1            //// if network is hidden ssid
>enable_network x
>save_config
>select_network x
>quit                                 //// Exit after complete

Here we go now you have new network added.
you can go again and run ```list_networks``` then you can connect any of the saved networks with ```select_network <id of the network from the list>``` and that's it.

