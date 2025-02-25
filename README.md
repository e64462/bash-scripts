### **ABOUT**
This program acts as a small wrapper around BORG Backup by managing a 'database' of filesystem endpoints specified in

`/usr/local/etc/backUP/backup.txt`

backUP will process the endpoints specified there in two ways: 
1. Build the full parent directory structure of each endpoint
2. If the endpoint is a directory, then also traverse it recursively

Calling `backUP -b` uses this information to create a backup of the full directory tree, preserving all permissions, leading to each endpoint specified in `backup.txt`. It will also use `mysqldump` to create a backup in the BORG repository containing all databases.

Additional options are supported. See Usage below.

***
### DEPENDENCIES (DEVELOPED WITH)
1. Ubuntu v20.04+      - The code assumes the value of `$SUDO_USER` -- so any Debian based distro might work
2. BORG Backup v1.2.3+ - For performing the backups
3. Bash v4+            - For associative arrays
4. MySQL v8+           - Only version tested

***
### INSTALLATION
All commands should be run as root
1. Initialize a BORG Backup Repository

`sudo borg init --encryption=repokey-blake2 /path/to/repo`

2. Run the installer.
```
cd bash-scripts
sudo ./install
```

3. (Optional) Add crontab entries by typing `sudo crontab -e` in your shell and add the following lines to automate backups
```
1 2 * * * /usr/local/bin/backUP -b  # Nightly backups at 2:00 AM
0 0 1 1 * /usr/local/bin/backUP -c  # Yearly pruning / compacting
```

4. That's it, you're done.

***
### CONFIGURATION
Create/Edit a config file `/usr/local/etc/backUP/backUP.conf` with your favorite editor, add these lines (uncommented lines are mandatory)
```
# Passphrase for BORG_REPO
BORG_PASSPHRASE = "passphrase"

# Absolute Path to BORG Repository
BORG_REPO = "/path/to/repo"

# Absolute Path to list of Backup Files, changing this requires editing backUP
BACKUP_DB = "/usr/local/etc/backUP/backup.txt"

# Absolute Path to the duplicate(s) of BORG_REPO
# DUPLICATE_REPO_PATH = "path-1"
# DUPLICATE_REPO_PATH = "path-2"

# Directory to store the log file. Logging will be disabled if unset.
# LOGPATH = "/var/log/backUP"

# Valid Levels: STOUT CRIT ERROR DEBUG TRACE
# LOGLEVEL = "CRIT"

# Uncomment to log all standard output generated to LOGPATH
# LOGSTOUT = "false"

# Uncomment to log all standard error generated to LOGPATH
# LOGSTERR = "true"

# Absolute Path to extraction directory for 'temp' folder. Defaults to your $SUDO_USER's home directory
# EXTRACT_TO = "path"

# System User, defaults to $SUDO_USER
# USER = "some user"
```

***
### USAGE
Type `backUP` in your shell for usage information

```
Usage:
 backUP option [<path>]

Options:
     -a <path> : Add <path> to the 'database'
     -b        : Backup all 'database' entries with BORG backup
     -c        : Prune and then cleanup BORG backups
 --conf <path> : Specify an alternate backUP config file to use
 --dupe <path> : Duplicate to <path> ( No limit to number of calls )
     -e        : Extract a BORG backup ( Defaults to ~/temp )
 --dest <path> : Set extraction directory ( Only applies to -e flag )
     -l        : List all BORG backups
     -L        : List all paths monitored by backUP
     -h        : Display usage information
     -r <path> : Remove <path> from the 'database'
     -s <name> : Search archives for <name>
 --pass        : Prompts for the BORG Backup passphrase
 --repo <path> : Sets the BORG repo to <path>
 ```
