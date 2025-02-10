# Creating Ubuntu 22.04 VM template

## Create VM
- No installation Media
- Ensure qemu-guest-agent is enabled
- No hard disk

## Add CloudInit drive after VM creation
Hardware > Add > CloudInit Drive
Ensure of the following: 
- Username set
- Password set
- SSH pub keys added
- IP config => DHCP

Click `Regenerate Image` once changes have been made.


## Download cloud image
```sh
wget https://cloud-images.ubuntu.com/minimal/releases/jammy/release/ubuntu-22.04-minimal-cloudimg-amd64.img
```

## Ensure VGA output is enabled on VM
```sh
qm set 902 --serial0 socket --vga serial0
```

## Rename the image file to a `.qcow2` format
```sh
mv ubuntu-22.04-minimal-cloudimg-amd64.img ubuntu-22.04.qcow2
```

## Resize the image
```sh
qemu-img resize ubuntu-22.04.qcow2 32G
```

## Modify the image
```sh
# Ensure libguestfs-tools are installed
apt-get install libguestfs-tools

# Install qemu-guest-agent
virt-customize -a ubuntu-22.04.qcow2 --install qemu-guest-agent

# Change timezone
virt-customize -a ubuntu-22.04.qcow2 --timezone Pacific/Honolulu

# Remove machine-id
virt-customize -a ubuntu-22.04.qcow2 --run-command "echo > /etc/machine-id; ln -sf /etc/machine-id /var/lib/dbus/machine-id"

# Other examples
# Edit ssh config
# virt-edit ubuntu-22.04.qcow2 /etc/ssh/sshd_config

# Edit cloud config, for say enabling root
# virt-edit ubuntu-22.04.qcow2 /etc/cloud/cloud.cfg
```

## Import disk into VM
```sh
qm importdisk 902 ubuntu-22.04.qcow2 local-lvm
```

## Add the disk to the VM in the web GUI
Go to the VM > Hardware > Click on the Unused Disk > Edit
For SSDs, make sure `discard` and `SSD emulation` are enabled

## Change the boot order
Under VM > Options > Boot Order: 
Make sure the newly imported disk is enabled and drag it to priority #2 (just below the CD ROM). 

## Convert to template
Right click the VM > Convert to template

## Post clone stuff
### Install qemu-guest-agent
```sh
sudo apt install qemu-guest-agent
# Press enter to accept the defaults
```
By default, the qemu-guest-agent will have a status of inactive (dead). The VM just needs to be rebooted and it should be working. 

