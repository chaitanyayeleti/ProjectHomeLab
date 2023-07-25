# Traefik- Home Lab setup using Rasberrypi 4


## Prerequisites

- DNS- AzureDNS or Cloudflare # or any traefik Supported dns provider
- Virtual Machine - Standard B1s # as a VPN Gateway
- rasberry Pi 4

## Sotware Prerequisites
- Ubuntu or RasberryPi OS # Rasberry Pi 4 supports multiple OS i prefer ubuntu
- Docker
- WireGuard # VPN 


## Azure Virtual Machine as VPN Gateway.

  - We should avoid relying solely on the ISP-provided public IP due to potential limitations like Double NAT, which can result in a lack of control over network settings. Instead, opting for an Azure VM as a VPN gateway enables us to enforce Network Security Groups (NSG) and set up specific routes, granting us greater control and enhancing the security of our environment

  - Let's create an Azure VM with the Standard B1s size. To minimize latency, select the closest or preferred location. Opt for the latest version of Ubuntu. Initially, enable ports 80 and 443 for web services, and for security purposes, keep port 22 open but later consider changing it to a custom port. Additionally, for WireGuard VPN, ensure that port 51820/UDP is open, as it operates on UDP.

  ### Accessing the VPN Gateway using SSH
  - To acesses newly created server over SSH from windows you can use CMD or terminal 
     ``` bash
    ssh -p 22 username@publicip 
    ```
  - Follow the path for WireGuard Configuration [WireGuard Configuration on my other Repo](https://github.com/chaitanyayeleti/WireGuard) **Make Sure you configured Static ip on rasberry client**
  - Once it is connected now all the traffic will go though the VPN tunnel . But the  main issue is just imagine 2 devices are connected to the VPN and one of the device you are hosting the website now the incoming traffic where it will go ?
  - For Saving us we have **IP Tables ** we use these to route the traffic this is the reason why u must keep Static ip for the Client device.
  - ececute the below mentioned CMD on VPN GateWay machine
    ``` bash
    sudo iptables -t nat -A PREROUTING -i eth0 -p tcp -m tcp --dport 80 -j DNAT --to-destination 172.16.0.12:80 # you can use same port or port fowrding . eth0 and wg0 are the interface you are telling to forward the packet
    sudo iptables -A FORWARD -i **eth0** -o wg0 -p tcp -m tcp --dport 80 -j ACCEPT  # understand properly the highleted once for port forwarding happens
    ```
- Execute both the CMD's for all the required ports for you like 80,443,8080,53 depends on your requirement in my view 80 and 443 is fine for us
- After the execution save the IP Tables by below CMD
  ``` bash
  sudo sh -c "iptables-save > /etc/iptables/rules.v4"
  ```
## End of all networking and VPN related config 

# Install Docker & Docker-compose  
 - on the client machine/ rasberry pi 4 install both docker and docker-compose by using below CMD
   ``` bash
   sudo apt-get install docker.io docker-compose -y
   ```
 - Execute below cmd for proper file structure
   ``` bash
   mkdir traefik
   cd traefik
   mkdir data
   cd data
   touch acme.json
   chmod 600 acme.json
   touch traefik.yml
   touch config.yml
   ```
- traefik
  - Data
    - config.yml
    - traefik.yml
    - acme.json
  - Docke-compose.yaml

## Once the file structure is created Copy the code from my repo






