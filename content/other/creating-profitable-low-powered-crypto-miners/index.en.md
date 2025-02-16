---
title: "Build a Profitable Passive Income Box with Low-Powered Hardware: A Guide"
draft: false
toc: true
date: 2023-02-07
description: "Learn how to set up a low-powered passive income crypto miner using a Raspberry Pi or Intel NUC, and earn $10-$20 per month per box with this guide"
tags: ["Build a Profitable Passive Income Box", "Low-Powered Hardware", "Passive Income", "Crypto Miner", "Raspberry Pi", "Intel NUC", "Guide", "Hardware Requirements", "OS Installation", "Software Installation", "Docker", "Automatic Docker Container Updates", "Ubuntu Server", "Ubuntu Desktop", "Raspbian", "Budget", "USFF", "Tiny", "Mini", "Micro PC", "Technical Experience", "EarnApp", "MYST", "Peer2Profit", "HoneyGain", "TraffMonitizer", "Watchtower", "Bitping", "Linux updates", "Ubuntu", "Debian", "CentOS", "RHEL", "offline updates", "local repository", "cache", "server setup", "client setup", "apt-mirror", "debmirror", "createrepo", "apt-cacher-ng", "yum-cron", "Linux system updates", "offline package updates", "offline software updates", "local package repository", "local package cache", "offline Linux updates", "handling offline updates", "offline update methods", "offline system maintenance", "Linux server updates", "Linux client updates", "offline software management", "offline package management", "update strategies", "Linux security updates"]
cover: "/img/cover/A_green_circuit_board_shaped_like_a_box_with_internet.png"
coverAlt: "a green, circuit board shaped like a box with internet connectivity symbols as wires connected to it."
coverCaption: ""
---

**Build a Profitable Passive Income Box with Low-Powered Hardware: A Guide**
Many people these days are into crypto mining and low powered miners such as helium miners. These are great and all but they don't earn all that much anymore and they are focused on one kind of earning. Today we're going to be building a low powered passive income box that earns anywhere from $15-$50 a month per box and residential IP.

*If you have the ability to set this box up on a guest network or, even better, a segregated VLAN, do so. While this is a security blog, we can't assume everyone's security concerns and risk tolerance.*

