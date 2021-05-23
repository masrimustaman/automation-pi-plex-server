# To enable ssh headless
- For headless setup, SSH can be enabled by placing a file named ssh, without any extension, onto the boot partition of the SD card from another computer. When the Pi boots, it looks for the ssh file. If it is found, SSH is enabled and the file is deleted
- Default password for pi is raspberry

# To enable VNC, connect to wifi, configure locale
```
sudo raspi-config
```
#  Set a Raspberry Pi with a static ip address
### check whether DHCPCD is already activated using the following command
```
sudo service dhcpcd status
```
### In case it’s not, activate DHCPCD as follows:
```
sudo service dhcpcd start
sudo systemctl enable dhcpcd
```

### For the editing of the activated DHCPCDs
```
sudo vim /etc/dhcpcd.conf

# add as per below (depending on your network configuration)
# interface wlan0
# static ip_address=192.168.0.4/24
# static routers=192.168.0.1
# static domain_name_servers=192.168.0.1
```


# To install Plex Media Server

### Pre Req install the “apt-transport-https” package
```
sudo apt-get install apt-transport-https
```

### Download and add the key to the package manager
```
curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
```

### Add the official plex repository to the sources list
```
echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
```

### Installing Plex to your Raspberry Pi

```
sudo apt-get update
sudo apt install plexmediaserver
# The first is that it creates a user and group for Plex to run under. This user and group is called “plex“. As Plex is running a different user to the Raspberry Pi’s default “pi” user, you will need to make sure you have permissions set correctly on your drive.
```
### To access Plex Server
```
http://ipaddress:32400/manage
```
# Install Samba

### install Samba using the following command
```
sudo apt-get install samba samba-common-bin
```

### configure Samba to share the Raspberry Pi directories with another computer within the network
```
sudo vim /etc/samba/smb.conf
```
sample 
```
[global]
netbios name = Pi
server string = The Pi File Center
workgroup = WORKGROUP

[HOMEPI]
path = /home/pi/share
comment = No comment
writeable=Yes
create mask=0777
directory mask=0777
public=no
# public: If set to no, it will only allow valid users to access the shared folder
```

### Add Pi as a Samba user
```
sudo smbpasswd -a pi
```

### restart the Samba service using the following command
```
sudo service smbd restart
```

# Install Transmission

### install the transmission torrent daemon to the Raspberry Pi
```
sudo apt install transmission-daemon
```

### As the Raspberry Pi Transmission software is automatically started, we will need to stop the service temporarily by running the command below
```
sudo systemctl stop transmission-daemon
```
### give the “pi” user access to the folder created 
```
sudo chown -R pi:pi /home/pi/share/torrent_completed
```

### Make changes to its config file.
```
sudo vim /etc/transmission-daemon/settings.json

# "download-dir": "/home/pi/share/torrent_completed",
# "rpc-whitelist": "192.168.*.*",
```

### edit the Transmission daemon startup script so that it uses the “pi” user instead of the default “debian-transmission
```
sudo vim /etc/init.d/transmission-daemon

# need to edit the “USER=” line, so that the Transmission daemon will be run by the “pi” user and not the “debian-transmission

```

### change the user from “debian-transmission” to “pi” in the service file

```
sudo nano /etc/systemd/system/multi-user.target.wants/transmission-daemon.service
# user=pi
```

### tell the service manager to reload all service configuration files
```
sudo systemctl daemon-reload
```

### take ownership of the “/etc/transmission-daemon” folder
```
sudo chown -R pi:pi /etc/transmission-daemon
```

### create a directory where the transmission-daemon will access the “setting.json” file
```
sudo mkdir -p /home/pi/.config/transmission-daemon/
sudo ln -s /etc/transmission-daemon/settings.json /home/pi/.config/transmission-daemon/
sudo chown -R pi:pi /home/pi/.config/transmission-daemon/

```

### restart the transmission-daemon
```
sudo systemctl start transmission-daemon
```

### Access the web interface
```
http://192.168.1.151:9091/transmission
```

### Torubleshooting
###### Stop transmission daemon
```
sudo service transmission-daemon stop
```

###### Add pi to debian-transmission group
```
sudo usermod -a -G debian-transmission pi 
```

###### changing the daemon-user
```
sudo nano /etc/init.d/transmission-daemon
# Change USER to pi
```

###### Change the rights of the configuration files folder
```
sudo chown -R pi /var/lib/transmission-daemon/info/
sudo chmod 755 /var/lib/transmission-daemon/info/settings.json 
```

###### Set the correct permissions for the download / incomplete folders
```
sudo chown -R pi /somewhere/downloads
sudo chown -R pi /somewhere/incomplete
```

###### change the file creation mask
```
sudo nano /etc/transmission-daemon/settings.json
# and change "umask": 18 to "umask": 2
```

###### Start transmission daemon
```
sudo service transmission-daemon start

```



# Backup and Restore

```
# In linux to backup
sudo dd bs=4M if=/dev/sdb of=PiOS.img

# In linux to restore
sudo dd bs=4M if=PiOS.img of=/dev/sdb

# In linux to backup + compress
sudo dd bs=4M if=/dev/sdb | gzip > PiOS.img.gz

# In linux to restore + decompress
gunzip --stdout PiOS.img.gz | sudo dd bs=4M of=/dev/sdb

```
