# Steps followed to install Wireguard with Docker

1. **Create Digital Ocean Account**

2. **Create Ubuntu droplet**

    a. Go to "manage" on Digital ocean account, then droplets.
  
    b. Ubuntu version 23.10 x64. Choosing $6 version.
   
    c. Give either SSH key or password
   
    d. After creating droplet, you can ssh into droplet using Putty (windows), local terminal, or the console that comes with the droplet. I'm using local terminal from my laptop for SSHing into droplet. You can run this command: ssh root@dropletIPAddress
   

3. **Install Docker using the following steps:**

     a. sudo apt update #Update any packages

     b.  sudo apt install apt-transport-https ca-certificates curl software-properties-common #Let apt use packages over HTTPS

     c. curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg #Add GPG key for Docker repository

     d. echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
#Add docker repository to APT sources
    
     e. sudo apt update #Update lists of packages again

     f. apt-cache policy docker-ce #Install from docker repository instead of ubuntu repository
   
     g. sudo apt install docker-ce #Install Docker
   
     h. sudo systemctl status docker #Check docker is running


    Page Used for Docker installation: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04



4. **Install/start Wireguard:**

   a. mkdir -p ~/wireguard/
   
   b. mkdir -p ~/wireguard/config/

   c. nano ~/wireguard/docker-compose.yml

    **Put all of this inside docker-compose.yml:**

       Version: '3.8'

       services:

        wireguard:
   
         container_name: wireguard
   
         image: linuxserver/wireguard
   
         environment:
           - PUID=1000
           - PGID=1000
           - TZ=America/Chicago #Might need to change this
           - SERVERURL=137.184.156.58 #Change this
           - SERVERPORT=51820
           - PEERS=pc1,pc2,phone1
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
    

**CHANGES NEEDED:**
1. Change timezone (TZ) to the one you are located in. I am in Chicago's time zone. For that timezone, you would type (in TZ): America/Chicago
   Here's where the link that tells you what to put in for TZ depending on where you live: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
2. Replace SERVERURL with IP Address of droplet

**CONTINUE REST OF STEP 4**

  d. cd ~/wireguard/ #Change into Wireguard directory
   
  e. docker-compose up -d #Start Wireguard/Docker
   
  f. docker-compose logs -f wireguard #This command will show the QR codes for PC1, PC2, and phone1. Start wireguard from phone. Shows QR code. Scan phone1.

  Page used for Wireguard installation (gives you more info):
  https://thematrix.dev/setup-wireguard-vpn-server-with-docker/


5. **Get Wireguard VPN on Phone**
   
    a. Install Wireguard from App Store
  
    b. Once you open the app, scan the QR Code for phone1 to create the tunnel for the VPN.
  
    c. Name the VPN and make sure it is active. After that, you can go to IPleak.net to make sure it can detect you are running on your VPN on your phone. The way you know if it's working is if it shows your droplet's IP address at the top under "your IP addresses."
  

6. **Get Wireguard for Laptop**
   
    a. Make sure you download Wireguard for your laptop.
  
    b. Need Config file. Make sure you're in the Wireguard directory. Then, cd into the config directory.
  
    c. Next, cd into a pc config directory (E.g.: My PC config file was located inside peer_pc1).
  
    d. Run the ls command and find the conf file. My config file was called peer_pc1.conf.
  
    e. Run nano peer_pc1.conf and copy all the contents in the file.
  
    f. Open the Wireguard application on the desktop and choose "Add Tunnel." Choose the empty tunnel option, and copy and paste all the contents from
the config file into the dialog box. Then, save it under a name, and now your tunnel is officially created.

    g. Make sure your tunnel is showing as "active." FInally, go to IPLeak.Net on your laptop and make sure the IP address of your droplet shows up under "your IP addresses." At this point, you now have a Wireguard VPN connection for both your phone and laptop.


# Here are some screenshots to show what my final results looked like for both my phone and laptop:
# Phone images:

# Screenshot 1: IP address before turning on VPN:
![IPAdBefore](https://github.com/Kandharis15/Kandharis15-WireguardProject/assets/122307869/1ae15f43-f3ab-4bcb-b861-0e9d65b679f6)

# Screenshot 2: IP address after turning on VPN:
![IPWithVPN](https://github.com/Kandharis15/Kandharis15-WireguardProject/assets/122307869/f49ce7f2-fdec-41a0-84d0-7e4edc028514)

# Screenshot 3: Active VPN for phone:
![PhoneVPNTunnel](https://github.com/Kandharis15/Kandharis15-WireguardProject/assets/122307869/c32b7116-7f26-49ad-8081-26fe6da7fe28)


# Laptop images:

# Screenshot 1: 3 way photo showing IP address before/after turning on VPN and interface of desktop version of Wireguard:
![VPNLaptop](https://github.com/Kandharis15/Kandharis15-WireguardProject/assets/122307869/db171141-14b2-47a1-863e-5dade77d22da)








