# Table of Contents
- [[#Getting Help|Getting Help]]
- [[#Navigating Directories|Navigating Directories]]
- [[#File Descriptors|File Descriptors]]
- [[#Filtering|Filtering]]
- [[#RegEx|RegEx]]
- [[#Permission Management|Permission Management]]
- [[#User Management|User Management]]
- [[#Service and Process Management|Service and Process Management]]
	- [[#Service and Process Management#Background a process|Background a process]]
- [[#Scheduling Tasks|Scheduling Tasks]]
- [[#Network Services|Network Services]]

# Getting Help

| Command             | Description                                                |
| ------------------- | ---------------------------------------------------------- |
| man \<tool\>        | Shows the manual pages for the tool                        |
| \<tool\> help       | Shows syntax of the tool                                   |
| apropos \<keyword\> | Searches the descriptions for instances of a given keyword |
# Navigating Directories

| Command | Description                                                                                                     |
| ------- | --------------------------------------------------------------------------------------------------------------- |
| cd      | Changes the directory. Entering new directories or going back home. Use `-` to jump back to previous directory. |
| ls      | Lists directory contents. Use `-la` to list hidden files too.                                                   |
| pwd     | Returns working directory name.                                                                                 |
| env     | Prints environment. Includes all the directories paths and the like.                                            |
| grep    | Can be used with env to find a specific directory path by piping \|.                                            |
| touch   | Creates an empty file.                                                                                          |
| mkdir   | Creates a new directory.                                                                                        |
| tree    | Shows the whole directory structure.                                                                            |
| mv      | Move file to a different directory, optionally also renames it.                                                 |
| cp      | Copies file and pastes it into the given directory.                                                             |
| which   | Returns the path to the file or program. which python would return the path to it.                              |
| locate  | Searches local database for files. Run `sudo updatedb` first.                                                   |
| find    | Used to find files. Can be used to filter the results.                                                          |
# File Descriptors

| FD         | Description                                                |
| ---------- | ---------------------------------------------------------- |
| STDIN - 0  | Data stream for input.                                     |
| STDOUT - 1 | Data stream for output.                                    |
| STDERR - 2 | Data stream for output that relates to an error occurring. |
Redirect file descriptors using \< or \>. Direction signals from where and where to.
- \>: Used to redirect a data stream to user's choosing. `find ... 2> errors.txt`
- <: Used to use a file as input. `cat < errors.txt`
- <<: Uses the current input stream as STDIN, i.e. prompts the user for input until keyword is inputted. `cat << <keyword> > test.txt` Allows user to input text and when keyword is inputted it stops the input stream and redirects the output into "test.txt".
- | : Pipes the output into another tool of the users choosing. `find ... 2>/dev/null | grep smth` Can pipe multiple times.
# Filtering
**Find command options**
![[Pasted image 20240822104459.png]]

| Command | Description                                                                                                                                                           |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| more    | Displays the file in an interactive view. Output remains in terminal after exiting.                                                                                   |
| less    | Displays the file in an interactive view. Output does not remain in terminal.                                                                                         |
| head    | Prints first 10 lines by default.                                                                                                                                     |
| tail    | Prints last 10 lines by default.                                                                                                                                      |
| sort    | Sorts lines alphabetically.                                                                                                                                           |
| grep    | Searches for given pattern. I.e. keyword. Use -v to search excluding the pattern.                                                                                     |
| cut     | Separate based on given delimiter with option -d.                                                                                                                     |
| tr      | Replaces a character from the lines with another character.                                                                                                           |
| sed     | Stream editor. Works similar to replace but with different syntax. Check for help.                                                                                    |
| column  | Displays in tabular form with -t. There are other options.                                                                                                            |
| awk     | Used to display the results of the line the user wants. `awk '{print $1 $NF}'` Gives the first and last of the results in the line. `one two three four` ->`one four` |
| wc      | Word count, can be used to count other things using the options. -l counts lines.                                                                                     |
# RegEx
![[Pasted image 20240826121207.png]]
Useful to use with the -e option of the grep command to filter with some logic.
# Permission Management
Permission types:
- `(r)` - Read
- `(w)` - Write
- `(x)` - Execute, does not allow user to execute or modify any files, only to traverse and access directories.
Permissions can be set for `owner` , `group` and `others`:
![[Pasted image 20240826122907.png]]
We can modify permissions using the `chmod` command, permission group references (`u` - owner, `g` - Group, `o` - others, `a` - All users), and either a `+` or a `-` to add remove the designated permissions.
To change the owner and/or the group assignments of a file or directory, we can use the `chown` command. The syntax is like following: `chown <user>:<group> <file/directory>`

Sticky bits replace `x` with `t` or `T`. Lower case sets execute permissions for all users while upper case does not give execute permissions. Sticky bits are used to only allow the owner to delete or rename files in the directory.
# User Management
| **Command** | **Description**                                                                                                                                            |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sudo        | Execute command as a different user.                                                                                                                       |
| su          | The `su` utility requests appropriate user credentials via PAM and switches to that user ID (the default user is the superuser). A shell is then executed. |
| useradd     | Creates a new user or update default new user information.                                                                                                 |
| userdel     | Deletes a user account and related files.                                                                                                                  |
| usermod     | Modifies a user account.                                                                                                                                   |
| addgroup    | Adds a group to the system.                                                                                                                                |
| delgroup    | Removes a group from the system.                                                                                                                           |
| passwd      | Changes user password.                                                                                                                                     |
# Service and Process Management
`systemctl` is used to list services.
Processes can be controlled using `kill`, `pkill`, `pgrep`, and `killall`. To interact with a process, we must send a signal to it. The most commonly used are:

| **Signal** | **Description**                                                                                                          |
| ---------- | ------------------------------------------------------------------------------------------------------------------------ |
| 1          | `SIGHUP` - This is sent to a process when the terminal that controls it is closed.                                       |
| 2          | `SIGINT` - Sent when a user presses `[Ctrl] + C` in the controlling terminal to interrupt a process.                     |
| 3          | `SIGQUIT` - Sent when a user presses `[Ctrl] + D` to quit.                                                               |
| 9          | `SIGKILL` - Immediately kill a process with no clean-up operations.                                                      |
| 15         | `SIGTERM` - Program termination.                                                                                         |
| 19         | `SIGSTOP` - Stop the program. It cannot be handled anymore.                                                              |
| 20         | `SIGTSTP` - Sent when a user presses `[Ctrl] + Z` to request for a service to suspend. The user can handle it afterward. |
## Background a process
We can suspend processes using `Ctrl Z`. We can check available jobs with `jobs`. Use `bg` to put process running in the background. You can automatically set a process into background by adding `&` at the end of the command. To get the background process into the foreground use `fg`.
# Scheduling Tasks
Honestly, just check the section on Linux Fundamentals. Easier to see from HTB.
# Network Services
`ssh` : Secure Shell is a network protocol that allows the secure transmission of data and commands over a network. It is widely used to securely manage remote systems and securely access remote systems to execute commands or transfer files. In order to connect to our or a remote Linux host via SSH, a corresponding SSH server must be available and running. The most commonly used SSH server is the OpenSSH server.

`nfs` : Network File System is a network protocol that allows us to store and manage files on remote systems as if they were stored on the local system. It enables easy and efficient management of files across networks. For Linux, there are several NFS servers, including NFS-UTILS (Ubuntu), NFS-Ganesha (Solaris), and OpenNFS (Redhat Linux).

`Web Servers` : A web server is a type of software that provides data and documents or other applications and functions over the Internet. They use the Hypertext Transfer Protocol (HTTP) to send data to clients such as web browsers and receive requests from those clients. These are then rendered in the form of Hypertext Markup Language (HTML) in the client's browser. Some of the most popular web servers for Linux servers are Apache, Nginx, Lighttpd, and Caddy.

`vpn` : Virtual Private Network is a technology that allows us to connect securely to another network as if we were directly in it. This is done by creating an encrypted tunnel connection between the client and the server, which means that all data transmitted over this connection is encrypted. Some of the most popular VPN servers for Linux servers are OpenVPN, L2TP/IPsec, PPTP, SSTP, and SoftEther.


| Command | Description                                                                                                                                                                                                        |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| curl    | Tool that allows us to transfer files from the shell over protocols like `HTTP`, `HTTPS`, `FTP`, `SFTP`, `FTPS`, or `SCP`.                                                                                         |
| wget    | Tool that allows us to download files from `FTP` or `HTTP` servers directly from terminal and serves as a good download manager. Difference with curl is that the website content is downloaded and stored locally |
# Backup and Restore
When backing up data on an Ubuntu system, we can utilize tools such as:
- Rsync
- Deja Dup
- Duplicity
# Access Control Systems
`Discretionary Access Control` : It is a widely used access control system that enables users to manage access to their resources by granting resource owners the responsibility of controlling access permissions to their resources. This means that users and groups who own a specific resource can decide who has access to their resources and what actions they are authorized to perform. These permissions can be set for reading, writing, executing, or deleting the resource.

`Mandatory Access Control` : Each resource is assigned a security label that identifies its security level, and each user or process is assigned a security clearance that identifies its security level. Access to a resource is only granted if the user's or process's security level is equal to or greater than the security level of the resource.

`Role-based Access Control` : Users are assigned roles based on their job responsibilities or other criteria, and each role is granted a set of permissions that determine the actions they can perform. In an RBAC system, each user is assigned one or more roles, and each role is assigned a set of permissions that define the user's actions. Resource access is granted based on the user's assigned role rather than their identity or ownership of the resource.
# System Logs
`Kernel Logs` : These logs contain information about the system's kernel, including hardware drivers, system calls, and kernel events. They are stored in the `/var/log/kern.log` file.

`System Logs` : These logs contain information about system-level events, such as service starts and stops, login attempts, and system reboots. They are stored in the `/var/log/syslog` file. In addition, we can use the `syslog` to identify potential issues that could impact the availability or performance of the system, such as failed service starts or system reboots.

`Authentication Logs` : These logs contain information about user authentication attempts, including successful and failed attempts. They are stored in the `/var/log/auth.log` file. It is important to note that while the `/var/log/syslog` file may contain similar login information, the `/var/log/auth.log` file specifically focuses on user authentication attempts, making it a more valuable resource for identifying potential security threats.

`Application Logs` : These logs contain information about the activities of specific applications running on the system. They are often stored in their own files, such as `/var/log/apache2/error.log` for the Apache web server or `/var/log/mysql/error.log` for the MySQL database server. These logs are particularly important when we are targeting specific applications, such as web servers or databases, as they can provide insights into how these applications are processing and handling data.

`Security Logs` : These security logs and their events are often recorded in a variety of log files, depending on the specific security application or tool in use. For example, the Fail2ban application records failed login attempts in the `/var/log/fail2ban.log` file, while the UFW firewall records activity in the `/var/log/ufw.log` file. Other security-related events, such as changes to system files or settings, may be recorded in more general system logs such as `/var/log/syslog` or `/var/log/auth.log`.