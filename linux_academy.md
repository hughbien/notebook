# Linux Academy

Notes from [Linux Academy](https://linuxacademy.com).

# Mastering The Linux Command Line

## Command Line Keyboard Shortcuts

* Ctrl-A moves to beginning of line
* Ctrl-E moves to end of line
* Ctrl-K deletes everything after cursor
* Ctrl-U deletes everything before cursor
* Ctrl-W deletes the last word
* Ctrl-T switches letters on both sides of the cursor
* Ctrl-F/B moves forward/back one character at a time
* Ctrl-D/H deletes forward/back one character at a time
* Ctrl-P brings up last command executed (up/down arrow works too)
* Ctrl-L clears entire screen except current command prompt
* Ctrl-C terminates the current process

## Command Line History

History is stored in `~/.bash_history` file.

* `history -c` clears your history (in case you accidentally type your password)
* `history` dumps history of commands, most recent at bottom
* `history | grep <word>` to search history file for specific word
* Ctrl-R keyboard shortcut to search history

## Which and Whereis

Used to find out information about other Linux commands.

* `which <command>` to find out more info (works on any commands including aliases)
* `echo $PATH` to see your executable paths
* `whereis <command>` lists the binary path, the source, and documentation

## Finding Files With Locate

Locate returns all files with the given search term. It uses a cached database.

* `locate <term>` searches for all files with term in it
* `locate /home` to search all files in home directory
* `updatedb` updates the locate database with current files

## The Powerful Find Command

Find files with one or more conditions, and perform actions against those files.

* `find .` find all files recursively within current directory
* `find . -name 'cron'` find all files/directories that exactly match term cron
* `find . -name 'cron*'` find all files/directories that start with cron
* `find . -type f -name 'cron*'` find all files that start with cron, only files no directories
* `find . -type d -name 'cron*'` find all directories that start with cron
* `find . -perm 777` find all files with 777 permission
* `find . -perm 777 -exec chmod 555 {} \;` run `chmod 555` against all 777 permission files
* `find . -mtime +1` find all files modified within last day
* `find . -atime +1` find all files accessed within last day
* `find . -size 1M` find all files that are 1 megabyte in size

## Streams, Redirects, and Pipes

0 represents STDIN, 1 is STDOUT, and 2 is STDERR.

* `<` to redirect STDIN from a file
* `>` to redirect STDOUT to a file
* `>>` to append STDOUT to a file
* `>&` redirects output of one stream to another, so `2>&1` will redirect STDERR to STDOUT
* `2>` to redirect STDERR to a file
* `2>>` to append STDERR to a file

Pipes takes the output of one command and redirects it as the input of another command.

* `ls /etc/ | wc` pipes output of ls to wc, to count number of lines
* `<command> | grep <word>` pipes output of command to grep to search for a word

## grep, egrep, fgrep

Use these commands to search with a regex pattern. `egrep` is just `grep -E` which uses the
extended regular expression pattern. `fgrep` is `grep -F` which uses fixed strings.

Extended regular expression patterns: | .* {}

* `grep <pattern>` searches for pattern from STDIN, use a pipe to search command outputs
* `grep ^<pattern>` all lines that begin with pattern
* `grep <pattern>$` all lines that end with pattern
* `grep <pattern> -c` returns a count of matches
* `egrep -i 'hello.*world' <file>` return lines that have hello and world in it, case insensitive match

## cut command

Use cut to cut out specific fields in a formatted file.

* `cut -f1 -d: passwd` returns list of usernames in `passwd` file, eg return first field of each
  line using the colon as the delineator

## sed Stream Editor

Use sed to edit files from your command prompt.

* `sed <command> <file>` execute sed command on file
* `sed 's/parttime/fulltime/' team.txt` substitute parttime with fulltime in the team.txt file
* `sed 's/parttime/fulltime/w promote.txt' team.txt` same command, but write to promote.txt file
* `sed '/fulltime/w fulltime.txt' team.txt` write only fulltime lines to fulltime.txt
* `sed '0,/parttime/s/parttime/promotion/' team.txt` only run sub command on first occurrence
* `sed 's/<[^>]*>//' team.txt` removes html tags from team.txt file
* `sed '/^$/d' team.txt` removes empty lines

## tee command

`tee` redirects STDIN to both STDOUT and writes it to a file. It's useful when you need to write
to a file but also want to inspect the command's output.

* `ls | tee mynewfile mynewfile2` print the contents of `ls` to STDOUT, but also write it to two
  different files
* `tee -a` to append instead of overwriting entire file

# Introduction to Linux Management Tools

## Reviewing System Details Through /proc and /etc/os-release

`/proc` looks like a directory but isn't. It's a psuedo filesystem that contains kernel data.
It contains primarily read-only, zero-byte files and directories. It doesn't persist between reboots.
See `man proc` for more details.

* `/proc/version` has Kernel version and build details
* `/proc/cpuinfo` has CPU model, vendor, specs
* `/proc/meminfo` has memory utilization, caching, and swap usage
* `/proc/modules` has Kernel modules currently loaded
* `modinfo <module>` to see more info about the module

Directories that start with a number are PIDs or process IDs, which contain information about the
running process.

`/etc/os-release` file contains information about the operating system and distro, such as:

* NAME - distribution name
* VERSION - release version
* PRETTY_NAME - full distribution
* URLs - support, reporting, etc...

## Taking a Look at Processes with ps

PS stands for process status and shows a snapshot of the process activity. It's lightweight and quick.

* `ps` lists out processes tied to current shell
* `ps -e` displays all active processes in simple format
* `ps -ef` for full format, shows additional info like user and start time
* `ps -eF` for extra full format, shows even more like estimated swap size, real memory usage, processor
* `ps -aux` shows %CPU %MEM and STAT for current state (r for running, s for sleep, c for stop)
* `ps -aux --sort user` to sort by user owning each process
* `ps -eo pid,user,start_time,cmd` to display specific columns in this order

## Monitoring Active Processes Using top

Top providers dynamic real-time view of running system.

* `top` starts the dashboard
* M to sort by memory
* C to sort by CPU
* N for PID view
* R to reverse the current sort
* x to highlight current column
* f for the field management view (customize which columns to use)
* k to bring up kill command (enter PID here to kill it)
* d to edit refresh rate
* o or O for searching case insensitive/sensitive (example: COMMAND=sshd or %CPU>0)
* c to display full command
* V for forest view of commands
* u to filter by user
* W to write out config file to `~/.toprc`

## Monitoring Active Processes Using htop

`htop` providers information like top, but in a friendlier UI.

* `htop` to start dashboard
* `F6` to change sort columns (or even click on sort by tab or column header)
* tag a row with spacebar
* highlight a row and hit `F` to follow it
* `F2` to customize the dashboard UI
* `F4` to filter out processes

## Using nmon to Monitor System Performance

`nmon` can be used to monitor multiple parts of your system. It can display realtime info or write
out static info to a CSV file.

* `sudo apt install nmon` or `sudo yum install nmon` or `sudo dnf install nmon` to install it
* on redhat, you might need to install `epel-release` first
* `nmon` to start dashboard
* h for the help screen, the most important command
* from the splash screen, use the corresponding letters to view specific dashboards

# Mastering Systemd

## The Purpose of systemd

`launchd` was an inspiration, especially its listening to sockets. A daemon needs to be able to
access a socket from another daemon before it can start, its dependency. With systemd, all the
sockets are available at once before daemons are started. One process providers sockets for all
services -- so there are fewer dependency issues. It also providers extra robustness to the system
by queueing messages, in case services aren't up and running yet to receive them. 

## systemctl

Stands for System Control. Deals with everything as units, sch as: `session-3.scope` or `user.slice`
or `dbus.socket`.

For demo purposes, we'll work with the Apache service:

```sh
$ yum -y install httpd
$ rpm -ql httpd | grep system
```

You'll see `httpd.service` and `htcacheclean.service`, service unit files to work with systemd.

`systemctl` is the swiss army knife of systemd, with several subcommands.

* `systemctl status httpd.service` shows status info on a unit
* `systemctl enable httpd.service` to start service when system boots, it creates a link from the
  systemd directory to Apache's install directory
* `systemctl disable httpd.service` to prevent service from starting when system boots, it removes
  the link from above

If you don't specify which unit you're using (eg `.service`), systemd will assume you're working
with a service.

Run `systemctl status` by itself to get a complete status report of your machine.

Run `systemctl` by itself to get status of all units on your system.

Besides configuring units, systemctl can also handle single starts/stops:

* `systemctl start httpd.service` to start the service
* `systemctl stop httpd.service` to stop the service
* `systemctl help http.service` for documentation
* `systemctl -H <hostname>` to work with a remote machine

## Introduction to the systemd Journal

It's a binary file that records everything that happens on a system: Kernel log messages, system
log messages (same as syslog), system services output to stdout and stderr, audit records. Default
location is at `/run/log/journal/`.

Log is lost on reboot. To persist it:

```sh
$ mkdir -p /var/log/journal
$ systemd-tmpfiles --create --prefix /var/log/journal
```

Take a look at the man page for journal config: `man 5 journald.conf`. Config is at
`/etc/systemd/journald.conf`.

## journalctl

* `journalctl -r` shows newest entries first
* `journalctl -e` jumps to end of the page
* `journalctl -n 100` limits amount of lines specified
* `journalctl -f` follow journal as entries are added
* `journalctl -u <unit>` only show specific entries about unit
* `journalctl -o verbose` displays journal entries with all fields
* `journalctl -o json-pretty` for data analysis format
* `echo "Hi there." | systemd-cat` sends output of a command to the journal
* `journalctl -b` shows entries since last reboot
* `journalctl --since 2018-02-09 12:28:00` shows entries since this timestamp
* `journalctl --disk-usage` displays disk usage
* `journalctl --rotate` rotate journal files

## More systemd Tools

* `systemd-analyze` prints how long it takes for system to boot up
* `localectl` view/change system's locale and keyboard mapping
* `timedatectl` view/change time zone and system time
* `hostnamectl` view/set system's host name
* `systemd-resolve` allow system to resolve hostnames
* `systemd-inhibit` prevents system from sleeping/shutting down while command is running
