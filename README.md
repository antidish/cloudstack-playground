# Playing with Cloudstack
Cloud Computing class of DTE FTUI - Even Semester 2024/2025 - Group 11:
- Annisa Ardelia Setiawan (2206059471)
- Muhammad Farrel Mirawan (2106731554)
- Andikha Wisanggeni (2106731503)
- Yehezkiel Jonatan (2006520235)

Instructor: Yan Maraden

Note: in our examples, every password is the same as the username. This is not best practice at all. Please use strong passwords, especially in production environments.

## Environment Setup
We used a Dell Latitude 3500 laptop for Cloudstack management and agent provided by our instructor, Yan Maraden. The hardware configuration is:
```
CPU : Intel Core i5 gen 8
RAM : 24 GB
Storage : 250GB
Network : Ethernet 100GB/s
Operating System : Ubuntu Server 25.04
```

Our network configuration is:
```
Network Address: 192.168.1.0/24
Host IP address: 192.168.1.11
Gateway: 192.168.1.1
Management IP: 192.168.1.11
Public IP: 192.168.1.201-192.168.1.210
System IP: 192.168.1.211-192.168.1.220
```

## Install Ubuntu Server 25.04
- We used a flash drive to install Ubuntu Server.
- Create a bootable drive of Ubuntu Server onto the flash drive.
- Boot into it.
- On the GRUB menu, click Try or Install Ubuntu Server
- Choose your language: default English
- Choose your keyboard layout: default English (US)
- Choose the type of installation: default Ubuntu Server, not Ubuntu Server (minimized). And choose Search for third-party drivers.
- Network configuration for internet connection: We used a wired connection.
- Proxy configuration: we used no proxies (blank)
- Ubuntu archive mirror configuration: we used the default mirror address (http://id.archive.ubuntu.com/ubuntu/). Make sure the mirror location passes the connectivity test.
- Guided storage configuration. We used the entire disk and set it up as an LVM group (default). We used no encryption (LUKS).
- Storage configuration. We extended the ubuntu-lv to take up the entire space of the ubuntu-vg, because it defaults to 100G while there's 235.421G of total space on the disk. We leave everything else as default. Confirm destructive action.
- Profile configuration: enter your username and password.
- SSH configuration: choose to install the OpenSSH server package so that we don't need to install it later.
- Third-party drivers: we have no applicable third-party drivers available, but choose to install them if you have.
- Featured server snaps: we skip them.
- Installing system. After it's done: Reboot now, remove the installation medium, then press ENTER.
- Login using the username and password.
- DONE! You can immediately SSH to the server because we've already installed OpenSSH Server beforehand. (Install net-tools first to use ifconfig to see the IP address of the server).


## Install Cloudstack on Ubuntu Server 25.04
Sources:
- https://github.com/AhmadRifqi86/cloudstack-install-and-configure/tree/main/cloudstack-install
- [Apache Cloudstack 4.18 Installation on Ubuntu Server 22.04 LTS](https://www.youtube.com/watch?v=txQTvnF9jk8)
- https://www.youtube.com/watch?v=DlJg3LYvIIs
- https://www.youtube.com/watch?v=mvuSBFGSYWI&list=PLW7vgBNPiQhmvj6dnS5ss3IdTqHCqvknm

### Network Configuration
There's a file in /etc/netplan/50-cloud-init.yaml. Edit that file into this (change the network address into your host IP address and the network interface aswell):
```yaml
# This is the network config written by 'subiquity'
network:
  version: 2
  renderer: networkd
  ethernets:
    enp1s0:
      dhcp4: false
      dhcp6: false
      optional: true
  bridges:
    cloudbr0:
      addresses: [192.168.1.11/24]  # Your host IP address
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1,8.8.8.8]
      interfaces: [enp1s0]
      dhcp4: false
      dhcp6: false
      parameters:
        stp: false
        forward-delay: 0
```

Apply the network configuration (if you change the network address, this will disconnect your SSH session and you would have to reconnect back using the new network address):
```bash
sudo netplan generate #generate config file for the renderer
sudo netplan apply  #applies network configuration to the system
sudo reboot #reboot the system
```

Check ifconfig to make sure there's a network bridge called cloudbr0:
```
cloudbr0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.11  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::d872:c1ff:fee0:d312  prefixlen 64  scopeid 0x20<link>
        ether da:72:c1:e0:d3:12  txqueuelen 1000  (Ethernet)
        RX packets 325  bytes 28333 (28.3 KB)
        RX errors 0  dropped 32  overruns 0  frame 0
        TX packets 95  bytes 12881 (12.8 KB)
        TX errors 0  dropped 2 overruns 0  carrier 0  collisions 0

enp1s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether a4:bb:6d:15:3c:72  txqueuelen 1000  (Ethernet)
        RX packets 357  bytes 37263 (37.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 95  bytes 12881 (12.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 84  bytes 6352 (6.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 84  bytes 6352 (6.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
And make sure you have internet connectivity.

### Random Installs (i don't think these are necessary)
Installing Hardware resource monitoring tools
```bash
sudo apt install htop lynx duf bridge-utils
```
- htop is CPU usage monitoring tool
- duf is Disk usage monitoring tool
- lynx is CLI based web browser
(htop is already in the system)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514111025png)

```bash
sudo apt install openntpd openssh-server sudo vim tar intel-microcode
```

- openntpd is NTP client to synchronize time between host and entire internet
- openssh-server is an SSH server to enable remote access
- vim is a text editor
- tar is a tool to compress and decompress file. Tar is used to decompress most of the downloaded file
- intel-microcode is a set of procedures written in x86 assembly to increase low level modularity
(the only package installed is openntpd, the rest is already installed)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514111124.png)

### Enable SSH Root Login (why actually?)
```bash
sudo passwd root
```
Ubuntu disables root password by default, so we have to make a root password to be able to login as root.

Permit SSH root login by editing the file /etc/ssh/sshd_config and restarting the ssh service.
```bash
sudo nano /etc/ssh/sshd_config
```
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514111909.png)

```bash
service ssh restart
```

### Configure Clock
We need our server's clock to be in sync by using NTP. Use timedatectl to make sure that the time and the timezone is correct.
```bash
timedatectl set-timezone Asia/Jakarta
```


### Actually Installing Cloudstack
The Controller and Compute Node is on the same host. We import cloudstack repositories key:
```bash
mkdir -p /etc/apt/keyrings 
wget -O- http://packages.shapeblue.com/release.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/cloudstack.gpg > /dev/null
sudo su
echo deb [signed-by=/etc/apt/keyrings/cloudstack.gpg] http://packages.shapeblue.com/cloudstack/upstream/debian/4.20 / > /etc/apt/sources.list.d/cloudstack.list
```
The last command has to be done by the root user and you can update the Cloudstack version (in this case, it's 4.20) when a newer release is available.

Install Cloudstack and MySQL Server:
```bash
sudo apt update
sudo apt install cloudstack-management mysql-server
```
You have to update apt first to get the new repository to show up. This will take a long time (an hour or two).
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514132037.png)

### Configure MySQL
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Copy these lines to under the [mysqld] section:
```
server-id = 1
sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ERROR_FOR_DIVISION_BY_ZERO,NO_ZERO_DATE,NO_ZERO_IN_DATE,NO_ENGINE_SUBSTITUTION"
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=1000
log-bin=mysql-bin
binlog-format = 'ROW'
```

Restart mysql service
```
systemctl restart mysql
```

Check mysql service status
```
systemctl status mysql
```
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514133105.png)
There should be "active (running)" and the Status is "Server is operational".

Deploy Database as Root and Then Create "cloud11" User with Password "cloud11" too. Change the password and IP address to yours.
```bash
sudo cloudstack-setup-databases cloud11:cloud11@localhost --deploy-as=root:root -i 192.168.1.11
```
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514133528.png)

### Configure Primary and Secondary Storage
```bash
sudo apt install nfs-kernel-server quota
```

Paste this line into /etc/exports:
```
/export  *(rw,async,no_root_squash,no_subtree_check)
```

Make two directories, each for primary and secondary storage:
```bash
sudo mkdir -p /export/primary /export/secondary
sudo exportfs -a
```

### Configure NFS Server
Paste this line into /etc/default/nfs-kernel-server:
```
RPCMOUNTDOPTS="-p 892 --manage-gids"
RPCNFSDCOUNT=8
RPCSVCGSSDOPTS=""
```

Paste this line into /etc/default/nfs-common:
```
STATDOPTS="--port 662 --outgoing-port 2020"
NEED_STATD=yes
```

Paste this line into /etc/default/quota:
```
RPCRQUOTADOPTS="-p 875"
```

Then restart the service:
```
service nfs-kernel-server restart
```

### Configure Cloudstack Host with KVM Hypervisor (and libvirtd config)
```
sudo apt install qemu-kvm cloudstack-agent
```

Enable vnc_listen in /etc/libvirt/qemu.conf (scroll down a bit):
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514135755.png)

Enable LIBVIRTD_ARGS="--listen" in /etc/default/libvirtd:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514140026.png)

Paste in lines to /etc/libvirt/libvirtd.conf:
```
listen_tls = 0
listen_tcp = 1
tcp_port = "16509"
mdns_adv = 0
auth_tcp = "none"
auth_tls = "none"
```

Restart libvirtd:
```
systemctl mask libvirtd.socket libvirtd-ro.socket libvirtd-admin.socket libvirtd-tls.socket libvirtd-tcp.socket
systemctl restart libvirtd
```

### Configuration to Support Docker and Other Services
Paste in lines to /etc/sysctl.conf:
```
net.bridge.bridge-nf-call-arptables = 0
net.bridge.bridge-nf-call-iptables = 0
```

Confirm the configuration:
```bash
sysctl -p
```

Generate UUID
```bash
sudo apt install uuid
uuid
```

Paste in the UUID into /etc/libvirt/libvirtd.conf:
```
host_uuid = "$UUID"
```

Restart the service:
```
systemctl restart libvirtd
```

### Configure IPtables Firewall
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514151645.png)

Just paste this (change the IP Address to yours):
```bash
NETWORK=192.168.1.11/24
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 111 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 111 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 2049 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 32803 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 32769 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 892 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 875 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 662 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 8250 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 8080 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 8443 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 9090 -j ACCEPT
sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 16514 -j ACCEPT
#sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 3128 -j ACCEPT
#sudo iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 3128 -j ACCEPT
```

Then install iptables-persistent and press yes when prompted to save current IPv4 and IPv6 rules. Don't worry when it says removing cloudstack-agent, just continue.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514141609.png)
```bash
sudo apt install iptables-persistent
```

Check the existence and content of /etc/iptables/rules.v4.

Actually... Install cloudstack-agent again...This will uninstall iptables-persistent, which is fine i think... Just save the configuration into another file for safe keeping.

Then what the hell is iptables-persistent is for then?

```bash
sudo touch /etc/iptables/rules.v4.bak
sudo iptables-save
# Save the configuration to rules.v4.bak
sudo apt install cloudstack-agent
```
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514152314.png)

### Disable apparmour on libvirtd
```bash
sudo ln -s /etc/apparmor.d/usr.sbin.libvirtd /etc/apparmor.d/disable/
sudo ln -s /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/usr.sbin.libvirtd
sudo apparmor_parser -R /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper
```

### Launch Management Server (finally!)
```
sudo cloudstack-setup-management
systemctl status cloudstack-management
tail -f /var/log/cloudstack/management/management-server.log #if you want to troubleshoot 
#wait until all services (components) running successfully
```

Open your web browser and connect to http://<YOUR_IP_ADDRESS>:8080.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514144731.png)
Username: admin
Password: password

![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514144905.png)
Continue with the installation.

You can change the password if you want.
Username = Password = admin

Zone type: Core (default)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145118.png)

Core zone type: Advanced (default)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145122.png)

Zone details: fill in the Name, required DNSes, and change the Hypervisor to KVM
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145257.png)

Network: skip the physical network
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145349.png)
For public traffic: reserve a range of IP addresses
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145504.png)
For Pod: make a new Pod with a range of reserved IP addresses
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145715.png)
For Guest Traffic: use 3300-3399

Add resources: for cluster, make a new cluster
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514150621.png)
For IP address: insert the Management Server's IP Address and use the root's login credentials
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514150233.png)
For Primary storage:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514150412.png)
For Secondary storage:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514152427.png)

Launch the zone!
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514152441.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514153539.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514153540.png)

Make sure the two system VMs are running:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164430.png)
After this, you should be able to safely reboot the server and comeback to this web console successfully.

## Installing Instances
### ISOs and Templates 
Register ISOs onto Cloudstack, either through a URL or upload it from your computer.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514222535.png)

TinyCore-16.0:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514223020.png)

Ubuntu Server 25.04:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250515004002.png)

Then you can create templates based on those instances. Then, those templates can be used to create instances.

### Starting a New Instance
Go to Compute > Instances then click Add Instance
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164559.png)

1: admin account
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164657.png)
2: deploy on the FRIENDZONE zone.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164658.png)
3: Use the default CentOS template.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164748.png)
4: Use the Small Instance Compute Offering.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164749.png)
5: No thanks (we just use the root disk)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164751.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514165451.png)
6: Create a new isolated network and use that.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514175300.png)
7: Nothing
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514171529.png)
8: Nothing
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514171530.png)
9: Name the Instance
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514171610.png)
Launch!
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514175321.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514175358.png)


## CentOS Instance Fun Stuff
The default CentOS instance on Cloudstack 4.20 is of version 5.5. It's EOL is on 31 March 2017, more than 8 years ago. Which means that things will fall apart, especially with getting packages from the repository.

### Internet access from inside
Make an isolated network. Then allow all egress from that network. Voila!

### SSH from outside
Go to the Source NAT and enable firewall (because it's default deny).
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514213315.png)

Then port forward the port 22 (SSH) into the instance
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514213353.png)

We need to use yum to install OpenSSH, but it's not gonna work because it's past EOL and the repo links are dead. So we have to configure it:
```bash
sudo nano /etc/yum.repos.d/CentOS-Base.repo
```
Look for the every section, like `[base]` and uncomment the lines starting with `baseurl` by removing the leading `#` from the **baseurl line**. Comment out all mirrorlist lines aswell.
Update all instances of `http://mirror.centos.org/` to `http://archive.kernel.org/centos-vault/`
and replace $releasever into the release version (in this case 5.5).
https://stackoverflow.com/a/73817913


Sidenote: use this if somehow by some reason you accidentally uninstalled yum (which i did):
```bash
rpm -Uvh http://archive.kernel.org/centos-vault/5.5/os/x86_64/CentOS/yum-fastestmirror-1.1.16-14.el5.centos.1.noarch.rpm http://archive.kernel.org/centos-vault/5.5/os/x86_64/CentOS/yum-3.2.22-26.el5.centos.noarch.rpm http://archive.kernel.org/centos-vault/5.5/os/x86_64/CentOS/yum-updatesd-0.9-2.el5.noarch.rpm
```

Then install OpenSSH:
```bash
sudo yum update
sudo yum install openssh-server openssh-clients
```

- https://phoenixnap.com/kb/how-to-enable-ssh-centos-7
```bash
sudo service sshd start
sudo service sshd status
```
Then you can ssh into the instance!

### Web service running on instance
- https://www.redhat.com/en/blog/webserver-use-https
```bash
sudo yum install httpd mod_ssl
```
Fill in the blanks:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514232040.png)

Verify the files were created:
```bash
ls -l /etc/pki/tls/private/ /etc/pki/tls/certs/
```
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514232204.png)

