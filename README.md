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

# Configuring Arch

## Setting a new root password

- This can be done by using the command: `passwd`
- Now the prompt asks you to enter the new password.

## Adding a Standard User 

- A user can be added  by:

	- `useradd -m -g users -G wheel,storage,power,video,audio -s /bin/bash nightfury(yourusername)`

- Then add the password for the user: 

	- `passwd nightfury`

### Giving a user root privileges on running commands with sudo 

Now we need to edit the sudoers file by doing `visudo` to edit the file using vim.

- In the sudoers file uncomment the line which says member of group wheel to execute any command.
- For this step you must know the basic navigation of vim like getting into insert mode, making and saving changes and finally exiting it.

To finally check whether the user has been given the root privileges with sudo do:

`su - nightfury(yourusername)`

## Setting Timezone and Region

- Set according to your local timezone.

	`ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime`

- Now to sync to the set timezone type;

	`hwclock --systohc`

## Setting System Lang

- Edit a file `locale.gen` at `/etc` by:

	- `nvim /etc/locale.gen`
	- Again i would say open it with the text editor you are comfortable with.
- Uncomment the line `en_US.UTF-8 UTF-8`
- After this generate the locale by: 
	`locale-gen`
- Create a locale config file in /etc/ and add your default lang in my case English
	- `nvim /etc/locale.conf`
	- Once into the file add `LANG = en_US.UTF-8`
	- Save and exit

## Setting up host name

- Creating a file /etc/hostname 
- In the opened file just add the hostname you want to set.
	- Example: `archlinux`
	- Then save and exit

After that we need to edit the /etc/hosts file and add the following
```
127.0.0.1        localhost
::1              localhost
127.0.1.1        archlinux.localdomain      archlinux
```

# Installing Grub

Installing grub and other essentials: 

	`pacman -S grub efibootmgr dosfstools mtools`

Now we need to install grub on the /boot partition that we made earlier:

	`grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=`

>Note this will only work if you have a UEFI system

Then generate a grub config file:

	`grub-mkconfig -o /boot/grub/grub.cfg`

# Enabling Essential services

- `systemctl enable bluetooth`
- `systemctl enable NetworkManager`

# Unmounting all the partitions

- `umount -lR /mnt`

> Hurrah!! you have successfully installed Arch Linux the proper way.

After this you can remove your flash drive and carry on with ricing your system.