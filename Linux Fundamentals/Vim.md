
| Commands      | Description                                                                    |
| ------------- | ------------------------------------------------------------------------------ |
| :q!           | Exits and trashes all changes.                                                 |
| :wq           | Exits and saves all changes.                                                   |
| :!rm \<file\> | Deletes said file.                                                             |
| dw            | Delete a word.                                                                 |
| d$            | Delete to the end of the line.                                                 |
| dd            | Delete a whole line.                                                           |
| 0             | Moves to start of the line.                                                    |
| u             | Undo.                                                                          |
| U             | Undo all changes to that line.                                                 |
| CTRL-r        | Redo.                                                                          |
| y             | Copy text.                                                                     |
| p             | When deleting a line or smth, it is stored. Pasted with p.                     |
| rx            | Replaces with x. Where x is any character.                                     |
| R             | Enters replace mode.                                                           |
| ce            | Deletes to end of word and places u in Insert mode.                            |
| cc            | Does the same for the whole line.                                              |
| G             | Move to bottom of the file.                                                    |
| gg            | Move to start of the file.                                                     |
| \<line\> G    | Moves to said line.                                                            |
| CTRL-g        | Show your location in the file and the file status.                            |
| /             | Search. N and n to navigate. Options are set with `:set`                       |
| %             | Find matching parenthesis in line.                                             |
| :s/old/new    | Replaces old with new for that line. Substitute command. Check for other uses. |
| :!            | To type external shell commands.                                               |
| v             | Enters visual selection. You can select text and then use operators as normal. |
| :r \<file\>   | Inserts text of the file starting from the current line.                       |
| o             | Inserts a line below current line. O for above.                                |
| CTRL-d        | Autocomplete commands.                                                         |

Include a number to do multiple times a motion or operator.