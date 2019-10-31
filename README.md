# Ombi Build
This recipe is for setting up Ombi. The following assumes your are familiar with Ombi and its preference menus and configuration options.

Network Prerequisites are:
- [x] Layer 2 Network Switches
- [x] Network Gateway is `192.168.1.5`
- [x] Network DNS server is `192.168.1.5` (Note: your Gateway hardware should enable you to a configure DNS server(s), like a UniFi USG Gateway, so set the following: primary DNS `192.168.1.254` which will be your PiHole server IP address; and, secondary DNS `1.1.1.1` which is a backup Cloudfare DNS server in the event your PiHole server 192.168.1.254 fails or os down)
- [x] Network DHCP server is `192.168.1.5`

Other Prerequisites are:
- [x] Synology NAS, or linux variant of a NAS, is fully configured as per [SYNOBUILD](https://github.com/ahuacate/synobuild#synobuild).
- [x] Proxmox node fully configured as per [PROXMOX-NODE BUILDING](https://github.com/ahuacate/proxmox-node/blob/master/README.md#proxmox-node-building).
- [x] pfSense is fully configured as per [HAProxy in pfSense](https://github.com/ahuacate/proxmox-reverseproxy/blob/master/README.md#haproxy-in-pfsense)
- [x] Sonarr LXC with Sonarr SW installed and configured per [Sonarr LXC - Ubuntu 18.04](https://github.com/ahuacate/sonarr/blob/master/README.md#sonarr-build)
- [x] Radarr LXC with Radarr SW installed and configured per [Radarr LXC - Ubuntu 18.04](https://github.com/ahuacate/radarr#radarr-build)
- [x] Jellyfin LXC with Jellyfin SW installed and configured per [Jellyfin LXC - Ubuntu 18.04](https://github.com/ahuacate/jellyfin#jellyfin-build)
- [x] Lidarr LXC with Lidarr SW installed and configured per [Lidarr LXC - Ubuntu 18.04](https://github.com/ahuacate/lidarr#lidarr-build)
- [x] Ombi LXC with Ombi SW installed and configured per [Ombi LXC - Ubuntu 18.04](https://github.com/ahuacate/proxmox-lxc-media/blob/master/README.md#1200-ombi-lxc---ubuntu-1804)

Tasks to be performed are:
- [ ] 1.00 Easy Sonarr Configuration
- [ ] 2.00 Manually Configure Sonarr Settings
- [ ] 3.00 Create & Restore Sonarr Backups
- [ ] 00.00 Patches & Fixes



## 1.00 Manually Configure Ombi Settings
Browse to http://192.168.50.119:5000 and login to Ombi. Click the `Settings Tab` and configure all your tabs as follows.

### 2.01 Configure Ombi Tab

| Ombi Configuration | Value
| :---  | :---:
| Base URL  | Leave blank
| Api Key | Leave Default
| Do not send Notifications if a User has the Auto Approve permission | `☑`
| Hide requests from other users | `☐`
| Ignore any certificate errors | `☐`
| Allow us to collect anonymous analytical data e.g. browser used | `☐`
| Language | English

And click `Submit`.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/media_management.png)

### 2.02 Configure Profiles
Edit Delay Profiles. Add 300 minutes to the torrent delay.
![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/profiles.png)

### 2.03 Configure Quality
Edit HDTV Quality and BluRay-1080p size limit to 10.00GB.
![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/quality.png)

### 2.04 Configure Indexers
This is where you configure Lidarr to use Usenet as your primary search indexer and Torrents (Jackett) as the secondary indexer.  For torrents Sonarr uses Jackett which must be installed as shown [HERE](https://github.com/ahuacate/jackett).

**A) Add Jackett as a Indexer**

Create a new torrent indexer using the `Torznab Custom` template and fill out the details as shown below.

| Add Torznab | Value
| :---  | :---:
| Name | `Jackett`
| Enable RSS Sync | `No`
| Enable Search | `Yes`
| URL | `http://192.168.30.113:9117`
| API Path | `/torznab/all/api`
| API Key | `s9tcqkddvjpkmis824pp6ucgpwcd2xnc`
| Categories | `5030,5040`
| Anime Categories | leave blank
| Additional Parameters | leave blank
| Minimum Seeders | `1`
| Seed Ratio | leave blank
| Seed Time | leave blank
| Season-Pack Seed Time | leave blank

And click `Save`. The finished Jackett configuration looks like:

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/torznab.png)

**B) Add Usenet Indexers**

Add all your Usenet indexers providers with the `Newsnab` presets (or custom if your provider is not listed).

Finally edit the `Options` Retention to `1500` days.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/indexers.png)

### 2.05 Configure Download Clients
**A)  Deluge Download Client**

First create a new download client using the `Torrent > Deluge` template and fill out the details as shown below.

| Add Deluge | Value | Notes
| :---  | :---: | :---
| Name | `Deluge`
| Enable| `Yes`
| Host | `192.168.30.113`
| Port | `8112`
| URL Base| leave blank
| Password| `insert your deluge password` | *This is your Deluge login password*
| Category | `sonarr-series`
| Recent Priority | First
| Older Priority | Last
| Add Paused | No
| Use SSL | No

And click `Test` to check it works. If successful, click `Save`.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/deluge.png)

**B)  NZBGet Download Client**

First create a new download client using the `Usenet > NZBGet` template and fill out the details as shown below.

| Add NZBGet | Value | Notes
| :---  | :---: | :---
| Name | `NZBGet`
| Enable| `Yes`
| Host | `192.168.30.112`
| Port | `6789`
| URL Base| leave blank
| Username | `client`
| Password| `insert your client password` | *This is your NZBGet client password*
| Category | `sonarr-series`
| Recent Priority | High
| Older Priority | Normal
| Add Paused | No
| Use SSL | No

And click `Test` to check it works. If successful, click `Save`.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/nzbget.png)

