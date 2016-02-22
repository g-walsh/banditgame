# Bandit Game Solutions

My solutions to the linux cli educational game Bandit <http://overthewire.org/wargames/bandit>.
You are encouraged to complete this game for yourself even if you are a unix ninja but I'm putting up my answers here for posterity and discussion.

The first few answers are fairly trivial but this gets progressively harder and I will comment more on the more interesting problems.

### Level 0

<http://overthewire.org/wargames/bandit/bandit0.html>

```
ssh bandit0@bandit.labs.overthewire.org
```
Here we use the given password `bandit0`

### Level 0 -> 1

<http://overthewire.org/wargames/bandit/bandit1.html>

```
cat readme
```
`cat` is a very useful function for joining files together but with only one argument it will quickly print the contents of a file to stdout (to the terminal).
Here the password for *bandit1* is stored in plain text.
```
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

### Level 1 -> 2

<http://overthewire.org/wargames/bandit/bandit2.html>

```
ssh bandit1@bandit.labs.overthewire.org
```
Here is something that I had to be reminded about that files named with `-`. When you use `-` in place of a filename in an argument of a function it will use STDIN or STDOUT so in this case writing
```
cat -
```
will not prodcue the desired effect as it will be reading STDIN, instead we want to explicitly refer to the file using
```
cat ./-
```
This shows us the password for *bandit2*
```
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

### Level 2 -> 3

<http://overthewire.org/wargames/bandit/bandit3.html>

```
ssh bandit2@bandit.labs.overthewire.org
```
Here tab completion will save the day by filling in the desired `\` to mark spaces in the filename
```
cat spaces\ in\ this\ filename

UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

### Level 3 -> 4

<http://overthewire.org/wargames/bandit/bandit4.html>

```
ssh bandit3@bandit.labs.overthewire.org
```
While `ls` will list the files in the current directiory `ls -a` will list all files including hidden ones.
```
cd inhere
ls -a
```
This will expose the `.hidden` file where the password is stored. File names that begin with a `.` will be hidden by default.
```
cat .hidden

pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

### Level 4 -> 5

<http://overthewire.org/wargames/bandit/bandit5.html>

```
ssh bandit4@bandit.labs.overthewire.org
```
This one also showed me a feature of `ls` that I didn't know which will only list human-readable files by using the option `-h`.
```
cd inhere
ls -ah
```
and as these files have a `-` in them we should view the correct file using
```
cat ./-file07

koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

### Level 5 -> 6

<http://overthewire.org/wargames/bandit/bandit6.html>

```
ssh bandit5@bandit.labs.overthewire.org
```

I spent a while looking at this one using `ls` but in the end using the fact that the file was 1033 bytes in size was the quickest way to find the file we were looking for.
Using the command `du` (disk usage) with its defaults will show the size of the directories and subdirectories. If we use the `-a` option this will show the individual file sizes and using `-b` will display those sizes in bytes (rather than as blocks on the disk).
```
du -ab
```
This produces a list of files with their respective sizes
```
...
2084	./inhere/maybehere18/.file2
7040	./inhere/maybehere18/spaces file3
154	./inhere/maybehere18/.file3
7334	./inhere/maybehere18/spaces file1
...
```
I then use a pipe `|` which sends the output of one command to the input of another to send this list of files to a command called `grep` which searches for text strings and tell it to search for `1033` (our desired file size).
```
du -ab | grep 1033

1033	./inhere/maybehere07/.file2
```
This looks like it might be our file!
```
cat ./inhere/maybehere07/.file2

DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

### Level 6 -> 7

<http://overthewire.org/wargames/bandit/bandit7.html>

```
ssh bandit6@bandit.labs.overthewire.org
```
Now we can use the `find` function to search the filesystem for files given certain tests that we want to give it.
Here we have 3 specific properties of the file user, group and size.
Using the tests `-group` and `-user` we can easily find the file we are looking for.

```
find / -group bandit6 -user bandit7
```

This will produce some errors for directories that we don't have permission to read and will output only one other file that meets these criteria.
Assuming that the designers haven't put the file somewhere we can't access then it will have to be this one.
We can perform `du` again to double check that the file has a size of 33 bytes.

```
du -b /var/lib/dpkg/info/bandit7.password

33

cat /var/lib/dpkg/info/bandit7.password

HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

### Level 7 -> 8

<http://overthewire.org/wargames/bandit/bandit8.html>

```
ssh bandit7@bandit.labs.overthewire.org
```
I think this is where they meant to introduce `grep` and as a result of us already using it we can solve this similarly to before.
We use `cat` to print all of the lines of this file and then pipe the output to `grep` and look for the word *millionth*.

```
cat data.txt | grep millionth

millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```

### Level 8 -> 9

<http://overthewire.org/wargames/bandit/bandit9.html>

```
ssh bandit8@bandit.labs.overthewire.org
```

This one is probably not the best solution but it is the first one that I found which first involves using `sort` to order the list `data.txt` so that repeated lines are all next to each other.
This can then be piped to `uniq` with the option `-c` which will count the number of repeated line.
Then we just need to `sort` it again in reverse order using `-r` which will list the only line that occurs only once at the end of the sort.

```
sort data.txt | uniq -c | sort -r
```

The final line of this output shows the password as it is the only line with 1 occurence according to `uniq`.

```
1 UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

### Level 9 -> 10

<http://overthewire.org/wargames/bandit/bandit10.html>

```
ssh bandit9@bandit.labs.overthewire.org
```

Using the `strings` function will output all of the human-readable strings of `data.txt` and then we just need to find those beginning with several `=` symbols using `grep`.

```
strings data.txt | grep ===
I========== the6
========== password
========== ism
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```

And only one of these looks like the correct password (from the previous formats).

### Level 10 -> 11

<http://overthewire.org/wargames/bandit/bandit10.html>

```
ssh bandit10@bandit.labs.overthewire.org
```
