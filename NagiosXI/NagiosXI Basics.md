# How It Works
In the most basic setup at the most surface level, nagios clients are installed on remote hosts that reach out to the **NagiosXI Server** over _SNMP(ports 161 and 162)_ with status information that the server will use for it's reporting on the WebUI being watched by the NOC.

![](../images/Pasted%20image%2020211014103149.png)

## Ports
HTTP(s): ports 80 and 443
SNMP:    ports 161 and 162
SSH:     port 22
FTP:     port 21

## Plugin Ports
MySQL :      port 3306
PostgreSQL : port 5432

# Installation
- Install the RPM `# yum install nagiosxi`
- Finalize the installation from the WebUI `http://<server_address>/nagiosxi` This step will prompt you for the _License Key_

# System Management
The components that make NagiosXI work are:
- Nagios Core
- The Plugins
- PNP
- NDOUtils
- Core Config Manager (CCM)
- MRTG

The directories to know are:
- /usr/local/nagiosxi
- /usr/local/nagios
- /etc/nagiosql
- /store

## /usr/local/nagiosxi
This is where most of the PHP that mamke up the NagiosXI installation are kept.

![](../images/Pasted%20image%2020211014122238.png)

## /usr/local/nagios
This directory holds: binaries and configuration files amongst the other components that do the backend work in nagios such as Nagios Core and PNP.

![](Pasted%20image%2020211014122641%201.png)

## /etc/nagiosql
This folder is where that _Core Config Manager(CCM)_ component stores backups of your configuration data. These are database backups that normally won't be needed. Snapshots that you would use in most restoral procedures are kept in **/usr/local/nagiosxi/nom/checkpoints/nagioscore/**

## /store
_/store_ is where **MySQL and Postgres** backups are kept. These backups are created by a cron job that runs scripts in _/root/scripts_. This folder has 3 sub-folders
- /store/backups/mysql
- /store/backups/postgresql
- /store/backups/nagiosxi

# Log Locations
The log dirs you will want to look at are:

- /usr/local/nagios/var
- /usr/local/nagiosxi/tmp
- /usr/local/nagiosxi/var
- /var/lib
- /var/log

## /usr/local/nagios/var
The Nagios Core and PNP4Nagios logs
**nagios.log:** Includes checks, notifications, external commands and events.

**ncpd.log:** Log output from the _ncpd_ daemon (nagios interface to the PNP performance monitoring tool/plugin-thing)

**perfdata.log:**

## /usr/local/nagiosxi/tmp
eh... this just has the php mailer log and upgrade logs. I don't think it's all that important

## /usr/local/nagiosxi/var

## /var/lib
## /var/log
