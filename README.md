# Home Media Server
Collection of docker compose/install scripts needed to setup a home media server using openmediavault, jellyfin and transmission

The scripts contained in this repo have been tested on a Raspberry Pi 4 running 64-bit lite OS.

The first thing we will need to do is flash a micro SD card with a Raspberry Pi lite operating system Buster or Bullseye(recommend using raspberry pi imager). The reason behind the lite version is not only the wasted resources(as we will be mostly using SSH, a browser or a dedicated client to access our home media server, but
mostly due to the fact that installation of openmediavault on an OS with GUI is actually not possible. When customizing our OS do not forget to set wireless lan credentials and enable ssh to avoid requiring an external display-keyboard.

Ok lets power on our Pi. It should automatically get connected to our wifi network. Cloning this repo on the NAS machine might be helpful at this stage of the process. SSH into it and lets start.

# Setting up a NAS using openmedia vault
Using the script contained in the omv folder install open media vault. This might take a while...

After the installation is complete use a browser to access the web UI of open media vault we will be setting our NAS on. The default credentials of omv web interface are username:admin and password:openmediavault.
We will need to create and mount a file system by going storage->file systems. You might need to wipe your storage before creating your filesystem(you might need to mount it twice when creating it). Then create a folder named "media" to share in the storage you just created. Then enable NFS
for linux and SMB for windows machines. On both cases enable them and also share the folder we just created by going share->select the media folder. And thats pretty much it. Your NAS is up and running. To test that you can add a network location with address \\{IP}\media 
(you might have trouble logging in, just check the user and reset his password also make sure he hass group ssh assigned)

# Docker setup
We will use docker compose files to run jellyfin and a torrent client later on so we will need docker installed on our machine. The easiest way is using the openmediavault docker plugin. The steps to do that are:
  1. Go to shared folders and create 2 folders named "containers" and "compose". Copy the container's folder path as we will need it later on. These folders might get large as time goes by so make sure they are on the drive with the highest capacity
  2. Go to omv-extras click enable, apply and then apt-clean.
  3. Go to system plugins and search and install compose plugin.
  4. Then go to services/compose/settings and on the compose path set the compose shared folder. On the docker storage path set the path we copied earlier
  5. Save the changes and restart docker

# Jellyfin setup
We will setup jellyfin media server to host our movies/series. There is an almost ready to go jellyfin docker compose file in the jellyfin folder. If you choose to get the files from a folder inside the same machine it would be better to mount the folder inside the jellyfin contianer. 
To do that you will have to replace the /srv/... with the actual path of your media folder

# Transmission setup
We can also setup a torrent client to download media and store them into our shared folder. That way jellyfin will pick them up and stream them. Also flood-for-transmission could be used as an alternative web UI.

# Firewall setup
A firewall could be configured using the interface of open media vault or ufw

# Pihole setup
Before running the docker compose file run the following commands:
sudo systemctl stop systemd-resolved.service
sudo systemctl disable systemd-resolved.service
sudo nano /etc/resolv.conf and edit the nameserver to another dns nameserver
If running openmediavault you might have to change the port of its web UI. Also the password for pihole is specified inside the docker compose file.
