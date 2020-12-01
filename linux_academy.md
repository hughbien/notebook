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
