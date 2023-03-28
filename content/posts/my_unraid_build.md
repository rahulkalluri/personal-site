---
title: My unRAID homeserver build
type: page
description: When it comes to selfhosting, go big or go home.
topic: Home Server
---

 {{< figure src="https://i.imgur.com/sVSGSUh.png" title="" >}} 

# Why self-host?

- Be in control of your own data. 

- No need to pay for cloud services from other providers. For example, I use [Bitwarden](https://bitwarden.com/) for my password manager. While I pay the yearly subscription to support the company, I could just as easily host my own Bitwarden instance to keep my passwords secure on my own server. 

- Not tied to any specific vendor or tool keeps things flexible

- Building my own server would be cheaper in the long term and much more power efficient

- Cause it's fun! 


# [Hardware](https://pcpartpicker.com/list/mCDJKp)


Type|Item|Notes
:----|:----|:----
**CPU** | [Intel Core i3-12100 3.3 GHz Quad-Core Processor](https://pcpartpicker.com/product/qrhFf7/intel-core-i3-12100-33-ghz-quad-core-processor-bx8071512100) | 
**CPU Cooler** | [ID-COOLING IS-50X V2 54.6 CFM CPU Cooler](https://pcpartpicker.com/product/hhH7YJ/id-cooling-is-50x-v2-546-cfm-cpu-cooler-is-50x-v2) | 
**Motherboard** | [ASRock Z690M-ITX/ax Mini ITX LGA1700 Motherboard](https://pcpartpicker.com/product/2PYmP6/asrock-z690m-itxax-mini-itx-lga1700-motherboard-z690m-itxax) | 
**Memory** | [Corsair Vengeance RGB Pro 16 GB (2 x 8 GB) DDR4-3200 CL16 Memory](https://pcpartpicker.com/product/QDhKHx/corsair-vengeance-rgb-pro-16gb-2-x-8gb-ddr4-3200-memory-cmw16gx4m2c3200c16) | From spare parts bin
**Storage** | [Samsung 970 Evo Plus 1 TB M.2-2280 PCIe 3.0 X4 NVME Solid State Drive](https://pcpartpicker.com/product/Zxw7YJ/samsung-970-evo-plus-1-tb-m2-2280-nvme-solid-state-drive-mz-v7s1t0bam) | Cache 
**Storage** | [Samsung 970 Evo Plus 1 TB M.2-2280 PCIe 3.0 X4 NVME Solid State Drive](https://pcpartpicker.com/product/Zxw7YJ/samsung-970-evo-plus-1-tb-m2-2280-nvme-solid-state-drive-mz-v7s1t0bam) | Cache 
**Storage** | [Western Digital Red Plus 8 TB 3.5" 7200 RPM Internal Hard Drive](https://pcpartpicker.com/product/hsYmP6/western-digital-wd-red-plus-8-tb-35-7200rpm-internal-hard-drive-wd80efbx) | Array 
**Storage** | [Western Digital Red Plus 14 TB 3.5" 7200 RPM Internal Hard Drive](https://pcpartpicker.com/product/NspzK8/western-digital-wd-red-plus-14-tb-35-7200rpm-internal-hard-drive-wd140efgx) | Array 
**Storage** | [Seagate Exos X18 16 TB 3.5" 7200 RPM Internal Hard Drive](https://pcpartpicker.com/product/VGxRsY/seagate-exos-enterprise-x18-16-tb-35-7200rpm-internal-hard-drive-st16000nm000j) | Array 
**Storage** | [Seagate Exos X18 16 TB 3.5" 7200 RPM Internal Hard Drive](https://pcpartpicker.com/product/VGxRsY/seagate-exos-enterprise-x18-16-tb-35-7200rpm-internal-hard-drive-st16000nm000j) | Parity 
**Case** | [Jonsbo N1 Mini ITX Desktop Case](https://pcpartpicker.com/product/YTkWGX/jonsbo-n1-mini-itx-desktop-case-n1) |
**Power Supply** | [Corsair SF750 750 W 80+ Platinum Certified Fully Modular SFX Power Supply](https://pcpartpicker.com/product/nJrmP6/corsair-750-w-80-platinum-certified-fully-modular-sfx-power-supply-cp-9020186-na) | 
 **SATA Expansion Card** | [Ziyituod PCIe SATA Card, 4 Port (ASMedia 1064)](https://www.amazon.com/dp/B07SZDK6CZ) | 
**Low-Profile SATA Cables** | [ADCAUDX Right-Angle SATA-III 0.5M Cable](https://www.amazon.com/dp/B0B1CZHXZ1)

The array has **38 TB** of usable space as one of the 16 TB drives is used as a parity drive. 

The 2 NVME drives are currently mirrored RAID1 but occasionally set to RAID0 if I'm anticipating a large volume of downloads since merging the drives using RAID0 is faster than keeping them separate.

I opted for a SATA expansion PCIe card instead of an HBA card primarily due to thermal management issues. I want this build to be quiet and tucked away. The ASMedia 1064 controller is pretty much an [approved, low wattage controller](https://forums.unraid.net/topic/102010-recommended-controllers-for-unraid/) for use in unRAID.

# Software

Everything listed below is enabled through [Docker](https://docker.com/) containers on [unRAID OS](https://unraid.net/product). Shout out to [Ibracorp](https://docs.ibracorp.io/ibracorp/) for providing a ton of fantastic documentation to help get many of these services up and running. 

## Administrative Tools
po
* [Code-Server](https://github.com/coder/code-server) - Access [Visual Studio Code](https://code.visualstudio.com/docs/remote/remote-overview) anywhere using your browser 

* [Homepage](https://github.com/benphelps/homepage) - Simple homepage/dashboard for Docker containers with integrations. Easily configured using YAML files.

* [Overseerr](https://github.com/sct/overseerr) - Request management and media discovery tool for PMS 

* [Wizarr](https://github.com/Wizarrrr/wizarr) - Manage user invites to Plex/Jellyfin with Overseerr integration

* [Tautulli](https://github.com/Tautulli/Tautulli) - Monitoring and analytics for PMS

* [Plex Meta Manager](https://github.com/meisnate12/Plex-Meta-Manager) - Automatically build collections and playlists for Plex. For example, when Halloween comes around Plex can recommend seasonally appropriate movies

* [Krusader](https://github.com/KDE/krusader) - A great file manager to interact with my server remotely

* [Adminer](https://github.com/vrana/adminer) - Lightweight tool to run database commands without having to type a single line of code

## Security & Networking

* [Cloudflared](https://github.com/cloudflare/cloudflared) - Connect resources through a secure tunnel hosted by Cloudflare  

* [Authelia](https://github.com/authelia/authelia) - Add multi-factor authentticaion and single sign-on to services 

* [Pihole](https://github.com/pi-hole/pi-hole) - Network wide ad blocking

* [Tailscale](https://github.com/tailscale/tailscale) - VPN into your home network from any device 

* [Traefik](https://github.com/traefik/traefik) - Reverse-proxy tool of choice. I've used [Caddy](https://github.com/caddyserver/caddy) in the past but Traefik is a lot more feature packed. It'll also create HTTPS certificates through LetsEncrypt for automatic HTTPS support.

* [Unbound](https://www.nlnetlabs.nl/projects/unbound/about/) - Recursive DNS resolver used [in conjunction](https://docs.pi-hole.net/guides/dns/unbound/) with Pihole

* [Docker Socket Proxy](https://github.com/Tecnativa/docker-socket-proxy) - Provide Docker containers a secure means to access host network 


## Media

* [Plex Media Server (PMS)](https://www.plex.tv/) - Share movies, music, and tv shows with friends and family

* [Jellyfin](https://github.com/jellyfin/jellyfin) - Free and open source alternative to PMS

* [PhotoPrism](https://github.com/photoprism/photoprism) - Host your own Google Photos. Powered by AI for face regonition, location/content classification, and more.


### Downloaders 

* [Deluge (w/ VPN)](https://github.com/binhex/arch-delugevpn) - My torrent client of choice. OpenVPN configs from [PrivateInternetAccess](https://www.privateinternetaccess.com/) though I recommend [Mullvad](https://mullvad.net/en/) if you're looking for a new VPN provider.

* SABnzbd - Usenet client. Usenet is a lot faster but requires more overhead, like costs towards a Usenet provider and sometimes an indexer. 

### *arr apps
> *arr apps are designed to automatically grab, sort, organize, and monitor your Music, Movie, E-Book, or TV Show collections for Lidarr, Radarr, Readarr, Sonarr, and Whisparr; and to manage your indexers and keep them in sync with the aforementioned apps for Prowlarr - [Servarr Wiki](https://wiki.servarr.com/)

* [Sonarr](https://github.com/Sonarr/Sonarr) - TV shows

* [Radarr](https://github.com/Radarr/Radarr) - Movies

* [Lidarr](https://github.com/Lidarr/Lidarr) - Music

* [Bazarr](https://github.com/morpheus65535/bazarr) - Manage and download subtitles

* [Prowlarr](https://github.com/Prowlarr/Prowlarr) - Indexer manager for the *arr apps that supports Torrent and Usenet trackers

* [Recyclarr](https://github.com/recyclarr/recyclarr) - Automatically sync [TRaSH guides](https://trash-guides.info/) to Sonarr/Radarr instances


## Database

* [MariaDB](https://github.com/MariaDB/server) - MySQL server. Used for Authelia and PhotoPrism.

* [Redis](https://github.com/redis/redis) - Used to support MariaDB and Authelia


# External Software

I use [PhotoSync](https://www.photosync-app.com/home.html) to automatically sync any photo taken on my phone to the server. Every photo lands on the cache drives for processing and then gets moved to the array once imported.

Google Drive through Google Workplace Business is used for my off-site cloud backups. 



# Backup
Now that I've got all my media automation sorted through, I'll be working on setting up my backup system. Stay tuned for updates!