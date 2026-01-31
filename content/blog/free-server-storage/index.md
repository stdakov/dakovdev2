+++
title = "Free Some Server Storage"
date = 2025-01-31
description = "Essential commands and strategies to reclaim disk space on your server"
+++

![Image](banner.png)

Running out of server storage is a common problem that can cause system failures, service disruptions, and data loss. This guide covers essential commands to monitor and clean up disk space on Linux servers.

<!-- more -->

## Check Disk Usage First

Before cleaning up storage, always check your current disk usage to identify problem areas.

### df -h: Check Filesystem Disk Space

The `df -h` command shows disk space usage for all mounted filesystems in human-readable format.

```bash
df -h
```

This displays information including:

- **Filesystem**: The disk partition or device
- **Size**: Total size of the filesystem
- **Used**: Amount of space used
- **Avail**: Available space remaining
- **Use%**: Percentage of space used
- **Mounted on**: Where the filesystem is mounted

Use this command to identify which partitions are running out of space. If you see 90% or higher usage, it's time to clean up.

### df -i: Check Inode Usage

Sometimes you can run out of inodes even with free disk space. Each file uses an inode, and some filesystems have inode limits.

```bash
df -i
```

This shows:

- **Inodes**: Total number of inodes
- **IUsed**: Number of inodes used
- **IFree**: Number of inodes available
- **IUse%**: Percentage of inodes used

If you see high inode usage, you likely have many small files that need cleanup.

## Analyze Disk Usage

### sudo ncdu /

NCurses Disk Usage (`ncdu`) provides an interactive interface to analyze disk usage and identify large directories.

```bash
sudo ncdu /
```

This command:

- Scans your entire filesystem starting from root (/)
- Shows a navigable tree of directories sorted by size
- Allows you to drill down into subdirectories
- Lets you delete files directly from the interface (use with caution)

**How to use:**

- Use arrow keys to navigate
- Press Enter to enter a directory
- Press 'd' to delete a file/directory (be very careful!)
- Press 'q' to quit

This is your best tool for finding what's consuming disk space. Focus on cleaning the largest directories first.

## Clean System Logs

### sudo journalctl --vacuum-time=3d

SystemD's journal can grow very large over time. This command removes journal entries older than 3 days.

```bash
sudo journalctl --vacuum-time=3d
```

**Options:**

- `--vacuum-time=3d`: Keep only 3 days of logs
- `--vacuum-time=7d`: Keep only 7 days of logs
- `--vacuum-size=500M`: Keep only 500MB of logs

**Cron-friendly:** Yes, can be scheduled weekly or monthly.

To make this persistent, edit `/etc/systemd/journald.conf`:

```
SystemMaxUse=500M
MaxRetentionSec=3day
```

Then restart the journal service:

```bash
sudo systemctl restart systemd-journald
```

### Delete Log Files

Remove old compressed and rotated log files:

```bash
# First, test what will be deleted (without -delete)
find /var/log -type f -regex ".*\.gz$"
find /var/log -type f -regex ".*\.[0-9]$"

# Then delete them
find /var/log -type f -regex ".*\.gz$" -delete
find /var/log -type f -regex ".*\.[0-9]$" -delete
```

**Warning:** Be careful with this aggressive cleanup that deletes ALL log files:

```bash
find /var/log -type f -delete
```

Only use this in emergency situations, as you'll lose all log history.

### Manual Log Directory Cleanup

For specific log directories:

```bash
rm -r /var/log/exim4/*
rm -r /var/log/journal/*
rm -r /home/admin/tmp/*
```

**Note:** These commands will completely empty these directories. Make sure you don't need the logs first.

**Cron-friendly:** The find commands with time-based filters work well in cron jobs.

## Clean Temporary Files

### find /home/\_/tmp -type f -name 'sess\_\_' -ctime +5 -delete

Remove old PHP session files (or other temporary files) older than 5 days:

```bash
find /home/*/tmp -type f -name 'sess_*' -ctime +5 -delete
```

**Breakdown:**

- `/home/*/tmp`: Search in all users' tmp directories
- `-type f`: Only files (not directories)
- `-name 'sess_*'`: Files starting with "sess\_"
- `-ctime +5`: Files older than 5 days
- `-delete`: Delete matching files

**Cron-friendly:** Yes, perfect for daily cron jobs.

**Test first:**

```bash
find /home/*/tmp -type f -name 'sess_*' -ctime +5
```

## Clean Docker Resources

### docker system prune -a -f

Docker images, containers, and volumes can consume massive amounts of disk space.

```bash
docker system prune -a -f
```

**What it does:**

- `-a`: Remove all unused images, not just dangling ones
- `-f`: Force deletion without confirmation
- Removes stopped containers
- Removes unused networks
- Removes dangling images
- Removes build cache

