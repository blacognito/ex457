# Lab Setup

## #1 - Download and Install RHEL
1. Log into Red Hat and download image. Image used in setup: `rhel-8.5-x86_64-dvd` 
    - Newer versions can also be used, but make sure that the image version you choose has a supported version of ansible tower. 
    - Check here: https://releases.ansible.com/ansible-tower/setup-bundle/
2. When loading the image into the hypervisor make sure to create the virtual machine without the image so that you will be able to change the settings before adding the image and starting the machine. 

<br/>

#### **Installation Summary**
- <u>*Software Selection:*</u>
    Workstation
- <u>*Installation destination:*</u>
Choose the disk and make sure storage configuration is set to **'automatic'**
- <u>*Network & Hostname:* </u> Make sure the ethernet is toggled on
- *Create root password.*
- *Create user and make sure it is administrator.*

<br/>

## #2 Register System
Eexcute these commands after successful installation of RHEL. 
```bash
sudo dnf update

# Use credentials from Red Hat account.
# Register system
subscription-manager register --username <username> --password <password>

# Attach subscription.
subscription-manager attach --auto

# Update repositories.
# Answer 'y' on all prompts. Update takes a while.
sudo dnf update
```


## #3 Ansible Installation

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

## #4 EVE-NG Lab Setup
<br>

### #4-1 - Cisco Devices
```bash
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