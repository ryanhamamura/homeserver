# Creating Ubuntu 22.04 VM template

## Create VM
- No installation Media
- Ensure qemu-guest-agent is enabled
- No hard disk

## Add CloudInit drive after VM creation
Hardware > Add > CloudInit Drive

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

