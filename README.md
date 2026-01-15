# Elliott's Cybersecurity Projects and Writeups

## OverTheWire: Bandit Writeups

### Level 0

**Objective:** SSH into the game server

**Solution:** To enter the server, the correct port and username must be used. The `-p` command sets the port and prepending `username@` to the ip sets the user.

Use the following command to enter the server:

```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

When prompted, enter the password bandit0.

### Level 0 -> Level 1

**Objective:** Locate password stored in readme. Use password to log into bandit1 through ssh.

**Solution:** First, use `ls` to determine the files and folders in the current directory. Viewing the result:

```
readme
```

It is clear that the readme file is located in the current directory. Using `cat` we can print out the contents of the readme file into the console, revealing the password. Use this password to ssh into bandit1, using the same port as in level 0.

### Level 1 -> Level 2

**Objective:** Locate password in a file called `-`.

**Solution:** Attempting to directly use the `cat` command to print out the contents of the `-` file will result in the terminal hanging as the `-` is misinterpreted as a command or a flag. This can be solved by prepending `./` to the filename, which allows for files with filenames beginning with `-` to be correctly interfaced with.

Using:

```
cat ./-
```

The contents of the `-` file are correctly printed to the console.

### Level 2 -> Level 3

**Objective:** Locate password in a file called `--spaces in this filename--`.

**Solution:** To correctly interface with a file where the filename has spaces between words, enclosing the filename in double quotes will work. However, attemping to `cat` the contents in this file using just double quotes will result in an error. This is because the filename includes a leading `--`. 

Borrowing from knowledge gained in Level 1 -> 2, prepending `./` in addition to the use of double quotes solves both issues.

```
cat ./"--spaces in the filename--"
```

This corrects prints the contents of the file to the console.

### Level 3 -> Level 4

**Objective:** Locate password in a hidden file in the inhere directory.

**Solution:** Use the `cd` command to change the directory to the `inhere` directory.

```
cd inhere
```

Using the `ls` command in the `inhere` directory, no files are revealed. This is solved by using the `-a` flag, which reveals all files in a directory, including hidden files.

```
ls -a
```

Use `cat` on the revealed file to obtain the password.

### Level 4 -> Level 5

**Objective:** Locate the password in the only human-readable file in the inhere directory.

**Solution:** Again, navigate to the `inhere` directory. Using `ls` brings up a list of all the files that need to be checked. While each file can be checked individually, the `file` command can be used to determine the exact file needed in a single search. To do this, the wildcard operator `*` can be leveraged to search every file in the directory.

Keeping in mind that each file starts with a `-`, the command:

```
file ./*
```
Will classify every file. The human-readable file is the ASCII text file, and using `cat` on it will reveal the password.

### Level 5 -> Level 6

**Objective:** Locate the password in a file under the inhere directory that has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable

**Solution:** To find files that meet all three criteria, a somewhat complicated `find` command is used.

The first section of this `find` command:

```
find . -type f
```

finds every file in the current directory and its subdirectories(which is in fact where all the files are).

The second section uses the flag `-size` to filter for any files of a selected size. As the desired size is provided in Bytes, the correct suffix for the size option is c.

```
-size 1033c
```

The third section uses the flag `-executable` and the operator `!`. The `-executable` flag filters for files that are executable. The `!` operator negates the condition set by the `-executable` flag, changing the filter such that it instead selects files that are not executable.

```
! -executable
```

The final section involves the use of `exec` and `grep`. `exec` is used to execute a `file` command on the filtered files. `{}` is a placeholder variable that represents the files filtered so far(e.g. file1 file2 etc.). The `+` operator is used to increase the efficiency of the command by processing all files at once rather than one at a time. The `|` pipe operator is then used to send the results to the `grep` command.

```
-exec file {} + |
```

The `grep` command searches each line of the results, searching for a match to the string provided to it. The output contains only lines which contain "ASCII text".

```
grep "ASCII text"
```

The placement of the `exec` action at the end of the `find` command is intentional. The flags before this section require access to file attributes, but after the `exec` action those files have already left the `find` command, leaving the flags no way of evaluating the files. Had the `exec` action come before those flags, the execution chain would have been broken.

Together, the full command is:

```
find . -type f -size 1033c ! -executable -exec file {} + | grep "ASCII text"
```

This should leave only one file that fulfills all three criteria. Read the contents of the file to retrieve the password.

### Level 6 -> Level 7

**Objective:** Find the password located somewhere in the server. The file containing the password has the properties:
- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

**Solution:** Again, this can be done using `find`. The `user` flag will find files owned by the target user, and the `group` flag will find files owned by the target group. Combining the `size` flag from the previous exercise, and by starting the search from the home directory by using `/`, the entire server can be searched for the password holding file. As the user bandit6 lacks the permission to read many files in the server, the results of the search will be polluted by a large number of permission denied messages. To clean up the search, `2>/dev/null` can be used to redirect all error messages(2 represents stderr and the error messages) into `/dev/null`, where they are deleted. Also, in order to ensure that only files are returned use the `type` flag with a value of f.

```
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

Retrieve the password from the resulting file.

### Level 7 -> 8

**Objective:** Find password stored in data.txt next to the word millionth

**Solution:** Viewing the `data.txt` file reveals that each line consists of a pair of words and passwords. Searching for a line with the word `millionth` will then also reveal the password. Use the `grep` command to search for `millionth` in the `data.txt` file to show the answer.

### Level 8 -> 9

**Objective:** Find password stored in data.txt in the line that only occurs once.

**Solution:** Use the `sort` command to sort data.txt and gather line duplicates together. Pipe the result in `uniq` with the `-c` to delete duplicates and to count the number of times each line of text appears. Conduct another `sort` with the flag `-n` to sort the results numerically, with the lowest integer on top. This will bring the line that only occurs once to the top of the results. To execute these commands in sequence, make sure to use the pipe operator `|` in between each step.

To avoid an excess of lines of text showing on the console, the `head` command can be used with the flag `-n` with a value `1` to limit the output to 1 line of text.
