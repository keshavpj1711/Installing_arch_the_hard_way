# Installing Arch the Hard Way

I just want to document everything we do in arch installation.\
First I want to document the steps then overtime I will also provide explaination for the steps as to know more about what we are actually doing.

Lets get started......

First things first we need to setup our network connection.

# Setting up Network Connection

## Checking if it's connected 

- One simple way to check is using ping method, that is basically you ping to an IP.
- Other one can be using tools like `iwctl` which comes with arch.
	- It is used to make WiFi connection during your installation.
- Other way could be using `ip addr show`, if you are connected by wire it will show it under ether net connection.

## If not connected 

- First thing to do is getting the name of your WiFi adapter 
- Then getting using the `iwctl` to access the tool.
- Now next thing we need to get done is getting the networks around us 
	`station wlan0 get-networks`
- Now exit iwctl and type: 
	`iwctl --passphrase "Yourpassword" station wlan0 connect "YourNetworkName"`
- Then check for connectivity using the steps mentioned above.

# Partitioning those disks (getting ready)

This is where we will be defining all the the steps to partition our disk for our very own system to be installed.

## Getting to know where to install

- type `lsblk` this gives you all the disks and partitions already present.
- We will be using cfdisk to partition our usable disk.

## Partitioning the disk

- Use `cfdisk /dev/your_selected_disk` to select the disk on which we will be working on.
- Now using fdisk we need to create mainly 3 partitions:
	- Root partition
	- EFI partition
	- Swap partition
- Like this
	![](./images/Pasted%20image%2020240513193338.png)
- Now our next task is **formatting our partitions** 

### Formatting our partitions

- Formatting our EFI Partition
	- `mkfs.fat -F32 /dev/partition_name`
- Formatting root partition
	- `mkfs.ext4 /dev/partition_name`
- Formatting swap partition
	- `mkswap /dev/partition_name`
This part is now completed and we can move onto mounting and creating required directories.
Just for confirmation: 
![](./images/Pasted%20image%2020240513194254.png)

### Mounting disks 

Now mount your root partition and make directory `/boot`
- Mounting root partition: 
	- `mount /dev/partition_name /mnt`
- Make directory:
	- `mkdir /mnt/boot`
Next mounting the boot partition: `mount /dev/partition_name_efi /mnt/boot`
Enabling the swap partition: `swapon /dev/partition_name_swap`

After mounting everything correctly it should look like this:
![](./images/Pasted%20image%2020240513195014.png)

# Installing Arch and all the necessary packages

To install arch and essential packages use:
`pacstrap -i /mnt base base-devel linux linux -firmware git sudo fastfetch bpytop amd-ucode nano neovim vim bluez bluez-utils networkmanager`

> After this is done and we boot from the main drive we need to tell the system to mount these partitions to the same location and that is done by **generating `fstabfile`**

# Generating File System Table(fstab)

To generate fstab: `genfstab -U /mnt >> /mnt/etc/fstab`
To check all the partitions and their mounting point are written correctly: 
	basically opening the fstab file: `cat /mnt/etc/fstab`

Now next thing we need to do is enter into the drive where we installed arch, to do that we do: `arch-chroot /mnt`
After using our above command we have actually entered arch as a root user.

