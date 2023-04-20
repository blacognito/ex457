# Lab Setup

## 1 - Download and Install RHEL
1. Log into Red Hat and download image. Image used in setup: `rhel-8.5-x86_64-dvd` 
    - Newer versions can also be used, but make sure that the image version you choose has a supported version of ansible tower. 
    - Check here: https://releases.ansible.com/ansible-tower/setup-bundle/
2. When loading the image into the hypervisor make sure to create the virtual machine without the image so that you will be able to change the settings before adding the image and starting the machine. 

<br/>

### Installation Summary
- <u>*Software Selection:*</u>
    Workstation
- <u>*Installation destination:*</u>
Choose the disk and make sure storage configuration is set to **'automatic'**
- <u>*Network & Hostname:* </u> Make sure the ethernet is toggled on
- *Create root password.*
- *Create user and make sure it is administrator.*

<br/>

## 2 - Register System
Eexcute these commands after successful installation of RHEL. 
```bash
sudo dnf update
```

Run this command to register the system. Use credentials from Red Hat account.
```
subscription-manager register --username <username> --password <password>
```

Attach subscription.
```
subscription-manager attach --auto
```
Update repositories. Answer 'y' on all prompts. Update takes a while.
```
sudo dnf update
```


## 3 - Ansible Installation

```bash
# Check Pythonn version
python3 --version

# If version is lower than 3.8, then update python.
sudo yum install python3.9

# Install ansible
pip3.9 install ansible

#Check ansible version
ansible --version

# Install paramiko
pip3.9 install paramiko
```

## 4 - EVE-NG Lab Setup
<br>

### 4.1 - Cisco Devices
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


### 4.2 - VyOS Device
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


### 5 - Ansible Tower Installation

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
