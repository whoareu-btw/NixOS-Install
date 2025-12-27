# NixOS-Install
How to install nixos manual

How to install nixos manually on EFI device (Made by wanna-know-something-man or Nyan)

 1. Iso
    First thing first, you should have nixos iso before you install nixos on your system. 
    Go to the nixos website, choose the iso (I'll use minimal iso for this tutorial). Download the iso (either x86_64 or arm64, I'll gonna use x86_64), verify the iso using SHA command :
      # sha256sum nixos.iso
      # when you go to the sha links, download it and use as like this:
         $ sha256sum nixos.iso.sha256
 	  # when the output says "OK", it's ready to use.

 2. Boootable
    Burn the file into flashdrive, you can use tools like ventoy or balena etcher. 
    If you want the fast track, use dd to burn the iso to the flashdrive. Or if you want use dd, you can use like this:
      $ sudo dd if=your-directory-nixos-iso of=flashdrive-you-gonna-use bs=4M status=progress

 3. Setup
    Boot to the flashdrive, then use lsblk command to see disk you gonna use. Use cfdisk trough the drive you gonna use, for exampe like:
      $ sudo cfdisk /dev/sda
      - then give 1G for boot partition.
      - 4G for swap (if you need swap).
      - and then root partition.

 4. Network
    if you use wifi, use nmtui command. If you use LAN just plug and play. For make sure your internet connect or not, use this command:
      $ ping -c 3 google.com
     
 5. Format disk
      $ sudo mkfs.vfat -F32 /dev/sda1
      $ sudo mkswap /dev/sda2 (if you need swap)
      $ sudo swapon /dev/sda2 (if you need swap)
      $ sudo mkfs.ext4 /dev/sda3

 6. Mounting partition
      $ sudo mount /dev/sda3 /mnt
      $ sudo mkdir -p /mnt/boot
      $ sudo mount /dev/sda1

 7. Generate config and editing config
      $ sudo nixos-generate-config --root /mnt
      $ sudo nano /mnt/etc/nixos/configuration.nix
 
      If you want use grub as your bootloader, then edit the config to this:
        
    #
	    bootloader = {
	      grub = {
            enable = true;
            device = "nodev";
		      efiSupport = true;
	        };
	      efi.canTouchEfiVariables = true;
         };
    #
      
 9. Setup hostname and user
    Still in cofiguration.nix btw
     
    # 
        networking.hostName = "name";
        users.users.haru = {
          isNormalUser = true;
          extraGroups = [ "wheel" ];  # For enable sudo
            packages = with pkgs; [   # if u need package only for this user
            tree
            ];
        };
      

 10. Install nixos to your drive
    Install by using this command:
      $ sudo nixos-install

 11. After installation
     Once you done with installation, login as root, then give ur user a password by this command:
       $ passwd name


Boom, now you got nixos on your machine.
