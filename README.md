# To install Plex Media Server

## Pre Req install the “apt-transport-https” package
```
sudo apt-get install apt-transport-https
```

## Download and add the key to the package manager
```
curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
```

## Add the official plex repository to the sources list
```
echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
```

## Installing Plex to your Raspberry Pi

```
sudo apt-get update
sudo apt install plexmediaserver
# The first is that it creates a user and group for Plex to run under. This user and group is called “plex“. As Plex is running a different user to the Raspberry Pi’s default “pi” user, you will need to make sure you have permissions set correctly on your drive.
```
