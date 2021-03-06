# NixOS-setup

## First steps
```{bash}
sudo su
echo 'nnoremap <F1> :.w !bash<CR>' > ~/.vimrc
vim ~/install-script
```

## Destination partitions setup

```{bash}
## NixOS alone
parted --script -a cylinder /dev/nvme0n1 -- mklabel gpt
parted --script -a cylinder /dev/nvme0n1 -- mkpart nixos           512MiB  -32GiB 
parted --script -a cylinder /dev/nvme0n1 -- mkpart swap linux-swap -32GiB 100% 

parted --script /dev/nvme0n1 -- mkpart ESP fat32 1MiB 512MiB
parted --script /dev/nvme0n1 -- set 3 boot on

mkfs.ext4 -L nixos /dev/nvme0n1p1
mkswap -L swap /dev/nvme0n1p2
mkfs.fat -F32 -n boot /dev/nvme0n1p3

## NixOS alongside others...
parted --script -a cylinder /dev/nvme0n1 -- mklabel gpt
parted --script -a cylinder /dev/nvme0n1 -- mkpart nixos           512MiB   192.5GiB
parted --script -a cylinder /dev/nvme0n1 -- mkpart manjaro.gnome   192.5GiB 384.5GiB
parted --script -a cylinder /dev/nvme0n1 -- mkpart manjaro.i3      384.5GiB 576.5GiB
parted --script -a cylinder /dev/nvme0n1 -- mkpart data            576.5GiB -32GiB
parted --script -a cylinder /dev/nvme0n1 -- mkpart swap linux-swap -32GiB 100% 

parted --script /dev/nvme0n1 -- mkpart ESP fat32 1MiB 512MiB
parted --script /dev/nvme0n1 -- set 6 boot on

mkfs.ext4 -L nixos /dev/nvme0n1p1
mkfs.ext4 -L data /dev/nvme0n1p4
mkswap -L swap /dev/nvme0n1p5
mkfs.fat -F32 -n boot /dev/nvme0n1p6

mount /dev/disk/by-label/nixos /mnt
mkdir -p /mnt/boot
mkdir -p /mnt/data
mount /dev/disk/by-label/boot /mnt/boot
mount /dev/disk/by-partlabel/data /mnt/data
```

## Generating configuration.nix
```{bash}
nixos-generate-config --root /mnt
vim /mnt/etc/nixos/configuration.nix
``` 

