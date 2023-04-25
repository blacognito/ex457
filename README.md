# Lab Setup

## 1 - Download and Install RHEL
1. Log into https://developers.redhat.com/ and download a RHEL image *(You have to create a user)*. Image used in setup: `rhel-8.5-x86_64-dvd` 
    - Newer versions can also be used, but make sure that the image version you choose has a supported version of ansible tower. 
    - Check here: https://releases.ansible.com/ansible-tower/setup-bundle/
2. When loading the image into the hypervisor make sure to create the virtual machine without the image so that you will be able to change the settings before adding the image and starting the machine. 

### 1.1 VMWare / Virtualbox
- When setting up the Linux machine it is recommended to use 2048MB of RAM and 25GB of storage.
- Before starting the machine, remember to go under network and change the adapter from *NAT* to *Bridge Adapter*. 
    - So that the Linux machine will be able to get an IP address and act as its very own machine on the network.

### 1.2 Installation
When you have started up the virtual machine just follow the on-screen installationg guide until you get to the *Installation Summary* page. Here you have to fill in some information that will be used in the installation. Information needed is listed below:

- **Localization**: 
    - Choose a *Keyboard*, *Language* and *Time & Date* 
- **Software**:
    - *Software Selection*: Choose `Workstation`
- **System**
    - *Installation Destination*: Click the hard drive and make sure *Storage Configuration* is set to `Automatic`
    - *Network and Host Name*: Choose the port and toggle the switch to `on`
- **User Settings**
    - *Root Password*: Create a root password.
    - *User Creation*: Create a user and make it administrator.

After filling in the correct information you can now continue the installation.

When you get to the *Initial Setup* page you have to accept a licensing agreement. Just click on *License Information* and accept the agreement. You can now finish the configuration and log into the machine. 


## 1.3 - Register System
The installation has now been done successfully, but we have to register the system in order to be able to receive software updates.
If you open up a terminal and execute this command: 
```
sudo dnf update
```
It will say that the system is not registered with an entitlement server. So we have to register our system. 
<br>
<br>
Open a terminal and execute the following commands to register the system. Use credentials from Red Hat account.
```
subscription-manager register --username <username> --password <password>
```
Attach subscription. Use root password when prompted.
```
subscription-manager attach --auto
```
Update repositories. Answer 'y' on all prompts. Update takes a while.
```
sudo dnf update
```

## 1.4 - Successful Installation
- RHEL Installation is now complete and it would be smart to take a snapshot of the virtual machine at this point in case you want to spin up another machine or revisit the machine at this state.
- It will also be better to work on the Linux machine using Remote SSH within VSCode (full access to terminal and files). 


## 2 - Ansible Installation

Check Python version:
```bash
python3 --version
````
If version is lower than 3.8, then update python:
```
sudo yum install python3.9
```
Install ansible:
```
pip3.9 install ansible
````
Check ansible version:
```
ansible --version
```
Install paramiko so ansible will be able to make SSH connections:
```
pip3.9 install paramiko
```

## 3 - EVE-NG Lab Setup
### 3.1 - Cisco Devices
```bash
# Router Config
conf t
hostname R2
ip domain-name cisco.com
ip ssh version 2
crypto key generate rsa modulus 1024
line vty 0 4
transport input ssh
login local
exit
username blacognito privilege 15 secret cisco
int g0/0
ip address 192.168.0.102 255.255.255.0
no shut
end
wr

# Switch config
conf t
hostname S2
ip domain-name cisco.com
ip ssh version 2
crypto key generate rsa modulus 1024
line vty 0 4
transport input ssh
login local
exit
username blacognito privilege 15 secret cisco
int vlan 1
ip address 192.168.0.100 255.255.255.0
no shut
end
wr
```
Alternative configuration:
```bash
conf t
hostname R3
ip domain-name cisco.com
ip ssh version 2
ip ssh server algorithm kex diffie-hellman-group14-sha1 diffie-hellman-group-exchange-sha1
ip ssh server algorithm encryption aes256-ctr aes192-ctr aes128-ctr aes256-cbc aes192-cbc aes128-cbc
crypto key generate rsa modulus 1024
line vty 0 4
transport input ssh
login local
exit
username blacognito privilege 15 secret cisco
vrf definition MGMT
address-family ipv4
int g0/0
vrf forwarding MGMT
ip address 192.168.0.101 255.255.255.0
no shut
end
wr
```
Log into the devices to create a fingerprint:
```bash
ssh blacognito@192.168.0.101
```
Command to troubleshoot if the login doesnt work:
```bash
ssh -vvvv blacognito@192.168.0.101
```
Might be that you have to remove an RSA key fingerprint from `~/.ssh/known_hosts`


### 3.2 - VyOS Device
```bash
# username: vyos
# password: vyos

configure
set interface ethernet eth0 address 192.168.0.104/24
commit
# 'show' to see running config

set service ssh port 22
commit

save
```


### 4 - Ansible Tower Installation

1. Navigate to this url `https://releases.ansible.com/ansible-tower/setup-bundle/` and locate the `.tar.gz` file that corresponds with the RHEL version that you are running.
2. Right-click and copy the link of the `.tar.gz` file.
3. Run this command on the RHEL machine: `wget https://releases.ansible.com/ansible-tower/setup-bundle/ansible-tower-setup-bundle-3.8.5-1.tar.gz`

4. Run this command: `tar -xzvf ansible-tower-setup-bundle-3.8.5-1.tar.gz`
5. Enter the new folder: `cd ansible-tower-setup-bundle-3.8.5-1`
6. Open the `inventory` file and add `password` as the value on all variables that contain *'password'* (Should be four variables).
    - NB! *'password'* is only used for the lab environment, please change if it is supposed to be used in a non-lab environment.
7. Run the shell script: `sudo ./setup.sh`
8. Run `ifconfig` to check your IP address. This is the IP address that you are going to use to browse to Ansible Tower.
9. Enter the IP address into a web browser and login to Ansible Tower using username: ´admin´ and password ´password´ (same password that was entered in the `inventory` file earlier.)
10. You will be prompted for a subscription. Just provide your Red Hat account credentials to receive a trial subscription.
11. You will have to log in again after the previous step. 
12. Good To Go!
