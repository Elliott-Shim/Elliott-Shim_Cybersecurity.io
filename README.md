# 

## OverTheWire: Bandit Writeups

### Level 0

**Objective:** SSH into the game server

**Solution:** To enter the server, the correct port and username must be used. The `-p` command sets the port and prepending `username@` to the ip sets the user.

Use the following command to enter the server:

```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

When prompted, enter the password bandit0.

### Level 0 → Level 1

**Objective** Locate password stored in readme. Use password to log into bandit1 through ssh.

**Solution** First, use `ls` to determine the files and folders in the current directory. Viewing the result:

```
readme
```

It is clear that the readme file is located in the current directory. Using `cat` we can print out the contents of the readme file into the console. This reveals the password to be: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`. Use this password to ssh into bandit1, using the same port as in level 0.

### Level 1 -> Level 2

**Objective** Locate password in a file called `-`.

**Solution** Attempting to directly use the `cat` command to print out the contents of the `-` file will result in the terminal hanging as the `-` is misinterpreted as a command or a flag. This can be solved by prepending `./` to the filename, which allows for files with filenames beginning with `-` to be correctly interfaced with.

The command:

```
cat ./-
```

Reveals the password to be: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
