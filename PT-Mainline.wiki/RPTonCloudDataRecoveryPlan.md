This documents RPT on Cloud Data Recovery Plan as part of [GDPR](https://jira01.hclpnp.com/browse/TP-51102).  Contact: Richard Ptasnik.

## Goals
* The backup data should occur automatically
* Use different credentials then the main production databases
* Replicate at different intervals to avoid propagating deletions
* Describe the exact steps required to restore data 
* A way to test/audit if the plan is working

## Automatic Backup Process
* A series of Jenkins/bash scripts automate the process of replicating the production data.  These are located in source control project [cloudant-backup](https://github01.hclpnp.com/richard-ptasnik/cloudant-backup)
* A [Jenkins server](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/cloudant-backups/) is responsible for scheduling, storing credentials, and e-mail notifications.

The automation flow 
* A Jenkins pipeline job is scheduled using cron schedules for daily, weekly, or monthly jobs.
* The latest [cloudant-backup](https://github01.hclpnp.com/richard-ptasnik/cloudant-backup) scripts are cloned from source control.
* Depending on the job type, the script invokes a Cloudant REST API to start a replication job from the main production account production database to the backup account using various backup databases.
* The script monitors the replication job for completion and sends out e-mails on job failures.

## Credential Strategy 
* The production database credentials have no access to the backup account.
* The scripts only have read-access to the production database.
* The scripts do not have access to the production account.
* Credentials are not stored in scripts.
* Credentials are stored in the Jenkins credential store.  

## Backup Propagation Schedule
To avoid keeping data indefinitely, the deletion of data in the production database is propagated to the backups.  The following 'rotating log' schedule will give the production team various time-frames to detect the introduction of deleted/corrupted data before it enters the backup databases.  The main use-case is protecting against a member of the production team (via accident or errant code) deleting the production database.

### Daily backups 
* Every day a backup is stored based on the day in the week (Mon/Tues/Wed/Thur/Fri/Sat/Sun)
* This will allow a history of daily backups going 7 days back in time.
* prodautodaily(0-6)
### Weekly backups
* Every Monday a weekly backup is created based on the week in the month (0-4)
* This will allow a history of weekly backups going 1 month back in time.
* prodautoweekly(0-4)
### Monthly backups
* Every month a backup is stored based on the current month.
* This will allow a history of monthly backups going 1 year back in time.
* prodautomonthly(0-11)


## Backup Steps
If a deletion or corruption is detected, say by accident or when upgrading RPT on Cloud.  Follow the exact steps.
* Stop all backup jobs in Jenkins.  This can be done by disabling the jobs in Jenkins.
* Log onto the backup account and make manual replications of a known good state.
* Find the last known good state.  For instance, if you delete the production database the best available state would be yesterdays daily backup.
* Copy the last good backup into the production database
* Restart the backup jobs only after the exact cause of the deletions are known.
* Before making a risky Cloud Manager version update, make manually backups before the switch over. 

 
