# KB Linux general cheat sheet

- [KB Linux general cheat sheet](#kb-linux-general-cheat-sheet)
  - [Basic commands](#basic-commands)
    - [cat](#cat)
    - [nl](#nl)
    - [sed](#sed)
    - [grep](#grep)
      - [Predefined classed for grep](#predefined-classed-for-grep)
    - [Bash history](#bash-history)
  - [Remote connect via SSH](#remote-connect-via-ssh)
  - [TreeSize](#treesize)
  - [Sudo](#sudo)
  - [Troubleshoot specific components](#troubleshoot-specific-components)
    - [Logrotate](#logrotate)
    - [Reading logs](#reading-logs)
      - [Less command key-commands](#less-command-key-commands)
      - [journald](#journald)
      - [journalctl commands](#journalctl-commands)
        - [Priority table](#priority-table)
    - [systemctl commands](#systemctl-commands)
    - [Logged in users](#logged-in-users)

## Basic commands

### cat

- Use `>` to create file and add text or `>>` to append text to an existing file
- When done typing, type `[Ctrl]`+`[D]`

### nl

List line numbers with nl

### sed

Use sed to find and replace text, for example:

- Replace every occurence of mysql with MySQL in /etc.snort.conf and create a new file with the changes:
  - `sudo sed s/mysql/MySQL/g /etc/snort/snort.conf > snort2.conf`
- Break the commands down:
  - The `s`-command performs the search
    - first list the term you want to search for, then the term you want to replace it with, separated by a forward slash `/`
  - The `g`-command tells Linux that you want the replacement performed globally.
  - The `>` outputs the results to a new file
- To only change the first occurence of mysql, you would leave out the `g`-parameter
  - `sudo sed s/mysql/MySQL/ /etc/snort/snort.conf > snort2.conf`
- To only replace the second occurence of mysql in the file:
  - `sudo sed s/mysql/MySQL/2 /etc/snort/snort.conf > snort2.conf`

### grep

- Filter al lines in gpl document starting with 'GNU' and colour them
  - `less gpl-3.0.txt | grep --color=always "^GNU"`
- Filter gpl document for the string 'cept' and two random characters and colour them
  - `grep --color=always "..cept"`
- Filter gpl document for strings that have a 't', followed by either a 'w' or and 'o' and then an 'o'
  - `less gpl-3.0.txt | grep --color=always "t[wo]o"`
- Filter gpl document for every line of strings that begin with a capital [A-Z]
  - `less gpl-3.0.txt | grep --color=always "^[A-Z]"`

#### Predefined classed for grep

- [:alnum] - Alphanumeric
- [:alpha] - Alphabetic
- [:blank:] - Space and tab
- [:digit:] - Digits
- [:lower:] - Lowercase letters
- [:upper:] - Uppercase letters

### Bash history

- Use `history` to list your command history
  - you can use `head`, `tail`, or pipe to `grep` to search specific commands
- Rerun specifid command using the number in front in the history table
- Rerun last command with `!!` or add sudo before it in case you forgot
- Rerun specific command from history by adding a exclamation mark `!` before the history number.
  - `!666`

## Remote connect via SSH

Login to Azure bastion or VM with azureadmin using SSH agent forwarding

- Add ssh private key to agent in KeePassXC
- Use the following command
  - `ssh azureadmin@<ip-address>`
- For bastion:
  - `ssh azureadmin@pbl-linux-bastion.westeurope.cloudapp.azure.com`
- For domain account login:
  - `ssh jansem@INT.PBL.NL@<ip-address>`
  - `ssh a_mjanse@PBLM.local@<ip-address>`
- Logging on with a specific private key to a remote system:
  - `ssh -i ~/.ssh/ansible-course-key-pair.pem ec2-user@3.217.151.26`

## TreeSize

- To list files Largest files (descending):
  - `du -h | sort -h -r`

## Sudo

- Always edit sudoers file with `visudo`
- Get root permissions
  - `sudo`
- Login as another user
  - `sudo -i -u henk`

## Troubleshoot specific components

### Logrotate

- Check logrotate daemon
  - `systemctl status logrotate`
- Check logrotate status
  - `cat /var/lib/logrotate/logrotate.status`
- Check configured Logrotate settings
  - `ls -l /etc/logrotate.d/`
- Run logrotate in debug mode:
  - `sudo /usr/sbin/logrotate -d /etc/logrotate.conf`

Example of Samba logrotate settings:

```bash
$ cat /etc/logrotate.d/samba

/var/log/samba/log.* {
    compress
    dateext
    maxage 365
    rotate 99
    notifempty
    olddir /var/log/samba/old
    missingok
    copytruncate
}
```

### Reading logs

#### Less command key-commands

|   Key   |                 Description                 |
|:-------:|:-------------------------------------------:|
| Arrow   | Move by one line                            |
| Space   | Move down one page                          |
| b       | Move up one page                            |
| g       | Go to the first line                        |
| G       | Go to the last line                         |
| 100g    | Go to the 100th line                        |
| /string | Search for the string from current position |
| n/N     | Go to the next or previous search match     |
| q       | Exit the logs                               |

#### journald

By default, journald does not keep logs between boots. You need to make the log files persistent:

- Open journald config
  - `sudo vim /etc/systemd/journald.conf`
- Edit the first line from `#Storage=auto` to `#Storage=persistent`
- Restart the journald daemon
  - `sudo systemctl restart systemd-journald`
- Check status of the journald service
  - `sudo systemctl status systemd-journald`

#### journalctl commands

- All logs in chronological order
  - `journalctl`
- All logs in reverse order
  - `journalctl -r`
- Show only the last 25 lines
  - `journalctl -n 25`
- Real-time watching (tail)
  - `journalctl -f`
- Only kernel messages
  - `journalctl -k`
- List all boot sessions
  - `journalctl --list-boots`
  - Session 0 is the current boot session. Boot session -1 is the last booted session, etc.
- Get logs from the previous boot session
  - `journalctl -b -2`
- Get logs from a specific service
  - `journalctl -u nginx`
- Filter on time interval
  - `journalctl --since=yesterday --until=now`
  - `journalctl --since "2021-12-05"`
  - `journalctl --since "2021-07-10 15:10:00" --until "2021-07-12"`
- Filter on UID, GID or PID
  - `journalctl _PID=1234`
- Combination of parameters
  - Only SSH logs from yesterday in UTC timestamps, you can use
  - `sudo journalctl -u ssh --since=yesterday --utc`
- Only ssh logs in current boot session
  - `sudo journalctl -u ssh -b0`
- Checking only last few logs:
  - `journalctl -xe`
    - -e = jump to the end of the journal log
    - -x = show extra information on the log entries (if available)
- Show only errors in the current boot session
  - `journalctl -p 3 -xb`
    - -p 3 : filter logs for priority 3 (which is error)
    - -x : provides additional information on the log (if available)
    - b : since last boot (which is the current session)
- List warning, notice and info logs from current boot session:
  - `journalctl -p 4..6 -b0`
- Check disk usage of journals
  - `journalctl --disk-usage`

##### Priority table

| Priority |   Code  |
|:--------:|:-------:|
| 0        | emerg   |
| 1        | alert   |
| 2        | crit    |
| 3        | err     |
| 4        | warning |
| 5        | notice  |
| 6        | info    |
| 7        | debug   |

### systemctl commands

- List current status
  - `systemctl status`
- List status specific service
  - `systemctl status nginx`
  - `systemctl status ssh`
- Check if service is running or not
  - `systemctl show sshd --property=SubState`
- Check if service is active or inactive
  - `systemctl is-active sshd`
- Check if service is loaded or not
  - `systemctl show sshd --property=LoadState`
- List all units available in memory
- `systemctl list-units`
- List all available units
  - `systemctl list-units --all`
- List failed services
  - `systemctl list-units --type=service --state=failed`
  - `systemctl --failed`
- List running services
  - `systemctl list-units --type=service --state=running`
- List enabled and disabled services
  - `systemctl list-unit-files --type=service --state=enabled,disabled`

### Logged in users

There are multiple options to see logged in users, here are some of the most useful:

- Use the `w` command
- The `who` command
- The `users` command
- The `last` command
- Get Active Xvnc/RDP users
  - `sudo ps -ef | grep Xvnc | awk -F "/home/PBLNL/" '{ print $2 }' | awk -F "/" '{ print $1 }' | awk NF`