Other `download tab` settings must be set as follows:

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/download_client.png)

### 2.06 Configure Connect
Here you need to create two connections: A) Jellyfin; and, B) sonarr-episode-trimmer. 

**A)  Jellyfin Connection**

First create a new connection using the `Emby (Media Browser)` template and fill out the details as shown below.

| Add - Emby (Media Browser) | Value | Notes
| :---  | :---: | :---
| Name | `Jellyfin`
| On Grab| `No`
| On Download | `Yes`
| On Upgrade | `Yes`
| On Rename | `Yes`
| Filter Series | leave blank
| Host | `192.168.50.111`
| Port | `8096`
| API Key | Insert your Jellyfin API key | *Note, create one in Jellyfin for Sonarr*
| Send Notifications| `Yes`
| Update Library | `Yes`

And click `Test` to check it works. If successful, click `Save`.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/jellyfin.png)

**B)  sonarr-episode-trimmer**

First create a new connection using the `Custom Script` template and fill out the details as shown below.

| Add - Custom Script | Value | Notes
| :---  | :---: | :---
| Name | `sonarr-episode-trimmer`
| On Grab| `No`
| On Download | `Yes`
| On Upgrade | `No`
| On Rename | `No`
| Filter Series | leave blank
| Path | `/home/media/.config/NzbDrone/custom-scripts/sonarr-episode-trimmer.py`
| Arguments | `--config /home/media/.config/NzbDrone/custom-scripts/config --custom-script`

And click `Test` to check it works. If successful, click `Save`.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/sonarr-episode-trimmer.png)

### 2.07 Configure General
Here are required edits: 1) URL Base; and, 2) setting the security section to enable username and login.

| Start-Up | Value | Notes
| :---  | :---: | :---
| Bind Address | `*`
| Port Number | 8989
| URL Base | `/sonarr`
| Enable SSL | No
| Open Browser on start | Yes
| **Security**
| Authentication | `Basic (Browser Pop-up)`
| Username | `storm` | *Note, or whatever username you choose*
| Password | `insert password here` | *Add a complex password and record it i.e oTL&9qe/9Y&RV*
| API Key | leave default

And click `Save`.

![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/general.png)

### 2.08 Configure UI
![alt text](https://raw.githubusercontent.com/ahuacate/sonarr/master/images/ui.png)


## 3.00 Create & Restore Ombi Backups
Ombi does'nt have a built in backup service.

But it's good idea to make a clean backup of your working Ombi settings, including all passwords etc, before adding any tv series media. The clean backup file MUST be stored outside of the Proxmox Ombi LXC container for safe keeping. Then in the event of you needing to recreate your Ombi LXC you can use this backup file to quickly restore all your Ombi settings.

The backup files must be located on your NAS in folder /mnt/backup/ombi for the following scripts to work.

### 3.01 Create a Base Settings Backup
Perform after you have completed Steps 1.00 or Steps 2.00. This file must be stored on your NAS for future rebuilds.
To create a backup use the Proxmox web interface and go to `typhoon-01` > `119 (ombi)` > `>_ Shell` and type the following:

```
sudo systemctl stop ombi.service &&
sleep 5 &&
cp /opt/Ombi/{Ombi.db,OmbiSettings.db,OmbiExternal.db} /mnt/backup/ombi &&
sudo systemctl restart ombi.service
```
Thats it. You have created a base backup.

### 3.03 Restore to Ombi Base Settings
With the Proxmox web interface go to `typhoon-01` > `119 (ombi)` > `>_ Shell` and type the following:
```
sudo systemctl stop ombi.service &&
sleep 5 &&
cp -rf /mnt/backup/ombi/{Ombi.db,OmbiSettings.db,OmbiExternal.db} /opt/Ombi &&
chown 1605:65605 /opt/Ombi/{Ombi.db,OmbiSettings.db,OmbiExternal.db} &&
sudo systemctl restart ombi.service
```


## 00.00 Patches & Fixes
