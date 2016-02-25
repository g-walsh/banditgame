# Bandit Game Solutions

**Note : I've just realised that in the welcome message to the server it states not to post solutions.
I'm not entirely certain why they wouldn't want this but the game is a couple of years old now and there are seemingly other solutions out there so I'll leave this up here but... SPOILER WARNING :)**

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

<http://overthewire.org/wargames/bandit/bandit11.html>

```
ssh bandit10@bandit.labs.overthewire.org
```

This file is encoded into base 64 and as a result we need to use `base64` to decode this into ASCI text.
Use the option `-d` to decode
```
base64 -d data.txt

The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

### Level 11 -> 12

<http://overthewire.org/wargames/bandit/bandit12.html>

```
ssh bandit11@bandit.labs.overthewire.org
```

I spent a while playing with `tr` when doing this question.
I've never used `tr` before and I can see where it would be useful in certain "find and replace" problems.
This probably not the best solution but the basic syntax of `tr` here is one of `tr [set1] [set2] < data.txt`.
This means replace all of the characters in set1 pairwise with the characters as defined in set2. I use this to produce the password

```
tr [a-zA-Z] [n-za-mN-ZA-M] < data.txt
```

Here `[set]` is a list of all lower case characters a-z and then all upper case characters A-Z.
This is replaced with the same characters but translated 13 characters along so starting from n to z and then a to m.

```
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

I feel there must be a more elegant way of doing this...

### Level 12 -> 13

<http://overthewire.org/wargames/bandit/bandit13.html>

```
ssh bandit12@bandit.labs.overthewire.org
```

This one took a short while!
They definitely did compress it a few times.
We start with `data.txt` and we want to move it and rename it to the `/tmp` folder that is suggested in the question.

```
mkdir /tmp/gordon
cp data.txt /tmp/gordon
cd /tmp/gordon
mv data.txt data1.txt
```

We now have a copy of the original data.txt in a nice temporary directory. We can now see what the file looks like by using `file data1.txt` and `cat data1.txt` which outputs
```
data1.txt: ASCII text

0000000: 1f8b 0808 34da 6554 0203 6461 7461 322e  ....4.eT..data2.
0000010: 6269 6e00 013f 02c0 fd42 5a68 3931 4159  bin..?...BZh91AY
0000020: 2653 5982 c194 8a00 0019 ffff dbfb adfb  &SY.............
0000030: bbab b7d7 ffea ffcd fff7 bfbf 1feb eff9  ................
```
This is a hexdump (as we were told in the question) and we will use the command `xxd` to reverse this with the option `-r`.

```
xxd -r data1.txt > data2.txt
```

We can now look at `data2.txt` using `file`

```
file data2.txt

dataz.txt: gzip compressed data, was "data2.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
```

So this shows us that we now have a file that is compressed using `gzip` and we'll need to uncompress this.
There are several subsequent files that are also all compressed using different commands and for brevity I will only show the required commands (sometimes including renaming with `mv` for appropriate suffix) for this file in particular.

``` bash
mv data2.txt data2.gz

gzip -d data2.gz

bzip2 -d data2

mv data2.out data2.out.gz

gzip -d data2.out.gz

tar xvf data2.tar

tar xvf data5.bin

bzip2 -d data6.bin

tar xvf data6.bin.out

mv data8.bin data8.gz

gzip -d data8.gz

cat data8

The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

Phew!

### Level 13 -> 14

<http://overthewire.org/wargames/bandit/bandit14.html>

```
ssh bandit13@bandit.labs.overthewire.org

exit
```

Here we are logged in as bandit13 and trying to read a file that is accessible only to bandit14 (and presumably root).
There is a file called `sshkey.private` which we can use to log in as bandit14 and then read the text password for the next problem.
I will use `scp` to get the key file to my local computer.

```
scp bandit13@bandit.labs.overthewire.org:/home/bandit13/sshkey.private ./
```

This will copy the ssh key to my local folder and I can then use that to log in as bandit14. (If you get a warning about an unprotected key then use `chmod 600 sshkey.private`)

```
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org
```
From here we can read the required password
```
cat /etc/bandit_pass/bandit14
eb
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```

### Level 14 -> 15

<http://overthewire.org/wargames/bandit/bandit15.html>

```
ssh bandit14@bandit.labs.overthewire.org
```

Here we can use `telnet` to connect to `localhost 30000` and then input the previous password.

```
bandit14@melinda:~$ telnet localhost 30000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