## Hardware Requirements:
One of the following is required. We basically just need any efficient and low powered computer we can get our hands on. Any Raspberry PI, Intel NUC, or similar will do. They don't have to be all that powerful. However I will recommend you have at least 32g-64g of storage, 4g of ram, and at least 2 cpu threads. For this we will be targeting a budget of around $100-$200 for hardware but feel free to go higher if it suits your needs. Our power target is aprox. 25w average.
### Raspberry Pi:
Hard to get ahold of these days but they are super low power and are quite customizable. For info on how to install raspian on your Raspberry PI 
- [Raspberry Pi 4B Model B DIY Kit](https://amzn.to/3x72kv0)
- [GeeekPi Raspberry Pi 4 4GB Starter Kit](https://amzn.to/3jG2g2k)
- [GeeekPi Raspberry Pi 4 8GB Starter Kit](https://amzn.to/3DQisF6)
### Any Mini PC with Intel N5100, N100, N305 or similar
For super low power Raspberry Pi equivalent but on x64 platform. 
- [Beelink U59 Mini PC ](https://amzn.to/3YkFhcj)
- [TRIGKEY Mini Computer](https://amzn.to/3XkbXkS)

## OS Installation:
We won't go into the technical details of how to install an operating system here. However here are some great resources to get you started.
### Raspbian:
- [Getting started](https://www.raspberrypi.com/documentation/computers/getting-started.html)
- [The New Method to Setup Raspberry Pi](https://www.youtube.com/watch?v=jRKgEXiMtns)
### Ubuntu:
- [Install Ubuntu desktop](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview)
- [Ubuntu Server - Basic installation](https://ubuntu.com/server/docs/installation)
- [Ubuntu Complete Beginner's Guide: Download & Installing Ubuntu](https://www.youtube.com/watch?v=W-RFY4LQ6oE)

_____________________

## Software Installation:
This is going to be a longer section. We are going to set up docker and then through docker we will set up automatic docker container updates and install multiple docker containers. We also assume you're using ubuntu server, however the commands for ubuntu server, ubuntu desktop, and raspbian should all be the same.

*For this section we assume some basic technical experience and that you have installed your operating system already as well as know how to get into the terminal.*

### Installing Updates:
We first want to be sure that we have our system fully up to date:
```bash
sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y
```

### Installing Docker:
We need to uninstall any existing versions that come prepackaged with the os and install the latest from Docker's repo themselves.
```bash
sudo apt-get remove -y docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Install Watchtower:
This docker container automatically updates all your docker containers to the latest images on a regular interval and cleans up old (stale) images.
```bash
docker run -d \
    --name watchtower \
    --restart unless-stopped \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower -c \
    --schedule "0 0 2 * * *" \
    --cleanup 
```
_____________________

### [Install Bitping](https://simeononsecurity.com/other/install-bitping-easily-using-docker/):
[*Bitping is a website monitoring and performance optimization solution that provides real-time, real user monitoring and instant feedback on downtime or degraded performance, with stress testing and benchmarking capabilities, dynamic rerouting and reprovisioning powered by a distributed network intelligence layer, and integration with existing workflows through webhooks.*](https://bitping.com)

Bitping offers you the ability to get paid out in Solana for providing a node for businesses to run lightweight network tests from your network.
This averages about 0.1 Cents per day per node. Not a lot I know, but it has potential and payouts are easy. 

#### Create an account:
Create an account at [bitping.com](https://bitping.com)

#### Install the docker container:
Step 1. Run these commands first as it walks you through setting up your container and asks you to sign in.
```bash
docker pull bitping/bitping-node
mkdir $HOME/.bitping/
docker run -it --mount type=bind,source="$HOME/.bitping/",target=/root/.bitping bitping/bitping-node:latest
```

Hit CTRL+C on your keyboard to escape the container following signing in with your bitping account.

Step 2. Run this command to persist the container in the background
```bash
docker run --net host --name bitping -td --mount type=bind,source="$HOME/.bitping/",target=/root/.bitping bitping/bitping-node:latest
```

_____________________

### [Install Earn App](https://simeononsecurity.com/other/install-earnapp-easily-using-docker/):
[*Take advantage of the time your devices are left idle by getting paid for your device’s unused resources*](https://earnapp.com/i/GCL9QzB5)

Earn app lets you share your internet as a VPN service for a surprising amount of rewards. Averages about $5 month per node per residential IP. Offers payouts via paypal and amazon gift cards. 

#### Create an Earn App Account:
Create an account at [earnapp.com](https://earnapp.com/i/GCL9QzB5).
*Warning, requires a google account*

#### Install the non docker version of the app to get your UUID:
Be sure to uninstall after you get your UUID otherwise you'll end up running it twice on the same host and without automatic updates
- [Instructions](https://help.earnapp.com/hc/en-us/articles/10261224561553-Installation-instructions)

#### Install the docker container:
Modify the string before pasting into your terminal. You need to specify your earn app UUID.
```bash
mkdir $HOME/earnapp-data
docker run -td --name earnapp --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro -v $HOME/earnapp-data:/etc/earnapp -e "EARNAPP_UUID"="" -e 'PUID'='99' -e 'PGID'='100' --name earnapp fazalfarhan01/earnapp:lite 
```
_____________________

### [Install EarnFM Client Using Docker](https://tinyurl.com/sosearnfm):  
[*Earn passive income by sharing your resources with EarnFM*](https://tinyurl.com/sosearnfm)  

This guide will help you set up the EarnFM client using Docker.

---

#### Prerequisites:  
- **EarnFM Account & API Key**: Register at [earn.fm](https://tinyurl.com/sosearnfm) and copy your API key from the settings in your EarnFM dashboard.
  - You have to download the Windows or Android app to create your account initially. Requires E-Mail confirmation.

---

#### Setup:  
Run the following command to set up the EarnFM client and Watchtower in one step:  

```bash
sudo docker stop earnfm-client; sudo docker rm earnfm-client; sudo docker rmi earnfm/earnfm-client:latest; sudo docker run -td --restart=always --dns 8.8.8.8 --dns 8.8.4.4 --dns 1.1.1.1 --dns 1.0.0.1 --dns 9.9.9.9 -e EARNFM_TOKEN="YOUR_APIKEY_PLEASE_REPLACE_ME" --name earnfm-client earnfm/earnfm-client:latest
```

Replace `"YOUR_APIKEY_PLEASE_REPLACE_ME"` with your EarnFM API key. For example:  
`"97f7414b-a0fb-4862-baba-e988d9a127fb"`

#### Enjoy Earning:
Get started and start earning passive income with EarnFM at scale!  
Sign up and learn more at [earn.fm](https://tinyurl.com/sosearnfm).
_____________________


### [Install Honey Gain](https://simeononsecurity.com/other/install-honeygain-easily-using-docker/):
[*Passive Income – Effortlessly with Honeygain, you can make money by simply sharing your Internet. Start earning now.*](https://r.honeygain.me/HONEY9149D)

Honey Gain lets you share your internet as a VPN service for a surprising amount of rewards. Averages about $5 month per node per residential IP. Payouts can be complicated. Read into it further before deciding to use this container

#### Create a Honey Gain Account:
Create an account at [honeygain.com](https://r.honeygain.me/HONEY9149D)

#### Install the Docker Container:
Modify the string with the obvious email, password, and device name before pasting into the terminal
```bash
docker run --name honeygain -td honeygain/honeygain -tou-accept -email ACCOUNT_EMAIL -pass ACCOUNT_PASSWORD -device DEVICE_NAME
```

_____________________

#### Alternate instructions for Raspberry Pi
- [How to install Honeygain on a Raspberry Pi with standard Raspberry Pi OS](https://www.reddit.com/r/Honeygain/comments/tj8vfa/how_to_install_honeygain_on_a_raspberry_pi_with/)


### [Install PawnsApp](https://simeononsecurity.com/other/install-pawnsapp-easily-using-docker/):
[*Make passive money online by completing surveys and sharing your internet *](https://pawns.app/?r=sos)
Pawns app, again similar to the others listed here offer to pay you for sharing your internet. Minimum payout is $5. Average payout is $0.50 per month per node per IP.

#### Create a PawnsApp Account:
Create an account at [https://pawns.app](https://pawns.app/?r=sos)

#### Install the Docker Container:

Modify the following with your email, password, device name, and device id before copying to your terminal.
```bash
docker pull iproyal/pawns-cli:latest
docker run -td --name pawnsapp --restart=on-failure:5 iproyal/pawns-cli:latest -email=email@example.com -password=change_me -device-name=raspberrypi -device-id=raspberrypi1 -accept-tos
```

_____________________

### [Install Repocket](https://simeononsecurity.com/other/install-repocket-easily-using-docker/):
[*Get Paid For Your Unused Internet*](https://link.repocket.co/raqc)

Similar to other offerings here. Minimum $20 Payout. Payouts can be complicated. Research for yourself to see if you want to use this service. Payouts average about $1 per node per box a month.

#### Create a Repocket Account:
Create an account at [repocket.co](https://link.repocket.co/raqc) and grab your api key from your dashboard.
#### Install the Docker Container:
Modify the following line with your email and api key before pasting into your terminal.
```bash
docker run -td --name repocket -e RP_EMAIL=your@email.com -e RP_API_KEY=yourapikey -d --restart=always repocket/repocket
```
_____________________


### [Install Traff Monetizer](https://simeononsecurity.com/other/install-traff-monetizer-easily-using-docker/):
[*Share your internet connection and make money online*](https://traffmonetizer.com/?aff=1389828&utm_source=traffmonetizerdockerguide)

Similar to EarnApp and HoneyGain, TraffMonetizer pays you to share your internet. Averages about $2 a month per node per IP. Only offers payouts in BTC.

#### Create your Traff Monetizer Account:
Create your account at [https://traffmonetizer.com](https://traffmonetizer.com/?aff=1389828&utm_source=traffmonetizerdockerguide)
Once you get into the dashboard, make note of your application token.

#### Install the Docker Container:
Copy the following string and append your token that you got from the dashboard before pasting into your terminal.

```bash
docker run -td --name traffmonetizer traffmonetizer/cli_v2 start accept --token
```
_____________________


### [Install Packetshare](https://www.packetshare.io/?code=2B1151EF76417143):
[*Share your internet connection and make money online*](https://www.packetshare.io/?code=2B1151EF76417143)

Similar to other bandwidth-sharing platforms, Packetshare pays you for sharing your internet connection. Ensure you comply with their Terms of Use to avoid account suspension.

#### Create your Packetshare Account:
Create your account at [https://www.packetshare.io](https://www.packetshare.io/?code=2B1151EF76417143).  
Once you register, make note of your credentials (username and password) for setting up the service.

#### Review the Terms of Service:
Before running the Packetshare Docker image, you must read and accept the Terms of Use. To get the current Terms of Service, run the following command in your terminal:

```bash
docker run packetshare/packetshare -show-tos
```

#### Install the Docker Container:
Copy and paste the following command into your terminal, replacing `USERNAME` and `PASSWORD` with your Packetshare account credentials:

```bash
docker run --restart unless-stopped packetshare/packetshare -accept-tos -email=USERNAME -password=PASSWORD
```

- Make sure to assign a different public IP address to each Docker container you set up.
- After starting the application, your new device should appear in your Packetshare dashboard.

#### Important Notes:
- Packetshare CLI follows the same restrictions as regular applications (e.g., device limits per IP address, network type checks).  
- Breaching Packetshare’s [Terms of Use](https://www.packetshare.io/ucenter.html?code=2B1151EF76417143) will result in account suspension.

_____________________

### Install ProxyLite:
[*Monetizing your Internet traffic by giving verified organizations the use of your Internet bandwidth*](https://proxylite.ru/?r=6AN2RKWB&utm_source=dockerguide)

Similar to EarnApp and HoneyGain, ProxyLite pays you to share your internet. Averages about $3 a month per node per IP. Offers payouts in BTC/PayPal/QIWI/Payeer.

#### Create your Traff Monetizer Account:
Create your account at [https://proxylite.ru](https://proxylite.ru/?r=6AN2RKWB&utm_source=dockerguide)
Once you get into the dashboard, make note of your userid.

#### Install the Docker Container:
Copy the following string and replace `$PROXYLITE_USER_ID` with your USERID that you got from the dashboard before pasting into your terminal.

```bash
docker rm -f proxylite && docker run -de "USER_ID=$PROXYLITE_USER_ID" --restart unless-stopped  --name proxylite proxylite/proxyservice
```
_____________________

### Install ProxyRack:

Similar to EarnApp, HoneyGain, and Proxylite, [ProxyRack](https://peer.proxyrack.com/ref/8lergbeafzcdwki144r5gdxqv1wepkox6tfcqigu) pays you to share your internet. Averages about $1 a month per node per IP. Payout methods vary.

#### Create your Traff Monetizer Account:
Create your account at [https://proxyrack.com](https://peer.proxyrack.com/ref/8lergbeafzcdwki144r5gdxqv1wepkox6tfcqigu)
Once you get into the dashboard, make note of your userid.

#### Install the Docker Container:
Copy the following string and replace `$PROXYLITE_USER_ID` with your `USERID` that you got from the dashboard before pasting into your terminal.

1. First, Generate a Device ID You can run this command to generate a device ID that you will need to copy and save to use in the future.

```bash
cat /dev/urandom | LC_ALL=C tr -dc 'A-F0-9' | dd bs=1 count=64 2>/dev/null
```

Example output `393889FD3A7AB796A3846423B1AC3AD73100508ADD9375AA24489A1D7C6AD713`

2. Run Proxyrack, edit Insert your Device ID after UUID. You can use this example command:

```bash
sudo docker run -td --name proxyrack --restart always -e UUID="" --restart unless-stopped  proxyrack/pop
```

3. Add this Device ID to your device list in your Peer account

Wait 5-10 minutes after running the Docker container with the UUID

Using the string you just generated above add this to your devices [https://peer.proxyrack.com/devices](https://peer.proxyrack.com/devices) You can add a "friendly" name to help you remember what this Device ID is associated with

_____________________

### [Install Mysterium](https://simeononsecurity.com/other/install-mysterium-easily-using-docker/):
[Mysterium](https://mystnodes.co/?referral_code=dZxIcDEWgjh8b5kviefiC7RFBInonroaPFHr2ztm) is a decentralized VPN and webscraping service built on the Etherium and Polygon blockchains. 
Payments average anywhere from $1-$20 a month depending on multiple factors per node per IP. Costs $1.XX to setup a node for activation. Payouts in MYST token.

#### Create an account at mystnodes.co

First, create an account at [mystnodes.co](https://mystnodes.co/?referral_code=dZxIcDEWgjh8b5kviefiC7RFBInonroaPFHr2ztm). You'll use this to manage your mysterium node(s).

#### Install the Docker Container:
```bash
docker volume create myst_data
docker run -td --cpus=1 --dns 8.8.8.8 --dns 8.8.4.4 --dns 1.1.1.1 --dns 1.0.0.1 --dns 9.9.9.9 --hostname myst --cap-add NET_ADMIN --network=host -p 4449:4449 -p 59850-60000:59850-60000 --name myst --device=/dev/net/tun  -v myst_data:/var/lib/mysterium-node mysteriumnetwork/myst:latest --udp.ports=59850:60000 service --agreed-terms-and-conditions
ufw allow 4449
ufw allow 59850:60000/tcp
```

#### Setup the Docker Container:

Go to `http://"nodeip":4449/#/dashboard` by replacing "nodeip" with the IP address of your node. You can find this by typing "ifconfig" in the terminal.

Click “start node setup”.

Past the address of the ERC20 wallet you want to receive rewards in and click “next”. You can use a standard Ethereum address like one of your MetaMask addresses.

Type in a password you’ll use to access this node dashboard in the future. DO check the checkbox to claim the node in your network.

Click the “Get it here” link and find your API key. Copy it. Go back and paste it. Click “Save & Continue”.

#### Port Forwarding:
We can not describe how to port forward for everyone's specific hardware. Here are some resources to learn how to port forward.
- [PortForward.com](https://portforward.com/)
- [Mysterium - Port Forwarding](https://docs.mysterium.network/troubleshooting/port-forwarding)

_____________________

### Auto Restart Docker Containers on Boot:
```bash
sudo docker update --restart unless-stopped $(docker ps -q)
```

_____________________

### Optional Configurations:
- [Automatic Ubuntu Updates and Reboots](https://www.cyberciti.biz/faq/set-up-automatic-unattended-updates-for-ubuntu-20-04/)
- [Blocking ToR Traffic on Ubuntu](https://serverfault.com/questions/1106645/blocking-tor-traffic-with-postfix-or-fail2ban-on-mailserver)
#### Increase security by blocking malware and trackers.
Force all dns requests to Cloudflares malware and tracking protection dns.
Also, block DNS/HTTPS requests.
*If you have more advanced of a router or firewall on the network you can even use packages like snort/securita  to create more advanced rules to block known bad acting IPs, tor access, torrents, p2p traffic in general, etc. This is highly suggested but not required.*
```bash
# Allow ssh still
sudo ufw allow 22
# Allow dns out
sudo ufw allow out 53/tcp
sudo ufw allow out 53/udp
# Redirect all dns back to 1.1.1.2 or 1.0.0.2
sudo iptables -t nat -A OUTPUT -p udp --dport 53 ! -d 1.0.0.2 -j DNAT --to-destination 1.1.1.2
sudo iptables -t nat -A OUTPUT -p udp --dport 53 ! -d 1.1.1.2 -j DNAT --to-destination 1.0.0.2
# Block DNS over HTTPS
sudo ufw deny out 853/tcp
sudo ufw deny out 853/udp 
iptables -A FORWARD -m string --string "get_peers" --algo bm -j LOGDROP
iptables -A FORWARD -m string --string "announce_peer" --algo bm -j LOGDROP
iptables -A FORWARD -m string --string "find_node" --algo bm -j LOGDROP
# Block Default ToR Ports
sudo ufw deny out 9050/tcp
sudo ufw deny out 9050/udp
sudo ufw deny out 9051/tcp
sudo ufw deny out 9051/udp
# Block Torrents
sudo ufw deny out 6881/tcp
sudo ufw deny out 6881/udp
sudo ufw deny out 6882-6999/tcp
sudo ufw deny out 6882-6999/udp
iptables -A FORWARD -m string --algo bm --string "BitTorrent" -j DROP
iptables -A FORWARD -m string --algo bm --string "BitTorrent protocol" -j DROP
iptables -A FORWARD -m string --algo bm --string "peer_id=" -j DROP
iptables -A FORWARD -m string --algo bm --string ".torrent" -j DROP
iptables -A FORWARD -m string --algo bm --string "announce.php?passkey=" -j DROP
iptables -A FORWARD -m string --algo bm --string "torrent" -j DROP
iptables -A FORWARD -m string --algo bm --string "announce" -j DROP
iptables -A FORWARD -m string --algo bm --string "info_hash" -j DROP
# Save the Changes and Enable the Firewall
sudo iptables-save
sudo ufw enable
```
For more advanced ToR blocking you can do the following:
```bash
#https://gist.github.com/jkullick/62695266273608a968d0d7d03a2c4185
sudo apt-get -y install ipset
ipset create tor iphash
curl -sSL "https://check.torproject.org/cgi-bin/TorBulkExitList.py?ip=$(curl icanhazip.com)" | sed '/^#/d' | while read IP; do
  ipset -q -A tor $IP
done
iptables -A INPUT -m set --match-set tor src -j DROP
```

_____________________

#### Docker Compose (Outdated):
To run all of these containers in one go, assuming you have all of your accounts and ids notated, you can update the following [`docker-compose.yml`](https://github.com/OlivierGaland/CashFactory/blob/main/docker-compose.yml):
```yaml
version: '3.5'

services:
    #Start of Portainer section :
    # Container management and monitoring : connect to your device port 9000 (Portainer)
    Portainer:
        image: portainer/portainer-ce:latest
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock
            - portainer_data:/data
        ports:
            - 8000:8000
            - 9000:9000
            - 9443:9443
        restart: always
        networks:
            default:
                ipv4_address: 172.106.0.11
    #End of Portainer + Webserver section
    
    mysterium:
    image: mysteriumnetwork/myst:latest
    container_name: mysterium
    restart: unless-stopped
    cpus: 1
    dns:
      - 8.8.8.8
      - 8.8.4.4
      - 1.1.1.1
      - 1.0.0.1
      - 9.9.9.9
    hostname: myst
    cap_add:
      - NET_ADMIN
    network_mode: host
    ports:
      - "4449:4449"
      - "59850-60000:59850-60000"
    volumes:
      - myst_data:/var/lib/mysterium-node
    command: --udp.ports=59850:60000 service --agreed-terms-and-conditions

    #Start of Earnapp section (remove this if Earnapp not wanted)
    # variables to define in .env file :
    # EARNAPP_DEVICE_ID : Your node id : sdk-node-<md5sum>
    #                     <md5sum> is a 32 char string containing a-z and 0-9 range : 
    #                       ex : Easy way to generate one :  echo "random string" | md5sum 
    #                            This will print 9f33ffbb8a9dcedb28ea909775a6b0d3  -
    #                            In that case use : sdk-node-9f33ffbb8a9dcedb28ea909775a6b0d3
    Earnapp:
        depends_on:
            - Portainer

        image: fazalfarhan01/earnapp:lite
        volumes:
            - earnapp-data:/etc/earnapp
        restart: always
        environment:
            - EARNAPP_UUID=$EARNAPP_DEVICE_ID
        networks:
            default:
                ipv4_address: 172.106.0.20
    #End of Earnapp section

    #Start of HoneyGain section (remove this if HoneyGain not wanted)
    # variables to define in .env file :
    # HONEYGAIN_EMAIL : Your Honeygain account email
    # HONEYGAIN_PASSWD : Your Honeygain account password 
    # DEVICE_NAME : This computer name (for display on dashboard)
    Honeygain:
        depends_on:
            - Portainer
        image: honeygain/honeygain
        command: -tou-accept -email $HONEYGAIN_EMAIL -pass '$HONEYGAIN_PASSWD' -device $DEVICE_NAME
        restart: always
        networks:
            default:
                ipv4_address: 172.106.0.30
    #End of HoneyGain section

    #Start of IproyalPawns section (remove this if IproyalPawns not wanted)
    # variables to define in .env file :
    # IPROYALPAWNS_EMAIL : Your IproyalPawns account email
    # IPROYALPAWNS_PASSWD : Your IproyalPawns account password 
    # DEVICE_NAME : This computer name (for display on dashboard)
    IproyalPawns:
        depends_on:
            - Portainer
        image: iproyal/pawns-cli:latest
        command: -email=$IPROYALPAWNS_EMAIL -password='$IPROYALPAWNS_PASSWD' -device-name=$DEVICE_NAME -accept-tos
        restart: always
        networks:
            default:
                ipv4_address: 172.106.0.40
    #End of IproyalPawns section

    #Start of Packetstream section (remove this if Packetstream not wanted)
    # variables to define in .env file :
    # PACKETSTREAM_CID : Your Packetstream CID (available in packetstream dashboard)
    Packetstream_PsClient:
        depends_on:
            - Portainer
        image: packetstream/psclient:latest
        restart: always
        environment:
            - CID=$PACKETSTREAM_CID
        networks:
            default:
                ipv4_address: 172.106.0.60
    Packetstream_Watchtower:
        depends_on:
            - Portainer
            - Packetstream_PsClient
        image: containrrr/watchtower
        command: --cleanup --include-stopped --revive-stopped --interval 60 ${_COMPOSE_PROJECT_NAME}_Packetstream_PsClient_${_COMPOSE_PROJECT_STACK_ID}
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock
        restart: always
        networks:
            default:
                ipv4_address: 172.106.0.61
    #End of Packetstream section
    
    #Start of Bitping section (remove this if Bitping not wanted)
    Bitping:
        depends_on:
            - Portainer
        image: bitping/bitping-node:latest
        restart: always
        volumes:
            - ./data/bitping:/root/.bitping
        networks:
            default:
                ipv4_address: 172.106.0.70
    #End of Bitping section     

    #Start of TraffMonetizer section (remove this if TraffMonetizer not wanted)
    # variables to define in .env file :
    # TRAFFMONETIZER_TOKEN : Your application token (available in TraffMonetizer dashboard)
    TraffMonetizer:
        depends_on:
            - Portainer
        image: traffmonetizer/cli_v2:latest
        restart: always
        command: start accept --token ${TRAFFMONETIZER_TOKEN}
        networks:
            default:
                ipv4_address: 172.106.0.80
    #End of Packetstream section   
    
    #Start of Repocket section (remove this if Repocket not wanted)
    # variables to define in .env file :
    # RP_EMAIL : Your application mail 
    # RP_API_KEY : Your application api key (available in repocket dashboard)
    Repocket:
        depends_on:
            - Portainer
        image: repocket/repocket:latest
        restart: always
        environment:
          - RP_EMAIL
          - RP_API_KEY
        networks:
            default:
                ipv4_address: 172.106.0.90
    #End of Repocket section    

    #Start of Proxylite section (remove this if Proxylite not wanted)
    # variables to define in .env file :
    # PROXYLITE_USER_ID : Your application use id (available in proxylite dashboard)
    Proxylite:
        depends_on:
            - Portainer

        image: proxylite/proxyservice:latest
        restart: always
        environment:
          - USER_ID=$PROXYLITE_USER_ID
        networks:
            default:
                ipv4_address: 172.106.0.100
    #End of Proxylite section 

    #Start of Proxyrack section (remove this if Proxyrack not wanted)
    # variables to define in .env file :
    # PROXYRACK_API_KEY : Your application api key (available in proxyrack dashboard -> profile -> generate API key (keep same for all devices))
    Proxyrack:
        depends_on:
            - Portainer

        image: proxyrack/pop:latest
        restart: always
        environment:
          - api_key=$PROXYRACK_API_KEY
          - device_name=$DEVICE_NAME
        networks:
            default:
                ipv4_address: 172.106.0.110
    #End of Proxyrack section 


volumes:
    portainer_data:
    earnapp-data:
    myst_data:

networks:
    default:
        driver: bridge
        ipam:
            driver: default
            config:
                - subnet: 172.106.0.0/16  
```

## Profit?