Make a directory from which the webserver will serve the web content:
```bash
mkdir /var/www/https
echo "Hello, World" > /var/www/https/index.html
```

Modify the /etc/httpd/conf.d/ssl.conf file to configre httpd to use the keys and the directory (scroll down a bit):
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514232952.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514233206.png)

Start the httpd service
```bash
sudo service httpd start
netstat -tlpn
```
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514233249.png)

Modify the firewall to allow https traffic (or in this case, allow everything everywhere all at once because i'm lazy)
```bash
iptables -I INPUT -j ACCEPT
iptables -I OUTPUT -j ACCEPT
iptables -I FORWARD -j ACCEPT
```

Don't forget to configure the port forwarding of Cloudstack.

When browsing, there will be a warning:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250515001216.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250515001218.png)


## TODO
- httpd unable to make secure connection
- https://github.com/apache/cloudstack/issues/7568
- https://serverfault.com/questions/370931/how-to-make-permanent-changes-to-iptables-of-centos-5-5
- https://superuser.com/questions/1048221/how-to-make-windows-send-specific-traffic-to-specific-ethernet-ports
- change timezone in CentOS
- mail server https://superuser.com/questions/306163/what-is-the-you-have-new-mail-message-in-linux-unix
- install ISOs and other templates
- multiple machines in a cluster
- VPN tunnel/reverse proxy/domain name to gain access from the internet
