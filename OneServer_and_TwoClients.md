# In AWS

🔴 1 VPC (Create VPC on 192.168.0.0/16);

🔴 Three subnets, for example "192.168.0.0/24", "192.168.1.0/24" and "192.168.2.0/24";

🔴 3 Instance (One Server, which will have the three subnets, so it has to be "small", where when creating the server "eth0" is "192.168.0.100" and "eth1" is "192.168.1.100", after creating, the machine must make a "Network Interface" with the remaining subnet and "attach" the server. Two Clients that each one of them will have their subnet. The "subnet 192.168.1.0/24" in "www.inova.pt" with the IP "192.168.1.101" and "subnet 192.168.2.0/24" in "central.inova.pt" with IP "192.168.2.101");

🔴 1 Elastic IP (for the server instance, but it must be "Network Interface" to the main board, which is "192.168.0.100");

🔴 STOP AWS to the server.

# In Termius
## Server

◻️ `sudo hostnamectl set-hostname control.inova.pt` ;

### To enter the "www.inova.pt" and "central.inova.pt" machines in the future

◻️ `nano chave.pem` paste the key ;

◻️ `chmod 400 chave.pem` or `chmod 600 chave.pem` .
___________________________________________________
◻️ `sudo su -` ;

◻️ `nano /etc/hosts` ;
```
192.168.1.101 www.inova.pt inova.pt www
192.168.2.101 central.inova.pt inova.pt central
```
◻️ `apt update -y && apt upgrade -y` ;

◻️ `apt install netfilter-persistent iptables-persistent` ; 

◻️ `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` ;

◻️ `iptables -t nat -L -nv` ;

◻️ `netfilter-persistant save` ;

◻️ `nano /etc/sysctl.conf` ;

Uncomment:
```
net.ipv4.ip_forward=1
```
◻️ `sysctl -p` confirm the modification in `/etc/sysctl.conf` ;

◻️ `ip a` copy where it says "link/ether", the following: "12:e3:02:75:19:67" (not equal) ;
```
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000
    link/ether 12:e3:02:75:19:67 brd ff:ff:ff:ff:ff:ff
    ...
```
◻️ `nano /etc/netplan/50-cloud-init.yaml`;

Copy from "eth1" to "set-name: eth1" and paste right under "set-name: eth1" changing "eth1" to "eth2" and value of "macaddress: ..." so you searched for ` ip a` as can be seen right away.
```
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
            dhcp6: false
            match:
                macaddress: 12:e3:57:6d:0b:6f
            set-name: eth0
        eth1:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 200
            dhcp6: false
            match:
                macaddress: 12:ca:61:a2:e3:43
            set-name: eth1
        eth2:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 200
            dhcp6: false
            match:
                macaddress: 12:e3:02:75:19:67
            set-name: eth2
    version: 2
```
◻️ `netplan try` .

## "www.inova.pt" and "central.inova.pt"
On server:

◻️ `ssh -i chave.pem www.inova.pt` / `ssh -i chave.pem central.inova.pt` ;

◻️ `sudo hostnamectl set-hostname www.inova.pt` / `sudo hostnamectl set-hostname central.inova.pt` ;

◻️ `sudo su –` ;

◻️ `cd /etc/netplan/` ;

◻️ `cp 50-cloud-init.yaml 50-cloud-init.yaml_$(date -Is)` ;

◻️ `nano 50-cloud-init.yaml` (❗❗❗BEWARE OF TABS❗❗❗) ;

Configuration example "www.inova.pt":
```           
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                use-dns: false
                use-routes: false
            routes:
              - to: 0.0.0.0/0
                via: 192.168.1.100
                on-link: true
            nameservers:
                addresses: [ 192.168.1.100 ]
            dhcp6: false
            match:
                macaddress: 02:3c:85:52:f4:6f
            set-name: eth0
    version: 2
```
Configuration example "central.inova.pt":
```           
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                use-dns: false
                use-routes: false
            routes:
              - to: 0.0.0.0/0
                via: 192.168.2.100
                on-link: true
            nameservers:
                addresses: [ 192.168.2.100 ]
            dhcp6: false
            match:
                macaddress: 12:3c:e5:52:h7:5i
            set-name: eth0
    version: 2
```
◻️ `netplan try` ;

◻️ `ping 1.1.1.1` (confirm you have an internet connection) ;

◻️ `apt-get update -y && apt upgrade -y` ;
