# NixOS install guide for EFI based system

##  1.  Prepare NixOS iso and verify the iso

    Visit nixos website and download minimal iso and checksum and then verify with this command:
    
    $ sha256sum -c nixos.iso.sha256

##  2.  Create bootlable flashdrive with dd
    
    $ dd if=~/Downloads/nixos.iso of=/dev/sdb bs=4M status=progress

##  3.  Create partition disk with cfdisk inside liveiso
    
    (a random disks size)

    # cfdisk /dev/disk-target
    1G with EFI flag
    4G with Linux swap flag
    248G with Linux root flag
    248G with Linux home flag

##  4.  How to format disk

    # mkfs.vfat -F32 /dev/sda1
    # mkswap /dev/sda2
    # swapon /dev/sda2
    # mkfs.btrfs /dev/sda3
    # mkfs.btrfs /dev/sda4

##  5.  Mounting disk
    
    # mount /dev/sda3 /mnt
    # mount -m /dev/sda1 /mnt/boot
    # mount -m /dev/sda4 /mnt/home

##  6.  Generate NixOS configuration

    # nixos-generate-config --root /mnt

##  7.  Basic `configuration.nix` 

    (example)

    {
      boot.loader = {
        grub = {
          enable = true;
          device = "nodev";
          efiSupport = true;
        };

        efi.canTouchEfiVariables = true;

        kernelPackages = pkgs.linuxKernel.packages.linux_6_12;
      };

      networking.networkmanager.enable = true;

      networking.hostName = "snowflake";

      users.users.nix = {
        isNormalUser = true;
        extraGroups = [ "wheel" ];
      };

      environment.systemPackages = with pkgs = [
        vim
        fastfetch
        btop
      ];

      zramSwap = {
        enable = true;
        priority = 60;
        algorithm = "zstd";
        memoryPercent = 50;
      };
    }

##  8.  Installation time

    # nixos-install

##  9.  After installation

    After installation, you can change your user password with:

    # nixos-enter --root /mnt -c 'passwd nix'

    And then reboot.


Now you have NixOS on your system, enjoy your NixOS journey~

Nyan~