### Level 15 -> 16

<http://overthewire.org/wargames/bandit/bandit16.html>

```
ssh bandit15@bandit.labs.overthewire.org
```

This is similar to the last problem but we use an ssl handshake to authenticate and encrypt the connection.
We can't connect to an ssl server without using this.
We use the commands `openssl` and `s_client` to connect to `localhost:30001`

```
openssl s_client -ign_eof -connect localhost:30001

...
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd
...

```

The question suggests that we use the option `-ign_eof` with s_client, to find out why below I quote from the man page of `openssl`

> S_CLIENT CONNECTED COMMANDS

> If a connection is established with an SSL server, any data received from the server is displayed and any key presses will be sent to the server.
> When used interactively (which means neither -quiet nor -ign_eof have been given), the session will be renegotiated if the line begins with an R; if the line begins with a Q or if end of file is reached, the connection will be closed down.

Essentially when you are running `openssl` in an interactive mode (without using `-quiet` or `-ign_eof`) a line beginning with B,Q or R will produce unexpected results and errors because they are commands for the server.

### Level 16 -> 17

<http://overthewire.org/wargames/bandit/bandit17.html>

```
ssh bandit16@bandit.labs.overthewire.org
```

In this level we can use a bunch of interesting stuff!
I started by making a directory that I could write to and then using `nmap` to scan for open ports between `31000` and `32000` on the localhost.
Then using `grep` (and an expression that means the *number 3 and then 4 other numerical digits*) we can write this to a text file containing the open ports in the range of interest.

```
mkdir /tmp/gordon1
nmap localhost -p 31000-32000 | grep -o '3[0-9]\{4\}' > /tmp/gordon1/test.txt

cat /tmp/gordon1/test.txt
31046
31518
31691
31790
31960
```

Now we use the `nc` utility to probe these ports with a very simple string of text `hello`.
Here I'm just using a `for` loop to sequentially input the port of interest into our `nc` command.

```
for line in $(cat /tmp/gordon1/test.txt);
do
  echo hello | nc -v localhost $line;
done
```

The output here shows that two of these ports won't accept non SSL communication so these are the two "servers" of interest.
The relevant ports are `31518` and `31790` and we can use our commands from the previous question to connect to them with `openssl` and input the password.

```
openssl s_client -ign_eof -connect localhost:31518
openssl s_client -ign_eof -connect localhost:31790
```

This works on port `31790` and gives us a private key as output.

```
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!

-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

These are the credentials that we want to connect to bandit17.
Create a temporary file to store them in

```
touch /tmp/gordon1/sshkey.private
```

Paste in the details using `nano` or `vi` and restrict permissions on the file.
Then connect to the machine as bandit17 using this key.

```
chmod 600 /tmp/gordon1/sshkey.private
ssh -i /tmp/gordon1/sshkey.private bandit17@localhost
```

We can also get bandit17s plain text password from the same place as we did in an earlier question.

```
cat /etc/bandit_pass/bandit17
xLYVMN9WE5zQ5vHacb0sZEVqbrp7nBTn
```

### Level 17 -> 18

<http://overthewire.org/wargames/bandit/bandit18.html>

```
ssh bandit17@bandit.labs.overthewire.org
```

Now we use the command `diff` to compare two files line by line.
Here the only difference in the files will be the next password.

```
diff passwords.old passwords.new
42c42
< BS8bqB1kqkinKJjuxL6k072Qq9NRwQpR
---
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```

### Level 18 -> 19

<http://overthewire.org/wargames/bandit/bandit19.html>

```
ssh bandit18@bandit.labs.overthewire.org
```

When we `ssh` into this server we get booted out straight away by the `.bashrc` file.
This file is a configuration file that is executed on logging into the terminal emulator, in this case logging in via ssh.
Instead of logging into the server interactively we can put a command at the end of the `ssh` which will execute that command rather than log into a remote shell.

```
ssh bandit18@bandit.labs.overthewire.org cat readme

IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

### Level 19 -> 20

<http://overthewire.org/wargames/bandit/bandit20.html>

```
ssh bandit19@bandit.labs.overthewire.org
```