**Warning:** This will remove ALL unused Docker resources. Make sure you don't need any stopped containers or old images.

**Safer alternative:**

```bash
# Remove only stopped containers and dangling images
docker system prune -f
```

**Cron-friendly:** Yes, but use with caution. Consider running weekly or monthly.

### Clean Docker Container Logs

Docker container logs can grow indefinitely. Find the largest log files:

```bash
sudo find /home/docker/containers -type f -name '*-json.log' -printf '%s %p\n' \
  | sort -n | tail -n 20 \
  | awk '{printf "%.1f MB %s\n", $1/1024/1024, $2}'
```

Truncate a specific container's log:

```bash
sudo truncate -s 0 /home/docker/containers/<container-id>/<container-id>-json.log
```

**Better solution:** Configure Docker log rotation in `/etc/docker/daemon.json`:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

Then restart Docker:

```bash
sudo systemctl restart docker
```

## Clean Email Queue (Exim)

If you're running an email server with Exim, the mail queue can fill up with spam or bounced messages.

### View Mail Queue

```bash
exim -bp
```

### Remove Single Message

```bash
exim -Mrm {message-id}
```

### Remove All Messages from Queue

Option 1:

```bash
exim -bp | awk '/^ *[0-9]+[mhd]/{print "exim -Mrm " $3}' | bash
```

Option 2 (cleaner):

```bash
exim -bp | exiqgrep -i | xargs exim -Mrm
```

**Cron-friendly:** Can be scheduled if you have persistent spam issues.

## Commands Suitable for Cron Jobs

Here are the commands that work well in automated cron jobs:

### Daily Cron Jobs

```bash
# Clean old session files
0 2 * * * find /home/*/tmp -type f -name 'sess_*' -ctime +5 -delete

# Clean old compressed logs
0 3 * * * find /var/log -type f -regex ".*\.gz$" -mtime +30 -delete
```

### Weekly Cron Jobs

```bash
# Clean journal logs (keep 7 days)
0 4 * * 0 sudo journalctl --vacuum-time=7d

# Docker cleanup (careful!)
0 5 * * 0 docker system prune -f
```

### Monthly Cron Jobs

```bash
# Aggressive log cleanup (keep 1 month)
0 3 1 * * find /var/log -type f -regex ".*\.[0-9]$" -mtime +30 -delete

# Clean old rotated logs
0 4 1 * * find /var/log -type f -regex ".*\.gz$" -mtime +60 -delete
```

### Sample Crontab Configuration

Edit your crontab with `crontab -e` and add:

```bash
# Clean old session files daily at 2 AM
0 2 * * * find /home/*/tmp -type f -name 'sess_*' -ctime +5 -delete

# Clean journal logs weekly on Sunday at 3 AM
0 3 * * 0 /usr/bin/journalctl --vacuum-time=7d

# Clean old compressed logs weekly
0 4 * * 0 find /var/log -type f -regex ".*\.gz$" -mtime +30 -delete

# Docker system prune monthly on the 1st at 5 AM
0 5 1 * * /usr/bin/docker system prune -f >> /var/log/docker-cleanup.log 2>&1
```

## Best Practices

1. **Always test commands first** without the `-delete` flag to see what will be affected
2. **Monitor disk usage regularly** with `df -h` and `df -i`
3. **Set up alerts** when disk usage exceeds 80%
4. **Configure log rotation** rather than manual cleanup when possible
5. **Review what's using space** with `ncdu` before aggressive cleanup
6. **Keep backups** of important data before bulk deletions
7. **Document your cleanup schedule** so you know what runs when
8. **Check application-specific logs** (nginx, apache, mysql, etc.)

## Emergency Quick Cleanup Script

When you're completely out of space and need immediate relief:

```bash
#!/bin/bash
echo "Emergency disk cleanup starting..."

# Clean journal
journalctl --vacuum-time=1d

# Clean old logs
find /var/log -type f -regex ".*\.gz$" -delete
find /var/log -type f -regex ".*\.[0-9]$" -delete

# Clean tmp files
find /tmp -type f -atime +2 -delete
find /var/tmp -type f -atime +2 -delete

# Clean old session files
find /home/*/tmp -type f -name 'sess_*' -ctime +1 -delete

# Docker cleanup
docker system prune -a -f

echo "Cleanup complete. Current disk usage:"
df -h
```

Save this as `emergency-cleanup.sh`, make it executable with `chmod +x emergency-cleanup.sh`, and run when needed.

## Conclusion

Regular disk space management prevents emergencies. Set up automated cleanup jobs, monitor your disk usage, and investigate unusual growth patterns. The combination of `df -h`, `ncdu`, and targeted cleanup commands will keep your server running smoothly.
