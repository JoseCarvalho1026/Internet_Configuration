# In AWS

🔴 1 VPC (Create VPC on 172.31.0.0/16);

🔴 Three subnets, for example "172.31.0.0/24", "172.31.1.0/24" and "172.31.2.0/24";

🔴 2 Instance ("control.enta.pt", which will have the three subnets, so it has to be "small", where when creating the server 
"eth0" is "172.31.0.100" and "eth1" is "172.31.1.100", after creating, the machine must make a "Network Interface" 
with the remaining subnet and "attach" the server."www.enta.pt" will have its own subnet.
The "172.31.1.0/24 subnet" in "www.enta.pt" with the IP "172.31.1.101" ;

🔴 1 Elastic IP (for the server instance, but it must be "Network Interface" to the main board, which is "172.31.0.100");

🔴 STOP AWS to the server.

# In Termius
## "control.enta.pt"

◻️ `sudo hostnamectl set-hostname control.enta.pt` ;

### To enter the "www.enta.pt" machine in the future:

◻️ `nano chave.pem` paste the key ;

◻️ `chmod 400 chave.pem` or `chmod 600 chave.pem` .
____________________________________________________
◻️ `sudo su -` ;

◻️ `nano /etc/hosts` ;
```
192.168.1.101 www.enta.pt enta.pt www
```
◻️ `yum update -y` ;

◻️ `yum install iptables-services` ; 

◻️ `systemctl enable --now iptables` ;

◻️ `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` ;

◻️ `iptables -t nat -L -nv` ;

◻️ `iptables -F` ;

◻️ `service iptables save` ;

◻️ `nano /etc/sysctl.conf` ;

To add
```
net.ipv4.ip_forward=1
```
◻️ `sysctl -p /etc/sysctl.conf` confirm the command from above ;

◻️ `service network restart` ;

## "www.enta.pt"
On "control.enta.pt":

◻️ `ssh -i chave.pem www.enta.pt` ;
