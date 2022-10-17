# Changing a VM ID in LVM
This is useful when the VM sits on a LVM or LVM-Thin storage pool within Proxmox and you need to change the ID. This script will prompt for the old VMID and then the new VMID. It automatically finds the vgname by identifying it from the old vmid.

```
#!/bin/bash

echo Put the VMID to change
read oldVMID
case $oldVMID in
    ''|*[!0-9]*)
        echo bad input. Exiting
        exit;;
    *)
        echo Old VMID - $oldVMID ;;
esac
echo
echo Put the new VMID
read newVMID
case $newVMID in
    ''|*[!0-9]*)
        echo bad input. Exiting
        exit;;
    *)
        echo New VMID - $newVMID ;;
esac
echo
vgNAME="$(lvs --noheadings -o lv_name,vg_name | grep $oldVMID | awk -F ' ' '{print $2}' | uniq -d)"
case $vgNAME in
    "")
        echo Machine not in Volume Group. Exiting
        exit;;
    *)
        echo Volume Group - $vgNAME ;;
esac

for i in $(lvs -a|grep $vgNAME | awk '{print $1}' | grep $oldVMID);
do lvrename $vgNAME/vm-$oldVMID-disk-$(echo $i | awk '{print substr($0,length,1)}') vm-$newVMID-disk-$(echo $i | awk '{print substr($0,length,1)}');
done;
sed -i "s/$oldVMID/$newVMID/g" /etc/pve/qemu-server/$oldVMID.conf;
mv /etc/pve/qemu-server/$oldVMID.conf /etc/pve/qemu-server/$newVMID.conf;

echo Ta-Da!
```
Make a new file on the hypervisor with a text editor such as vim [vim](vim.md): ``vim change-id.sh``. This will create a new script file in the current directory. Paste the contents above, press `:` to bring up the commands then save (`w`) and exit (`q`). Then run [[Chmod]]``+ x change-id.sh`` to make it executable. Run the script with 

# Changing a VM ID in ZFS
This is useful when the VM sits on a ZFS storage pool within Proxmox and you need to change the ID. This Script takes two arguments. The Old VM ID and the New VM ID. 

```
#!/bin/bash

if [[ $# != 2 ]]; then
  cat <<-END >&2
usage: $0 old-id new-id
END
  exit 1
fi

old_id=$1
new_id=$2

if ! disks=$(zfs list -r -o name $POOL/data | grep "vm-${old_id}-disk"); then
  echo "did not find any disks, check old vm id and running zfs" >&2
  exit 1
fi

for disk in $disks; do
  new_disk=$(echo $disk | sed "s/vm-${old_id}-disk/vm-${new_id}-disk/g")
  zfs rename $disk $new_disk
done

sed -i "s/vm-${old_id}-disk/vm-${new_id}-disk/g" /etc/pve/qemu-server/${old_id}.conf
mv /etc/pve/qemu-server/${old_id}.conf /etc/pve/qemu-server/${new_id}.conf
```
Make a new file on the hypervisor with a text editor such as vim [vim](vim.md): ``vim change-id.sh``. This will create a new script file in the current directory. Paste the contents above, press `:` to bring up the commands then save (`w`) and exit (`q`). Then run [[Chmod]]``+ x change-id.sh`` to make it executable. Then `.\change-id.sh <old-id> <new-id>`.
# Remove the Login No subscription warning

When logging into proxmox without an Enterprise Subscription you will get warned about not having a valid subscription every time. It gets rather annoying so this one lineer will modify the proxmoxlib.js file that tells the UI to post a warning if no subscription is there and backs it up. If you do a distro update you will probably have to run this command again.

```
sed -Ezi.bak "s/(Ext.Msg.show\(\{\s+title: gettext\('No valid sub)/void\(\{ \/\/\1/g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy.service
```

# Applying a theme
Themes aren't natively supported in proxmox but there are some people out there that have made some scripts to customize the look to make it a little more appealing such as [Lunar Computer](https://lunar.computer/posts/themes-proxmox/) 

The [Weilbye PVE Discord Dark](https://github.com/Weilbyte/PVEDiscordDark/).  is a preferred favorite. 

# Upgrading PBS 2.04 to 2.1
PBS 2.04 was still a beta release in most ways. There have been some enhancements made with PBS 2.1. One of the biggest is that while still running *bullseye* it is now using Linux kernel 5.13 and OpenZFS 2.1.
### Actual no-subsciption repository
Previously with 2.04 there was no repository specifically if you did not have a subscription. When upgrading you will need to update `/etc/apt/sources.list` to include the no subscription repository:
```bash
deb http://ftp.debian.org/debian bullseye main contrib
deb http://ftp.debian.org/debian bullseye-updates main contrib

# PBS pbs-no-subscription repository provided by proxmox.com,
# NOT recommended for production use
deb http://download.proxmox.com/debian/pbs bullseye pbs-no-subscription

# security updates
deb http://security.debian.org/debian-security bullseye-security main contrib

```
	