This problem contains a binary executable that bandit19 has permission to use and it will execute a command as bandit20.
The binary is called `bandit20-do` and can be executed using `./bandit20-do` to see what the syntax is.
We can then use

```
./bandit20-do cat /etc/bandit_pass/bandit20

GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

### Level 20 -> 21

<http://overthewire.org/wargames/bandit/bandit21.html>

```
ssh bandit20@bandit.labs.overthewire.org
```

In this problem we want to open and listen to a local port using `nc` and the option `-l` to listen to this port.
We also want to pass the password for this level to this port

```
cat /etc/bandit_pass/bandit20 | nc -l localhost 8040
```

`nc` is now listening on port `8040`.
We now want to run the binary mentioned in the question that will connect to a port on localhost and then read a line of text from it.
The binary `suconnect` will read this line of text and if it matches the password it will then send the next password back to the port that our `nc` process is listening to.
This next command is to be run on a different ssh shell.

```
./suconnect 8040
Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
Password matches, sending next password
```

Then back in the original terminal the `nc` command will terminate hopefully with

```
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```

### Level 21 -> 22

<http://overthewire.org/wargames/bandit/bandit22.html>

```
ssh bandit21@bandit.labs.overthewire.org
```

As they suggest we can first move to `/etc/cron.d` and then look at what is there.

```
cd /etc/cron.d
ls
behemoth4_cleanup  cronjob_bandit24_root  natas-stats       php5         sysstat
cron-apt           leviathan5_cleanup     natas25_cleanup   semtex0-32   vortex0
cronjob_bandit22   manpage3_resetpw_job   natas25_cleanup~  semtex0-64   vortex20
cronjob_bandit23   melinda-stats          natas26_cleanup   semtex0-ppc
cronjob_bandit24   natas-session-toucher  natas27_cleanup   semtex5
```

The interesting ones here are the ones called `cronjob_bandit*` and here we are looking for bandit22s password.
Lets have a look at this file and then the subsequent script `cronjob_bandit22.sh`

```
cat cronjob_bandit22
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null

cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

```

Lets see if we can read the temporary file

```
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

### Level 22 -> 23

<http://overthewire.org/wargames/bandit/bandit23.html>

```
ssh bandit22@bandit.labs.overthewire.org
```

As in the last question we can look at the `cron` task `/etc/cron.d/cronjob_bandit23` and then look at the resulting script file.

```
cat /usr/bin/cronjob_bandit23.sh

#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget

```

This script file defines two variables `myname` which calls the function `whoami` which outputs the current user name.
The second is `mytarget` which takes the string `I am user $myname` and then calculates the md5sum and removes anything after the first space.
After these variables are calculated the script then takes the password for that user and writes it to `/tmp/$mytarget` which is a file named by the md5sum calculated earlier.

The solution is fairly easy after reading what the script does we first need to find out what the `$mytarget` variable would be with bandit23.

```
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```

Now we can find the password by looking for this file in the `/tmp` directory

```
cat /tmp/8ca319486bfbbc3663ea0fbe81326349

jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```

### Level 23 -> 24

<http://overthewire.org/wargames/bandit/bandit24.html>

```
ssh bandit23@bandit.labs.overthewire.org
```

Here we want to write a shell script in a similar style to the one in the previous question.
First lets see what the cronjob looks like

```
cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
	echo "Handling $i"
	timeout -s 9 60 "./$i"
	rm -f "./$i"
    fi
done
```

This cron task will run any executable file in `/var/spool/bandit24` and then delete it.
Our job here is to write a script that will be run by bandit24 to write the password to a file accessible by bandit23.
We should start by making a temporary directory to write our files to (and make it universally accessible and create our script.

```
mkdir -p /tmp/gordon24
chmod -R 777 /tmp/gordon24

cd /tmp/gordon24
vim passme.sh
```

Script file

```
#!/bin/bash

myname=$(whoami)

cat /etc/bandit_pass/$myname > /tmp/gordon24/pass24
```

We need to make this script executable and then we should copy it to the target directory of the cronjob and wait.

```
chmod 777 passme.sh
cp ./passme.sh /var/spool/bandit24/
```

After less than 1 minute the `cron` task will run and hopefully write the password to our temporary file `pass24`.

```
cat ./pass24

UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

### Level 24 -> 25

<http://overthewire.org/wargames/bandit/bandit25.html>

```
ssh bandit24@bandit.labs.overthewire.org
```
