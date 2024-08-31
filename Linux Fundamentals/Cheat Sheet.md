### Getting Help

| Command             | Description                                                |
| ------------------- | ---------------------------------------------------------- |
| man \<tool\>        | Shows the manual pages for the tool                        |
| \<tool\> help       | Shows syntax of the tool                                   |
| apropos \<keyword\> | Searches the descriptions for instances of a given keyword |
| sd                  |                                                            |
### Navigating Directories

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
### File Descriptors

| FD         | Description                                                |
| ---------- | ---------------------------------------------------------- |
| STDIN - 0  | Data stream for input.                                     |
| STDOUT - 1 | Data stream for output.                                    |
| STDERR - 2 | Data stream for output that relates to an error occurring. |
Redirect file descriptors using \< or \>. Direction signals from where and where to.
- >: Used to redirect a data stream to user's choosing. `find ... 2> errors.txt`
- <: Used to use a file as input. `cat < errors.txt`
- <<: Uses the current input stream as STDIN, i.e. prompts the user for input until keyword is inputted. `cat << <keyword> > test.txt` Allows user to input text and when keyword is inputted it stops the input stream and redirects the output into "test.txt".
- | : Pipes the output into another tool of the users choosing. `find ... 2>/dev/null | grep smth` Can pipe multiple times.
### Filtering
Find command options
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
### RegEx
![[Pasted image 20240826121207.png]]
Useful to use with the -e option of the grep command to filter with some logic.
### Permission Management
Permission types:
- `(r)` - Read
- `(w)` - Write
- `(x)` - Execute, does not allow user to execute or modify any files, only to traverse and access directories.
Permissions can be set for `owner` , `group` and `others`:
![[Pasted image 20240826122907.png]]
We can modify permissions using the `chmod` command, permission group references (`u` - owner, `g` - Group, `o` - others, `a` - All users), and either a `+` or a `-` to add remove the designated permissions.
To change the owner and/or the group assignments of a file or directory, we can use the `chown` command. The syntax is like following: `chown <user>:<group> <file/directory>`

Sticky bits replace `x` with `t` or `T`. Lower case sets execute permissions for all users while upper case does not give execute permissions. Sticky bits are used to only allow the owner to delete or rename files in the directory.
### User Management
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
### Service and Process Management
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
**Background a process**
We can suspend processes using `Ctrl Z`. We can check available jobs with `jobs`. Use `bg` to put process running in the background. You can automatically set a process into background by adding `&` at the end of the command. To get the background process into the foreground use `fg`.
### Scheduling Tasks
Honestly, just check the section on Linux Fundamentals.




### Networks

| Command  | Description                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------- |
| ifconfig | Used to assign or view an address to a network interface and/or configure network interface parameters. |
