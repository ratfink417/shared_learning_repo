# Introduction
LXC stands for Linux Containers. It's is a containerization technology similar to [[Virtualization/Docker]]. The manual accessed via `man lxc`, states that LXC provides resource management through control groups and resource isolation via [namespaces](Docker%20Glossary.md#Namespaces). LXC,  aims to use these new functionalities to provide a userspace container object which provides full resource isolation and resource  control for an applications or a full system.

To verify what version of LXC your current environment supports, as well as features of the kernel that LXC has access to run `lxc-checkconfig` ^8a8024

# Control Groups AKA cgroups
Cgroups are groups of resources the container utilizes. More information can be located [here](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v1/cgroups.html)