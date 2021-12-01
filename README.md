# WireGuard
Installation guide for WireGuard VPN on a Ubuntu Droplet

# Digital Ocean
1.	I used my existing account, as I wasn't going to use much bandwidth
2.	Create a project, and then create a droplet
 * I used Ubuntu 20.04 for the image
 * I selected San Fransisco as that was recommended
 * I selected the cheapest CPU and the cheapest data plan
3. Open a console directly from digital ocean, giving you root access

# Installing Docker Compose
1. Install tools: `sudo apt install apt-transport-https ca-certificates curl software-properties-common -y`
2. Create docker key: `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
3. Add docker repo: 
   ```
   sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   ```
4. Switch to the correct repo: `apt-cache policy docker-ce`
5. Install docker: `sudo apt install docker-ce -y`
6. Install docker compose: `sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
7. Set permissions: `sudo chmod +x /usr/local/bin/docker-compose`

# Installing WireGuard
1. First, run this command block
```
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
```
2. Now, copy and paste this into the nano window: 
```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=143.198.99.44
      - SERVERPORT=51820
      - PEERS=laptop,phone
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```
3. Ctrl + X, Y, Enter
4. I selected the America/Chicago timezone, inserted my droplet's IP address, and set PEERS to my phone and laptop
5. Start WireGuard: 
```
cd ~/wireguard/
docker-compose up -d
```

# Connecting devices
1. Install WireGuard on your phone, and hit the plus button and select scan QR code
2. Run this command to display a QR code: `docker-compose logs -f wireguard`
3. Navigate to ~/wireguard/config/peer_laptop#
4. open the peer_laptop.conf and copy the info inside
5. Install the wireguard PC program
6. Create empty tunnel, and paste the info into it

# Screenshots
1. Phone:
 * Off VPN: ![Screenshot_20211201-145728](https://user-images.githubusercontent.com/27169767/144313200-7fdfab53-6735-4b09-9706-e531c0801cca.png)
 * On VPN:![Screenshot_20211201-145738](https://user-images.githubusercontent.com/27169767/144313213-b3c268fe-5af5-4d0a-9f79-05b4bb31f367.png)

2. Laptop:
 * Off VPN:<img width="526" alt="laptopNoVPN" src="https://user-images.githubusercontent.com/27169767/144313091-7f978a23-e45d-4c7f-a246-c8794b2b8ebf.PNG">
 * On VPN:<img width="508" alt="laptopVPN" src="https://user-images.githubusercontent.com/27169767/144313105-c0549636-9979-4574-8415-b3f6ab4ffd4a.PNG">

