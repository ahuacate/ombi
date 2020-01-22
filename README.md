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
- [1.00 Manually Configure Ombi Settings](#100-manually-configure-ombi-settings)
	- [1.01 Ombi Tab](#101-ombi-tab)
	- [1.02 Configuration Tab](#102-configuration-tab)
	- [1.03 Media Server Tab](#103-media-server-tab)
	- [1.04 TV Tab](#104-tv-tab)
	- [1.05 Movies Tab](#105-movies-tab)
	- [1.06 Music Tab](#106-music-tab)
	- [1.07 Notifications Tab](#107-notifications-tab)
	- [1.08 System Tab](#108-system-tab)
- [2.00 Ombi User Management](#200-ombi-user-management)
	- [2.01 Add a new user](#201-add-a-new-user)
- [3.00 How to use Ombi](#300-how-to-use-ombi)
- [4.00 Create & Restore Ombi Backups](#400-create--restore-ombi-backups)
	- [4.01 Create a Base Settings Backup](#401-create-a-base-settings-backup)
	- [4.02 Restore to Ombi Base Settings](#402-restore-to-ombi-base-settings)
- [00.00 Patches & Fixes](#0000-patches--fixes)


## 1.00 Manually Configure Ombi Settings
Browse to http://192.168.50.119:5000 and login to Ombi. Click the `Settings` and configure all your tabs as follows.

### 1.01 Ombi Tab
Click the `Settings` > `Ombi Tab` and configure as follows:

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

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/Ombi_Configuration.png)


### 1.02 Configuration Tab
Nothing to do here.

### 1.03 Media Server Tab
Click the `Settings` > `Media Server Tab` > `Emby/Jellyfin` and configure as follows:

| Emby/Jellyfin Configuration  | Value
| :---  | :---:
| Server Name  | `jellyfin`
| Host IP | `192.168.50.111`
| Port | `8086`
| SSL | `☐`
| Emby Api Key | `Insert your jellyfin api key here`
| Externally Facing Hostname | `.e https://jellyfin-site1.foo.bar`

And click `Test Connectivity` and if success click `Submit`.

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/Jellyfin_Configuration.png)

### 1.04 TV Tab
Click the `Settings` > `TV Tab` > `Sonarr` and configure as follows:

| Sonarr Settings  | Value | Notes
| :---  | :---: | :---
| Enable | `☑`
| V3 | `☑`
| Sonarr Hostname or IP | `192.168.50.115`
| Port | `8989`
| Sonarr Api Key | `Insert your sonarr api key here`
| SSL | `☐`
| Sonarr Base Url| `/sonarr`
| **Other Settings**
| Quality Profiles | `HD-1080p` | *Note, change to `Ultra-HD` or custom `4K > HD-1080p` if you have 4K HDR TV's*
| Quality Profiles (Anime) | `HD-1080p` | *Note, change to `Ultra-HD` or custom `4K > HD-1080p` if you have 4K HDR TV's*
| Default Root Folders  | `/mnt/video/tv/`
| Default Root Folders (Anime)  | `/mnt/video/tv/`
| Language Profiles | Leave Default
| Enable season folders | `☑`

And click `Test Connectivity` and if success click `Submit`.

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/Sonarr_Settings.png)


### 1.05 Movies Tab
Click the `Settings` > `Movies Tab` > `Radnarr` and configure as follows:

| Sonarr Settings  | Value | Notes
| :---  | :---: | :---
| Enable | `☑`
| Hostname or IP | `192.168.50.116`
| Port | `7878`
| Api Key | `Insert your radarr api key here`
| SSL | `☐`
| Base Url| `/radarr`
| **Other Settings**
| Quality Profiles | `HD-1080p` | *Note, change to `Ultra-HD` or custom `4K > HD-1080p` if you have 4K HDR TV's*
| Default Root Folders  | `/mnt/video/movies/`
| Default Minimum Availability | `Physical/Web`

And click `Test Connectivity` and if success click `Submit`.

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/Radarr_Settings.png)


### 1.06 Music Tab
Click the `Settings` > `Music Tab` > `Lidarr` and configure as follows:

| Sonarr Settings  | Value
| :---  | :---:
| Enable | `☑`
| Hostname or IP | `192.168.50.117`
| Port | `8686`
| Api Key | `Insert your lidarr api key here`
| SSL | `☐`
| Base Url| `/lidarr`
| **Other Settings**
| Quality Profiles | `Ahuacate`
| Default Root Folders  | `/mnt/music/`
| Metadata Profile | `Standard`
| Album Folder | `☑`

And click `Test Connectivity` and if success click `Submit`.

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/Lidarr_Settings.png)

### 1.07 Notifications Tab
Nothing to do here.

### 1.08 System Tab
Click the `Settings` > `System Tab` > `Update` and configure as follows:

| Update Settings | Value
| :---  | :---:
| Enable Automatic Update| `☑`
| Use your own updater script | `☐`
| Ombi Process Name | Ombi

And click `Submit`.

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/Update_Settings.png)


## 2.00 Ombi User Management
Ombi allows you to host your own media request and user management system. If you are sharing Jellyfin server with other users, allow them to request new content using an easy to manage interface. Manage all your requests for Movies, TV with ease, leave notes for the user and get notification when a user requests something.

When you add a new user you are required to set their roles. The user role options are:

Changing user roles requires them to log out and in again before they are applied.

| User Role | Description
| :---  | :---
| Admin | This is the role that gives the power to everything! You have full access to all users, manage requests and the ability to view and change any settings.
| Power User | This role has the ability to manage users and manage requests. This role does not have access to any settings
| Request Movie | Gives the ability for the user to request movies
| Request Tv | Gives the ability for the user to request TV Shows, Seasons and Episodes
| Auto Approve Movie | Auto approves all movie requests for this user
| Auto Approve TV | Auto approves all Tv Show, Season and Episode requests for this user
| Disabled | If a user has this role they can no longer log in.
| Receives Newsletter | User will receive your Newsletter.
| Request Music | User can request music.
| Auto Approve Music | Users music requests will be automatically approved and sent to Lidarr.
| Manage Own Requests | This will allow the user to Delete requests (should be used in conjunction with the Hide Requests from other users).
| Custom Page | This allows the user to edit the custom page (if enabled)

Changing user roles requires them to log out and in again before they are applied.

### 2.01 Add a new user
Browse to http://192.168.50.119:5000 and login to Ombi. Click the `User Management` > `Add User To Ombi` and configure all your fields as requested. 

My standard setup for trusted users is as follows:

| User Details | Value
| :---  | :---
| Username | Choose your users login name
| Alias | Leave Blank
| Email Address | Add yourusers email address
| Password | Random 16 character password ONLY i.e cA(8&KxjLHz8s4?A
| **User Settings**
| **Roles**
| `☑` | Auto Approve Movie
| `☑` | Auto Approve Music
| `☐` | Power User
| `☑` | Auto Approve Tv
| `☐` | Receives Newsletter
| `☐` | Request Music
| `☐` | Manage Own Requests
| `☐` | Edit Custom Page
| `☐` | Admin
| `☐` | Request Movie
| `☐` | Request Tv
| `☐` | Disabled
| **Request Limits** | Leave Default
| **Notification Preferences** | Leave Default
| ** Quality & Root Path Preferences | Leave Default

And remember to click `Create` to add the new user.

![alt text](https://raw.githubusercontent.com/ahuacate/ombi/master/images/User_Management.png)

## 3.00 How to use Ombi
You can use Ombi two ways: With any web browser by typing your externally facing URL (i.e https://jellyfin-site1.foo.bar) or by installing the Ombi App from either the Android or Iphone store. Then login with your preconfigured Ombi user account.

## 4.00 Create & Restore Ombi Backups
Ombi does'nt have a built in backup service.

But it's good idea to make a clean backup of your working Ombi settings, including all passwords etc, before adding any tv series media. The clean backup file MUST be stored outside of the Proxmox Ombi LXC container for safe keeping. Then in the event of you needing to recreate your Ombi LXC you can use this backup file to quickly restore all your Ombi settings.

The backup files must be located on your NAS in folder /mnt/backup/ombi for the following scripts to work.

### 4.01 Create a Base Settings Backup
Perform after you have completed Steps 1.00 or Steps 2.00. This file must be stored on your NAS for future rebuilds.
To create a backup use the Proxmox web interface and go to `typhoon-01` > `119 (ombi)` > `>_ Shell` and type the following:

```
sudo systemctl stop ombi.service &&
sleep 5 &&
cp /opt/Ombi/{Ombi.db,OmbiSettings.db,OmbiExternal.db} /mnt/backup/ombi &&
sudo systemctl restart ombi.service
```
Thats it. You have created a base backup.

### 4.02 Restore to Ombi Base Settings
With the Proxmox web interface go to `typhoon-01` > `119 (ombi)` > `>_ Shell` and type the following:
```
sudo systemctl stop ombi.service &&
sleep 5 &&
cp -rf /mnt/backup/ombi/{Ombi.db,OmbiSettings.db,OmbiExternal.db} /opt/Ombi &&
chown 1605:65605 /opt/Ombi/{Ombi.db,OmbiSettings.db,OmbiExternal.db} &&
sudo systemctl restart ombi.service
```

---

## 00.00 Patches & Fixes
