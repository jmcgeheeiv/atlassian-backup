# atlassian-backup
Back Up Atlassian Databases

This tool backs up JIRA Server and Confluence Server databases according the
[best practices outlined by Atlassian](https://confluence.atlassian.com/adminjiraserver072/backing-up-data-828788079.html).

## Config file
The backups are controlled by a config file.  The example config file
`atlassian-backup.conf` contains documentation on the variables that can be set
in the config file.  It also contains example values.

## Running atlassian-backup
The default config file location is `/etc/atlassian-backup.conf`, but ordinarily
you specify the config file on the command line:
```
atlassian-backup [config_file]
```

As it runs `atlassian-backup` tells you what it is doing:
```
Backing up JIRA
  - Backing up JIRA attachments
/bin/tar: Removing leading `/' from member names
    Created /atlassian/backups/jira/jira-2017-11-07-19-34-attachments.tgz
  - Dumping JIRA database
    Created /atlassian/backups/jira/jira-2017-11-07-19-34-database-dump.sql.gz

Backing up Confluence
  - Backing up Confluence attachments
/bin/tar: Removing leading `/' from member names
    Created /atlassian/backups/confluence/confluence-2017-11-07-19-34-attachments.tgz
  - Dumping Confluence database
    Created /atlassian/backups/confluence/confluence-2017-11-07-19-34-database-dump.sql.
gz
```

## Running with cron
On UNIX it is convenient to run `atlassian-backup` using cron.  Edit your cron
table using the command:
```
crontab -e
```
In the following example cron table, backups are created every weekday.

On the first of every month, a backup is created and retained indefinitely.
Then, to save space, the daily backups from the preceding month are deleted:
```
10 07 * * 2-6 /atlassian/atlassian-backup/bin/atlassian-backup /atlassian/atlassia
n-backup/configs/atlassian-backup.conf
# On the first of each month, after the backup runs,
# save the backups from the first and delete the rest.
10 10 1 * * cd /atlassian/backups/confluence && mv confluence-20??-??-01-*z monthly && r
m confluence-20??-??-??-*z
10 11 1 * * cd /atlassian/backups/jira && mv jira-20??-??-01-*z monthly && rm jira-20??-
??-??-*z
```

## History

Matt Kirby of Puppet Labs created
`[jira-backup](https://github.com/puppetlabs/jira-backup)` to back up JIRA alone.
This supported MySQL.

John McGehee of Wave Computing enhanced `jira-backup` to back up Confluence and
support PostgreSQL.
