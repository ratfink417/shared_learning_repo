[Back to Index](Index.md)
# Introduction:
Proxmox is a Debian based Virtualization Platform that offers virtualization in two forms QEMU/KVM and LXC. QEMU and KVM are different technologies but Proxmox refers to them as QEMU/KVM because of how it utilizes the technologies together.

The Proxmox [Admin-Guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.html) has an excellent graphic representing the elements within Proxmox seen here: ![](/Images/Proxmox-Stack.svg)

## QEMU vs KVM

To understand  the main difference, one is a Type-2 hypervisor and the other is Type-1.

	- Type-1 is a native or bare metal hypervisor that’s running directly on the host’s hardware
	- Type-2 is a hosted hypervisor, which means that it runs on a conventional operating system just as other computer programs would

**QEMU** is short for Quick emulator. It is a type-2 hypervisor that emulates all aspects of a computer's CPU to create a virtualized environment. Think of VirtualBox running on Windows or MacOS. The main issue with QEMU is the Host CPU and the virtualized one have no direct connection to the HOST hardware so every virtual process is emulated by the CPU which uses a huge amount of processing resources.

**KVM** is the Linux Kernel Virtualization Module. It is considered a type-1 hypervisor, meaning it is a full virtualization solution. It can utilize x86 virtualization extensions such as Intel-VT or AMD-V within the Host's CPU. Paired with these extensions, KVM provides the ability for a portion of the physical CPU to be directly mapped to the virtual CPU. This means that information can be passed and executed directly on the physical CPU instead of being emulated.

## QEMU/KVM
Proxmox utilizes both technologies in its virtualization solution. As seen in the graphic above. QEMU hosts and Manages the VMs and KVM interfaces with the Linux Kernel.

# PCI Pass-through and the Joys of IOMMU
I don't know why this is, but for whatever reason none of the virtualization technologies on your motherboard nor the ability to pass PCI devices to your VMS is inherently turned on by default.

## How to enable IOMMU
#PLACE-HOLDER-FOR-CONTENT

## Passing Hard drives to a VM
There are a few reasons why you'd want to pass a hard drive directly to a VMs, but there are roughly two ways to do it one is relatively easy but with certain limitations and the other well...is not....😒

### LUN-Passthrough
The logical volume method simply points to the device id as it is located on the hypervisor. Simply add the device to the conf file file the 
>  `-scsi<X> /dev/disk/by-id/<hard drive serial id>`

### PCI Passthrough Method
#PLACE-HOLDER-FOR-CONTENT

# Backing up VMs
Backing up virtual machines is, according to most forums, a difficult task or at least it was. Proxmox now has a special distro called the Proxmox Backup Server or PBS for backing up VMs, Containers, and Linux Hosts. Refer to [[Proxmox-BACKUP!BEEP!BEEP!]] for all things related to that subject.

# Creating VMs
VM creation is relatively simple. In the upper right hand corner there are 4 buttons: s

# Converting an OVA to work on Proxmox
For reasons unbeknownst to myself or anyone else on the internet, Proxmox as of current version 7.0-13 STILL does not have native support for creating a VM from an OVA file using the GUI or easily with the CLI.

# Upgrading Proxmox from 6.x-x to 7.0
Starting in version 6.4-X a commandline tool called pve6to7 was introduced to help facilitate upgrades to version 7. Running `pve6to7 --full` will run a full upgrade check to determine any issues or compatibility problems with upgrading from version 6 to 7.

**Refer to the [UPGRADE NOTES](Proxmox%20is%20Hard.md###UPGRADE%20NOTES) section for any additional details on issues with pve6to7 and how to mitigate them before proceeding.**

After running a [apt-get update](Apt%20Package%20Manager.md##apt-get%20update) and [apt-get upgrade](Apt%20Package%20Manager.md##apt-get%20upgrade) it may list several applications that were not upgraded or skipped:

```
root@proxmox:~# apt-get upgrade
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  libproxmox-acme-perl libpve-storage-perl pve-container pve-kernel-5.4 qemu-server
0 upgraded, 0 newly installed, 0 to remove and 5 not upgraded.
```

It is highly advised to also run a [distribution upgrade](Apt%20Package%20Manager.md##apt-get%20dist-upgrade) as well to install these packages listed above.

After running a final check update the sources list from Buster to Bullseye with `sed -i 's/buster\/updates/bullseye-security/g;s/buster/bullseye/g' /etc/apt/sources.list` to update any repo in the list.

Update the enterprise list with ``echo "#deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise" > /etc/apt/sources.list.d/pve-enterprise.list``. The ``#`` in front will still keep it from updating if we don't have an enterprise license

Update ceph repositories with ``echo "deb http://download.proxmox.com/debian/ceph-octopus bullseye main" > /etc/apt/sources.list.d/ceph.list``

Then run the commands to  [apt-get update](Apt%20Package%20Manager.md##apt-get%20update)  and verify all the urls say "bullseye" and **NOT** "buster" if all is good and `pve6to7` has no other warnings of concern,  go ahead and preform the [distribution upgrade](Apt%20Package%20Manager.md##apt-get%20dist-upgrade). 


Once everything has been fetched and downloaded you will get a final warning:
```
W: (pve-apt-hook) !! ATTENTION !!
W: (pve-apt-hook) You are attempting to upgrade from proxmox-ve '6.4-1' to proxmox-ve '7.0-2'. Please make sure to read the Upgrade notes at
W: (pve-apt-hook)       https://pve.proxmox.com/wiki/Upgrade_from_6.x_to_7.0
W: (pve-apt-hook) before proceeding with this operation.
W: (pve-apt-hook) 
W: (pve-apt-hook) Press enter to continue, or C^c to abort.
```

### UPGRADE NOTES
Ideally Proxmox wants you to shut down all VMs and CTs before proceeding; however, since this hypervisor is running my router it wont be able to preform the update without internet access. I disabled all other VMs but the pFSense one never went down and I never lost internet during the entire process never the less i wouldn't have even considered this without having a [backup](Proxmox-BACKUP!BEEP!BEEP!.md) of the host and the VMs/LXCs first.

Any [[LXC]] Containers configurations that specify a cgroup will need to be renamed to cgroup2 because  PVE7 now supports cgroup v2.0

If you have applied any [themes](Proxmox%20Customized.md#applying%20a%20theme)  or other customizations they such as [ disabling the no subscription alert](Proxmox%20Customized.md#Remove%20the%20Login%20No%20subscription%20warning) they will need be reapplied after you are done updating.

[Back to Index](Index.md)