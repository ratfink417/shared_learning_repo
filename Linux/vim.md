Vim stands for Vi IMproved. It is a functional text editor that supports many features such as undo, visual selection, find and replace, syntax highlighting etc. Refer to the manual with `man vim` for a complete list of commands.
# Basic Editing
This covers the most basic uses of Vim for editing text files.
## Editing AKA Insert Mode
 Insert mode allows you to enter new text or edit existing text is done by pressing the "INSERT", "i", or "s" key. Additionally, when using a Mac "CMD" + "ENTER" will also enter insert mode. You'll know your in insert mode because the bottom row of the shell will say `-- INSERT --`.
 
## Command line
Save, quit, search, etc are all done from the command line inside vim. It is activated by pressing the `:` key. 

## Save
After bring up the [Command line](vim.md##Command%20line) press `w` to write AKA save.

## Quit
Quitting is done by bringing up the [Command line](vim.md##Command%20line) and pressing 'q'. If you have made any editations after the last save, it will warn you and tell you to save first or quit without saving by pressing `q!`