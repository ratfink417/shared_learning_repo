# Introduction
Apt is the package manager most commonly found in Debian distros. For a detailed list of commands run `man apt` in the terminal.

# Commands
## apt-get update
`apt-get update` doesn't actually update anything other than the package catalog based upon the sources.list most commonly found in `/etc/apt/sources.list`. Additional source lists are often located in the `/etc/apt/sources.list.d/` directory. After running this command it will usually inform you as to how many packages are out of date.

## apt-get upgrade
`apt-get upgrade` is the command to actually install any updates found after running `apt-get update`. Often  ran together as `apt-get update && apt-get upgrade`

## apt-get dist-upgrade
`apt-get dist-upgrade` stands for distro upgrade. It upgrades all packages as well as any major OS components such as the Linux kernel to the latest version for the distro that is specified in the sources.list.

## apt-get auto-remove
`apt-get auto remove` is used to clean and remove any packages that are still installed but are no longer needed by the system. Its ideal to run this after a large amount of updates or a full [distro upgrade](Apt%20Package%20Manager.md##apt-get%20dist-upgrade).
# apt-get update issues.
## apt-get update Warnings
## apt-get update Failures
### - Suite Value changed from 'stable' to 'old stable'
This issue is due to a version release change when a distro has exited from active development to long term support. The suite is now regarded as old stable instead of stable. This error is warning you of the change in order to proceed you will need to run:

`apt-get update --allow-releaseinfo-change`

You only need to run this command once after which you can proceed with Apt-get update as normal.
#### Occurrences
- This issue was experienced when running [Proxmox](Proxmox%20is%20Hard.md) 6.3.1 and trying to upgrade to 6.4-13. Version 6.4 was needed facilitate [upgrading to PVE 7](Proxmox%20is%20Hard.md#Upgrading%20Proxmox%20from%206.x-x%20to%207.0).


