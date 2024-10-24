## Radeon-Profile install to control fan
***Note: This GUI returns fan to auto setting if you close the GUI for this. Recommend using the commands to set static values. When using in a VM, if you do not rebind the GPU to a driver on host after shutting down the fans will go into limp/maintance mode or use what ever the last setting is stored on the gpu when you shutdown the VM recommend to set fan to 20% as a systemd service or some sort of unit file and make it complete before any other steps such as unloading the GPU drivers, mistakes in setting this up can potentially cause slow shutdowns so we have to be careful.***

***Note: We are doing the setup on Noble-Numbat, unfortunately there is not a release file on the PPA server yet, and we have to manually add the .list file, and the key file in order to complete. My full steps below (Not required on Jammy Jellyfish or before)***


### Full Bash Script

```bash
#!/bin/bash

# Remove the existing PPA if it is there
add-apt-repository --remove ppa:radeon-profile/stable

# Install dirmngr, required for key management
apt install -y dirmngr

# Ensure the root .gnupg directory exists with the right permissions
mkdir -p /root/.gnupg
chmod 700 /root/.gnupg

# Start dirmngr to manage GPG keys
dirmngr < /dev/null

# Add the Radeon Profile PPA for Jammy (since Noble is unsupported)
add-apt-repository -S "deb https://ppa.launchpadcontent.net/radeon-profile/stable/ubuntu jammy main"

# Add the GPG key for the PPA manually
gpg --no-default-keyring --keyring /etc/apt/keyrings/radeon-profile.gpg --keyserver keyserver.ubuntu.com --recv-keys 93562F724DD47F9D

# Ensure the PPA sources list is correctly configured for Jammy
echo "deb [signed-by=/etc/apt/keyrings/radeon-profile.gpg] https://ppa.launchpadcontent.net/radeon-profile/stable/ubuntu jammy main" > /etc/apt/sources.list.d/archive_uri-https_ppa_launchpadcontent_net_radeon-profile_stable_ubuntu-noble.list
echo "# deb-src https://ppa.launchpadcontent.net/radeon-profile/stable/ubuntu jammy main" >> /etc/apt/sources.list.d/archive_uri-https_ppa_launchpadcontent_net_radeon-profile_stable_ubuntu-noble.list

# Update package lists
apt update

# Install radeon-profile
apt install -y radeon-profile
```

### How to Use:
1. Save the script as `setup-radeon-profile.sh`:
   ```bash
   nano setup-radeon-profile.sh
   ```

2. Make it executable:
   ```bash
   chmod +x setup-radeon-profile.sh
   ```

3. Run the script:
   ```bash
   sudo ./setup-radeon-profile.sh
   ```

***Visit the website for the PPA to ensure the correct keys are used - https://launchpad.net/~radeon-profile/+archive/ubuntu/stable ...***