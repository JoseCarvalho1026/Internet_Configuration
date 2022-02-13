# In AWS

🔴 1 VPC (Create VPC on 172.31.0.0/16);

🔴 Three subnets, for example "172.31.0.0/24", "172.31.1.0"24" and "172.31.2.0/24";

🔴 3 Instance (One Server, which will have the three subnets, so it has to be "small", where when creating the server "eth0" is "172.31.0.100" and "eth1" is "172.31.1.100", after create the machine must make a "Network Interface" with the remaining subnet and "attach" the server. Two Clients that each will have their subnet. The "subnet 172.31.1.0" in "client1" with the IP "172.31.1.101 " and "subnet 172.31.2.0" in "client2" with IP "172.31.2.101");

🔴 1 Elastic IP (for the server instance, but it must be "Network Interface" to the main board, which is "172.31.0.100");

🔴 STOP AWS to the server.

# In Termius
## Server

◻️ `sudo hostnamectl set-hostname server.com` ;

### Para futuramente entrar nas máquinas "Clients"

◻️ `nano chave.pem` colar a chave ;

◻️ `chmod 400 chave.pem` or `chmod 600 chave.pem` .
___________________________________________________
◻️ `sudo su -` ;

◻️ `apt update -y && apt upgrade -y` ;

◻️ `apt install netfilter-persistant iptables-persistant` ; 

◻️ `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` ;

◻️ `iptables -t nat -L -nv` ;

◻️ `netfilter-persistant save` ;

◻️ `nano /etc/sysctl.conf` ;

Descomentar:
```
net.ipv4.ip_forward=1
```
◻️ `sysctl -p` confirm the modification in `/etc/sysctl.conf` ;

◻️ `ip a` copiar onde diz "link/ether", o seguinte: "12:e3:02:75:19:67" (não é igual) ;
```
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000
    link/ether 12:e3:02:75:19:67 brd ff:ff:ff:ff:ff:ff
    ...
```
◻️ `nano /etc/netplan/50-cloud-init.yaml`;

Copiar de "eth1" a "set-name: eth1" e colar logo em baixo de "set-name: eth1" mudando "eth1" para "eth2" e valor do "macaddress: ..." pelo que foi buscar em `ip a` como pode ser observado logo de seguida.
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

## Clients
◻️ `sudo hostnamectl set-hostname client.com` / `sudo hostnamectl set-hostname client2.com`

◻️ `sudo su –`

◻️ `cd /etc/netplan/`

◻️ `cp 50-cloud-init.yaml 50-cloud-init.yaml_$(date -Is)`

◻️ `nano 50-cloud-init.yaml` (❗❗❗BEWARE OF TABS❗❗❗)

Configuration example:
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
                macaddress: 02:3c:85:52:f4:6f
            set-name: eth0
    version: 2
```
◻️ `netplan try`

◻️ `ping 1.1.1.1` (confirm you have an internet connection)

◻️ `apt-get update -y && apt upgrade -y`
