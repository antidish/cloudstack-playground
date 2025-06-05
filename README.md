# Apache Cloudstack Installation and Configuration

Cloud Computing Class of DTE FTUI - 2024/2025

Group 11:
- Annisa Ardelia Setiawan (2206059471)
- Muhammad Farrel Mirawan (2106731554)
- Andikha Wisanggeni (2106731503)
- Yehezkiel Jonatan (2006520235)

Instructor: Yan Maraden

_Note: in our examples, every password is the same as the username. This is not best practice at all. Please use strong passwords, especially in production environments._

## Table of Contents
1. [Introduction](#introduction)
2. [Environment Setup](#environment-setup)
3. [Installing Ubuntu Server 25.04](#installing-ubuntu-server-2504)
4. [Network Configuration](#network-configuration)
5. [CloudStack Installation](#cloudstack-installation)
6. [Launch Management Server](#launch-management-server)
7. [Installing Instances](#installing-instances)
8. [CentOS Instance Fun Stuff](#centos-instance-fun-stuff)
9. [TODO](#TODO)

## Introduction

![image](https://github.com/user-attachments/assets/411ecbce-7202-4d6c-a446-3b0b73925a2c)

Apache CloudStack is open-source software designed to deploy and manage large networks of virtual machines, as a highly available, highly scalable Infrastructure as a Service (laaS) cloud computing platform. It  manages pools of compute, storage, and network resources, providing an on-demand, elastic cloud computing service. Essentially, it turns existing virtual infrastructure into a cloud-based IaaS platform.  CloudStack is used by a number of service providers to offer public cloud services, and by many companies to provide an on-premises (private) cloud offering, or as part of a hybrid cloud solution.

### Architecture
![4179 011](https://github.com/user-attachments/assets/d3b63f95-893f-46f4-a242-0a61d12c71ee)

- Management Server: The central orchestrator that handles resource allocation, VM provisioning, and overall cloud operations.
- Hypervisor Hosts: The compute layer where virtual machines run, supporting hypervisors like KVM, VMware, and XenServer.
- Primary Storage: Direct storage for virtual machines, ensuring fast access to disk resources.
- Secondary Storage: Stores templates, ISO images, and VM snapshots, shared across multiple zones.
- CloudStack UI/API: The interface for users and administrators to interact with the cloud, offering both a web-based UI and RESTful API.

## Environment Setup
We used a Dell Latitude 3500 laptop for Cloudstack management and agent provided by our instructor, Yan Maraden. 

### Hardware Configuration
| Component | Specification           |
| --------- | ----------------------- |
| CPU       | Intel Core i5 (8th Gen) |
| RAM       | 24 GB                   |
| Storage   | 250 GB SSD              |
| Network   | Ethernet 100 Gb/s       |
| OS        | Ubuntu Server 25.04     |

### Network Configuration
| Configuration | Value                         |
| ------------- | ----------------------------- |
| Network       | 192.168.1.0/24                |
| Host IP       | 192.168.1.11                  |
| Gateway       | 192.168.1.1                   |
| Management IP | 192.168.1.11                  |
| Public IP     | 192.168.1.201 - 192.168.1.210 |
| System IP     | 192.168.1.211 - 192.168.1.220 |

## Installing Ubuntu Server 25.04
- We used a flash drive to install Ubuntu Server.
- Create a bootable drive of Ubuntu Server onto the flash drive.
- Boot into it.
- On the GRUB menu, click Try or Install Ubuntu Server

### Choosing Language
Language: default English
![image](https://github.com/user-attachments/assets/4fc70e51-d359-491c-a670-bd7752d079f7)

### Choosing Keyboard
Keyboard layout: default English (US)
![image](https://github.com/user-attachments/assets/5e451bed-037d-4026-9a63-ffc584553ea4)

### Installation Type
The type of installation: default Ubuntu Server, not Ubuntu Server (minimized). And choose Search for third-party drivers.
![image](https://github.com/user-attachments/assets/76ea7ef6-f520-4c3d-b621-2d944a41aa53)

### Internet Connection
Network configuration for internet connection: We used a wired connection.
```
DHCPv4  192.168.1.3/24
```
![image](https://github.com/user-attachments/assets/1ea6da4b-dd3d-412f-a69d-234a2880d3ec)

### Proxy configuration
We used no proxies, we left it blank.
![image](https://github.com/user-attachments/assets/cc2290c6-c289-4b45-9a7b-1dc906463f9b)

### Ubuntu Archive Mirror Configuration
We used the default mirror address.
```
http://id.archive.ubuntu.com/ubuntu/
```
Make sure the mirror location passes the connectivity test.
![image](https://github.com/user-attachments/assets/7a03c312-8503-456e-bdf5-caae77380955)


### Guided Storage Configuration
In guided storage configuration. We used the entire disk and set it up as an LVM group (default). We used no encryption (LUKS).
![image](https://github.com/user-attachments/assets/3f56d829-8a7b-4136-8f10-ecf817ef2e77)

### Storage Configuration
We extended the ubuntu-lv to take up the entire space of the ubuntu-vg, because it defaults to 100G while there's 235.421G of total space on the disk. We leave everything else as default. Confirm destructive action.
![image](https://github.com/user-attachments/assets/a2d72c50-1c72-45db-955f-323a64da5135)
![image](https://github.com/user-attachments/assets/0ccce91d-69f4-48ef-b32c-97afa5af4332)

### Profile Configuration
Enter your username and password.
![image](https://github.com/user-attachments/assets/159bc0af-66c0-4d49-a13e-f02f30e3787e)

### SSH configuration
We choose to install the OpenSSH server package so that we don't need to install it later.
![image](https://github.com/user-attachments/assets/dd71e02f-e4f2-4b0b-9020-acc56500fd89)

### Third-Party Drivers
We have no applicable third-party drivers available, but choose to install them if you have.
![image](https://github.com/user-attachments/assets/e8e2e89e-17c0-46ba-bd2e-8ad243cbe9a7)

### Featured Server Snaps
We skip all of them.
![image](https://github.com/user-attachments/assets/9b6adc09-0211-4a03-bf17-a24c6cf86361)

### Installing System
![image](https://github.com/user-attachments/assets/e250a0f1-2fb6-4587-8b86-96207ee9bc1e)
After it's done: click Reboot now, remove the installation medium, then press ENTER.
![image](https://github.com/user-attachments/assets/d64d8b6f-5d6a-49a9-b3a9-dbcfacbcf268)
![image](https://github.com/user-attachments/assets/3a2ce287-e256-49b4-b455-6cfbc75304b6)

### Login
Login using the username and password.
![image](https://github.com/user-attachments/assets/64a0742a-758a-4877-afd7-0a44c3c1afb4)

> DONE! Server is now ready for SSH access.

### SSH Access
We use PuTTY to do SSH.
![image](https://github.com/user-attachments/assets/d82e8798-38c1-4e75-85d3-c59e264dd93c)


## Network Configuration
Sources:
- https://www.youtube.com/watch?v=DlJg3LYvIIs
- https://www.youtube.com/watch?v=mvuSBFGSYWI&list=PLW7vgBNPiQhmvj6dnS5ss3IdTqHCqvknm

### Edit File in Netplan
There's a file in /etc/netplan/50-cloud-init.yaml. 

```
cd /etc/netplan
sudo nano ./50-cloud-init.yaml
```
![image](https://github.com/user-attachments/assets/23517bd0-dc31-4398-bf2b-b2e9639cb0cc)

Edit that file into this (change the network address into your host IP address and the network interface aswell):
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
![image](https://github.com/user-attachments/assets/998faa0f-da56-4d00-a1a1-f039e4fffeaa)

### Applying Network Configuration
Apply the network configuration (if you change the network address, this will disconnect your SSH session and you would have to reconnect back using the new network address):
```bash
sudo netplan generate #generate config file for the renderer
sudo netplan apply  #applies network configuration to the system
sudo reboot #reboot the system
```
- `sudo netplan generate`: Creates a network configuration file for the specified renderer.  
- `sudo netplan apply`: Applies the generated network configuration to the system.  
- `sudo reboot`: Restarts the system to ensure the applied network settings take effect.  

### Checking Connection
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
![image](https://github.com/user-attachments/assets/cd6de4d5-8ca2-4f2a-889e-fa772ccc68f3)
And make sure you have internet connectivity.

### Random Installs (These may not be necessary for you)
Installing Hardware resource monitoring tools
```bash
sudo apt install htop lynx duf bridge-utils
```
- `htop` is CPU usage monitoring tool
- `duf` is Disk usage monitoring tool
- `lynx` is CLI based web browser


```bash
sudo apt install openntpd openssh-server sudo vim tar intel-microcode
```
- `openntpd` is NTP client to synchronize time between host and entire internet
- `openssh-server` is an SSH server to enable remote access
- `vim` is a text editor
- `tar` is a tool to compress and decompress file. Tar is used to decompress most of the downloaded file
- `intel-microcode` is a set of procedures written in x86 assembly to increase low level modularity


### Configure Clock
We need our server's clock to be in sync by using NTP. Use timedatectl to make sure that the time and the timezone is correct.
```bash
timedatectl set-timezone Asia/Jakarta
```
![image](https://github.com/user-attachments/assets/275121ca-ec2a-4990-81aa-2cca81b18968)

Scroll until you found your country time zone.

![image](https://github.com/user-attachments/assets/8b53bdbc-d933-43d5-8ad3-f3a08cc44124)


### Enable SSH Root Login
Ubuntu disables root password by default, so we have to make a root password to be able to login as root.
```bash
sudo passwd root
```
![image](https://github.com/user-attachments/assets/473120a7-f922-4c45-a1e1-2208ac86bfe7)


Permit SSH root login by editing the file /etc/ssh/sshd_config and restarting the ssh service.
```bash
sudo nano /etc/ssh/sshd_config
```

Find the line 'PermitRootLogin' make sure it set to 'yes'

![Pasted image 20250514111909](https://github.com/user-attachments/assets/8873fb5a-62a6-478d-9827-ab1275955127)


Then restart the ssh service.

```bash
service ssh restart
```


## Cloudstack Installation
The Controller and Compute Node is on the same host. We import cloudstack repositories key:
```bash
mkdir -p /etc/apt/keyrings 
wget -O- http://packages.shapeblue.com/release.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/cloudstack.gpg > /dev/null
sudo su
echo deb [signed-by=/etc/apt/keyrings/cloudstack.gpg] http://packages.shapeblue.com/cloudstack/upstream/debian/4.20 / > /etc/apt/sources.list.d/cloudstack.list
```
- The first step involves creating a directory to store the CloudStack public key. 
- The `wget -O` command is used to download the specified URL and pass the output to `gpg --dearmor`, which converts the ASCII-armored key into its binary format. 
- The `sudo tee` command redirects the output of to file, ensuring the key is properly stored for package authentication.


### Cloudstack List
Open the cloudstack list.
```
sudo nano /etc/apt/sources.list.d/cloudstack.list
```
![image](https://github.com/user-attachments/assets/0a7ed97a-1738-467a-9010-b5dad5a7abf0)

We use the Cloudstack current latest version, 4.20
```
deb [signed-by=/etc/apt/keyrings/cloudstack.gpg] http://packages.shapeblue.com/cloudstack/upstream/debian/4.20 /
```
![Screenshot 2025-06-05 074548](https://github.com/user-attachments/assets/1f3cf087-5730-4226-99e1-e90eddb31353)


### Installing Cloudstack and MySQL Server:
```bash
sudo apt update
sudo apt install cloudstack-management mysql-server
```
You have to update apt first to get the new repository to show up. This will take a long time (an hour or two).
![Pasted image 20250514132037](https://github.com/user-attachments/assets/448509e5-e4d2-4690-b2ff-567f60646da1)


### Configure MySQL
Open mysql config file
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
![image](https://github.com/user-attachments/assets/66e72695-aa3d-4aa4-9c3a-73a676e1fcc2)


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
![image](https://github.com/user-attachments/assets/f9bed32a-41ad-4cda-a14e-fd599a3167a4)


Restart mysql service
```
systemctl restart mysql
```
![image](https://github.com/user-attachments/assets/cca14347-46ae-4cd9-8b32-737e2ddcb537)


Check mysql service status
```
systemctl status mysql
```
There should be "active (running)" and the Status is "Server is operational".

![Pasted image 20250514133105](https://github.com/user-attachments/assets/2d9cb93b-970b-4783-a198-b54f4a4e4964)

Deploy Database as Root and Then Create "cloud11" User with Password "cloud11" too. Change the password and IP address to yours.
```bash
sudo cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:grup11 -i 192.168.1.3
```
![image](https://github.com/user-attachments/assets/2e39c8de-98ee-439e-b5e8-70b93bf21ee9)


You should get this message if you're succesful
![Screenshot 2025-06-05 044631](https://github.com/user-attachments/assets/4d611b76-ba26-425e-a801-20610fde3602)

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

![Screenshot 2025-06-05 044702](https://github.com/user-attachments/assets/61f45191-4c76-4f42-bc4f-fad0ead7cba6)

Enable LIBVIRTD_ARGS="--listen" in /etc/default/libvirtd:

![Screenshot 2025-06-05 044726](https://github.com/user-attachments/assets/f2e8b4b4-bdfb-45e0-945f-fb2cb1eff8e1)

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
```bash
sudo apt install iptables-persistent
```
![Pasted image 20250514141609](https://github.com/user-attachments/assets/84c19796-57ce-473c-9f3e-057aa23148e3)

Check the existence and content of /etc/iptables/rules.v4.

Install cloudstack-agent again. And just save the configuration into another file for safe keeping.

```bash
sudo touch /etc/iptables/rules.v4.bak
sudo iptables-save
# Save the configuration to rules.v4.bak
sudo apt install cloudstack-agent
```
![Pasted image 20250514152314](https://github.com/user-attachments/assets/84eac179-37e5-4014-a4fd-2909cefc98f4)

### Disable apparmour on libvirtd
```bash
sudo ln -s /etc/apparmor.d/usr.sbin.libvirtd /etc/apparmor.d/disable/
sudo ln -s /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/usr.sbin.libvirtd
sudo apparmor_parser -R /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper
```

## Launch Management Server
```
sudo cloudstack-setup-management
systemctl status cloudstack-management
tail -f /var/log/cloudstack/management/management-server.log #if you want to troubleshoot 
#wait until all services (components) running successfully
```

Open your web browser and connect to http://<YOUR_IP_ADDRESS>:8080.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514144731.png)
```
Username: admin
Password: password
```
You can change the password if you want.

### Continue with the installation.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514144905.png)

### Zone type: Core (default)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145118.png)

### Core zone type: Advanced (default)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145122.png)

### Zone details: fill in the Name, required DNSes, and change Hypervisor to KVM
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145257.png)

### Network
- Skip the physical network
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145349.png)
- For public traffic: reserve a range of IP addresses
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145504.png)
- For Pod: make a new Pod with a range of reserved IP addresses
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514145715.png)
- For Guest Traffic: use 3300-3399

### Add resources
- For cluster, make a new cluster
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514150621.png)
- For IP address: insert the Management Server's IP Address and use the root's login credentials
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514150233.png)
- For Primary storage:
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514150412.png)
- For Secondary storage:
  ![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514152427.png)

### Launch the zone
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514152441.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514153539.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514153540.png)
> Success! Zone is launched.

### System VM
Make sure the two system VMs are running:
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164430.png)
After this, you should be able to safely reboot the server and comeback to this web console successfully.

## Installing Instances
### ISOs and Templates 
Register ISOs onto Cloudstack, either through a URL or upload it from your computer.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514222535.png)

### TinyCore-16.0
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514223020.png)

### Ubuntu Server 25.04
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250515004002.png)
Then you can create templates based on those instances. Then, those templates can be used to create instances.

### Starting a New Instance
Go to Compute > Instances then click Add Instance
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164559.png)

#### 1: admin account
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164657.png)
#### 2: deploy on the FRIENDZONE zone.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164658.png)
#### 3: Use the default CentOS template.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164748.png)
#### 4: Use the Small Instance Compute Offering.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164749.png)
#### 5: No thanks (we just use the root disk)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514164751.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514165451.png)
#### 6: Create a new isolated network and use that.
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514175300.png)
#### 7: Nothing
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514171529.png)
#### 8: Nothing
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514171530.png)
#### 9: Name the Instance
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514171610.png)
#### Launch!
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514175321.png)
![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514175358.png)
> Successfully launched!


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
![Pasted image 20250514232204](https://github.com/user-attachments/assets/6684aad3-bdf2-4f77-83fa-fdf755c8398b)

Make a directory from which the webserver will serve the web content:
```bash
mkdir /var/www/https
echo "Hello, World" > /var/www/https/index.html
```

Modify the /etc/httpd/conf.d/ssl.conf file to configre httpd to use the keys and the directory (scroll down a bit):
![Pasted image 20250514232952](https://github.com/user-attachments/assets/945f978a-8830-4e8a-968b-baa77bbae84b)

![image](https://raw.githubusercontent.com/antidish/cloudstack-playground/refs/heads/main/assets/Pasted%20image%2020250514233206.png)

Start the httpd service
```bash
sudo service httpd start
netstat -tlpn
```
![Screenshot 2025-06-05 050352](https://github.com/user-attachments/assets/90268345-5f20-4379-9104-eb842da3190a)

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
