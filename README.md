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