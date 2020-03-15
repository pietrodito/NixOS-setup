# NixOS-setup
## Partition setup

```{bash}
parted --script -a cylinder /dev/nvme0n1 -- mklabel gpt
parted --script -a cylinder /dev/nvme0n1 -- mkpart primary 512MiB -32GiB
parted --script -a cylinder /dev/nvme0n1 -- mkpart primary linux-swap -32GiB 100% 
parted --script /dev/nvme0n1 -- mkpart ESP fat32 1MiB 512MiB
parted --script /dev/nvme0n1 -- set 3 boot on

mkfs.ext4 -L nixos /dev/nvme0n1p1
mkfswap -L swap /dev/nvme0n1p2
mkfs.fat -F32 -n boot /dev/nvme0n1p3
```
