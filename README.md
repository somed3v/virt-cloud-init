# virt-cloud-init

Script for preparing [cloud](https://github.com/canonical/cloud-init) init images for [virt-manager](https://virt-manager.org/)

## Description

This is a bash script that downloads a cloud init Debian image, resizes it, generates an iso image based on *cloud-init.yml* config, then runs it using virt-manager. Once created, the VM will also be available in your [Virtual Machine Manager](https://virt-manager.org/) GUI.

The script itself is designed to work in the directory where you plan to store iso and images for your VMs. It will create the following directories when all procedures are run:

```text
cloud/
disk/
downloads/
virt-cloud-init.sh
cloud-init.yml
```

- The *cloud/* folder will store all the *.iso* file that were generated based on the *cloud-init.yml* file.
- The *disk/* folder will store disk images of your VMs.
- The *downloads/* folder will store all the downloaded *.qcow2* images.

Currently, the script can be customized using argument flags. Other customization will be added in the future.

Before a run, edit the *cloud-init.yml* file according to your needs. Examples can be found at [cloud inits GitHub repo](https://github.com/canonical/cloud-init/tree/main/doc/examples).

## Script arguments

To view script's instructions, run:

```bash
./virt-cloud-init.sh --help
```

You will get the following text

```text
virt-cloud-init script v0.5.0
ABOUT
Cloud init image preparation tool for virt and virt-manager

SYNTAX
./virt-cloud-init.sh [download|prepare|create|regenerate-cloud-init|all] [-h] [-n|o|m|s|c|net|img|u|i|b] [ARG]

COMMANDS
download               Download iso
prepare                Prepare image and cloud-init iso
create                 Create VM with cloud-init iso (optionally can run)
regenerate-cloud-init  Cleans VM disk off cloud-init, regenerates the iso
all                    Run All commands above consecutively

OPTIONS
-h --help           Print this Help.
-n --name           Specify VM and image name prefix. Default: default-vm
-o --os             Specify OS variant. Default: debian13
-m --memory         Specify VM memory (in MiB). Default: 2048
-s --storage        Specify VM images size (in K|M|G). Default: 16G
-c --cpus           Specify CPU numbers. Default: 2
-net --network      Specify Network name for VM. Default: default
-if --interface     Specify Network interface for VM. Default: enp1s0
-dhcp --dhcp        Flag for using DHCP on Network, else it will set static IP for VM. Default: false
-ip --ip            Specify Network IP for VM. Default: 10.200.0.10
-mask --mask        Specify Network MASK for VM. Default: 24
-gw --gateway       Specify Network GW for VM. Default: 10.200.0.1
-dns --dns          Specify Network DNS server for VM. Default: 1.1.1.1
-img --image-index  Specify a known config listed in images.ini. By default asks dynamically.
-u --url            Specify custom url to an .qcow2 image. Default: https://cloud.debian.org/images/cloud/trixie/latest/debian-13-generic-arm64.qcow2
-i --interactive    Flag to attaching console upon VM start (also boots the VM).
-b --boot           Flag for booting VM after creation.
```

Upon every run, you get the following interactive prompt:

```text
0) debian13 debian-13-generic-arm64.qcow2
1) debian12 debian-12-genericcloud-amd64-daily.qcow2
2) debian11 debian-11-generic-amd64.qcow2
3) debian10 debian-10-generic-amd64.qcow2
4) ubuntu23.04 lunar-server-cloudimg-amd64.img
5) ubuntu22.04 jammy-server-cloudimg-amd64.img
6) ubuntu20.04 focal-server-cloudimg-amd64.img
7) ubuntu18.04 bionic-server-cloudimg-amd64.img
8) fedora37 Fedora-Cloud-Base-37-1.7.x86_64.qcow2
9) fedora36 Fedora-Cloud-Base-36-1.5.x86_64.qcow2
[✓] fedora37 Fedora-Cloud-Base-37-1.7.x86_64.qcow2 selected
```

**Note:** Ubuntu support is WIP

## Examples

Download default Debian 11 image:

```bash
./virt-cloud-init.sh download
```

Change VM image name to "my-vm", resize it to 32GB, use DHCP and generate a cloud image iso with config based on `cloud-init.yml`:

```bash
./virt-cloud-init.sh prepare -n my-vm -s 32 --dhcp
```
Change VM image name to "my-vm", set custom static IP, gateway and DNS server, and generate a cloud image iso with config based on `cloud-init.yml`:

```bash
./virt-cloud-init.sh prepare -n my-vm -ip 10.0.0.10 -mask 24 -gw 10.0.0.1 -dns 9.9.9.9
```


<details>
<summary>ℹ Note about Virtual Machine Manager URI</summary>

By default, libvert uses `qemu:///session` URI, hence, VMs created with `virt-install` will not appear in your Virtual Machine Manager GUI. To fix this issue, export the following variable:

```bash
export LIBVIRT_DEFAULT_URI="qemu:///system"
```

More info on this issue on [StackOverflow](https://stackoverflow.com/questions/35683443/why-are-my-vms-visible-to-either-virsh-virt-manager-but-not-both)

</details>

Create VM with name "my-vm", with 4096MB of RAM and custom network

```bash
./virt-cloud-init.sh create -n my-vm -m 4096 -net isolated_net
```
Now you can start and access your VM inside Virtual Machine Manager GUI.

