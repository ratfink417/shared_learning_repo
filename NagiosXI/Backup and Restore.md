# Backing Up
NagiosXI comes with a backup script which will save copies of these things:
- Nagios Core files _/usr/local/nagios/_
- NagioxXI files _/usr/local/nagiosxi/_
- NagiosQL files _/var/www/html/nagiosql/_ and _/etc/nagiosql_
- MRTG files _/var/lib/mrtg/_ and _/etc/mrtg/_
- NRDP files _/usr/local/nrdp/_
- NagVis files _/usr/local/nagvis/_
- CRON files _/var/spool/cron/apache/_
- Apache config files _/etc/httpd/conf.d/_
- logrotate confisg _/etc/logrotate.d/_
- MySQL DB's (nagios, nagiosql, nagiosxi)
- PostgresQL DB's (nagiosxi)

The backups will be stored in _/store/backups/nagiosxi/_ in a timestamped .tar.gz file that will look something like `1479858002.tar.gz`

**NOTE:** Before you begin, make sure the _/store_ partition has plenty of space.

**NOTE:** The backup will trigger a restart of the nagios service

**NOTE:** The backup script expects the default password of _nagiosxi_ if you have changed this, then you will need to reflect that change in the script

The backup script you will be using is located at **/usr/local/nagiosxi/scripts/backup_xi.sh**

# Restoring From Backup
The restore script _according to the documentation_(https://assets.nagios.com/downloads/nagiosxi/docs/Backing-Up-And-Restoring-Nagios-XI.pdf) can be used for migrations across RHEL versions.

