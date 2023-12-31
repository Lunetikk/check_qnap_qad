# check_qnap_qad
These are "quick and dirty" scripts to check a QNAP via check_by_ssh. If MIBS work for you, you should rather use them instead of these scripts...  
The scripts might work on any QNAP as long as you can execute "/sbin/getsysinfo" as "admin" via shell.  
As of today (05.07.2023) they all work fine on a "QNAP 1655" with OS "QTS 5.0.1 (20230421)"

***Prerequisites***

- You need a user (like "monitoring") to connect via ssh, to be able to execute the checkscripts and gather infos. This user should be able to connect via ssh-key (passwordless)
  
***Usage***

The script "check_qnap_qad.sh" has to be executed by "admin" because it needs elevated rights.  
Log in as "admin", create the script, make it executeable (chmod 770 check_qnap_qad.sh) and create a cronjob (crontab -e). Also add it to "/etc/config/crontab" to make it persistent, otherwise the entry will be gone after a reboot
```
# example to run the script every 5 minutes
*/5 * * * * /share/homes/admin/check_qnap_qad.sh
```

All other scripts should be placed inside the new users home (example: monitoring => /share/homes/monitoring/)  
They can then be executed via "check_by_ssh" from your monitoringserver
```
# example
# /usr/lib/nagios/plugins/check_by_ssh -H 192.168.178.123 -l monitoring -C "/share/homes/monitoring/check_infofiles.sh"
All files have been modified in the last hour.
```

Currently all scripts point to "/share/homes/monitoring/monitoring" as path for infofiles, if you want to use another user you need to edit this path which is always at the top of the script.

- check_cputemp.sh => checks CPU temperature (currently only status, no alert => see ToDos)
- check_systemp.sh => checks system temperature (currently only status, no alert => see ToDos)
- check_fans.sh => checks fan speeds, alerts if they are running to slow
- check_disks.sh => checks all available disks (except PCI), alerts if temperature is above waring and critical value, if status and SMART is bad
- check_vols.sh => checks all volumes, alerts if available free space is lower than warning and critical value, if status isnt ready
- check_infofiles.sh => checks modification date for all infofiles, alerts if older than 1 hour to indicate that something is wrong

***ToDo***

I want to change the following things:
- add more comments
- add optional variables ( -c, -w ) via script execution for "temp warning", "temp critical", "diskspace warning", "diskspace critical", ...
- currently the scripts are not checking any PCI disks (like QM2: M.2 Expansion Cards) because "/sbin/getsysinfo" doesnt provide these infos, Iam looking for a workaround
- check_cputemp.sh and check_systemp.sh are currently not checking anything, this will be fixed asap
