
## CLI

[command output redirect to both: file and terminal](https://stackoverflow.com/questions/13591374/command-output-redirect-to-file-and-terminal)
```sh
ls 2>&1 | tee /tmp/ls.log
```
`2>&1` redirects channel 2 (stderr/standard error) into channel 1 (stdout/standard output), such that both is written as stdout. It is also directed to the given output file as of the tee command.

Furthermore, if you want to _append_ to the log file (say it exists), use tee -a as
```sh
ls 2>&1 | tee -a /tmp/ls.log
```
### stdin, stdout, stderr
http://www.learnlinux.org.za/courses/build/shell-scripting/ch01s04.html

## Bash Guide
[Bash Guide for Beginners](http://tldp.org/LDP/Bash-Beginners-Guide/html/index.html)
by Machtelt Garrels  
including complete intro into __if__, __awk__, __sed__ etc.

## Wildcard workaround
The wildcard * operations don't include files with name beginning with ".". E.g. if following directory contains files ".gitignore" and ".travis.yml" - cp * won't copy them, rm * won't delete them.  
"__cp -r .* .__" - will fail, the problem is that every directory on a unix-type file system contain two special directories:  
- "." refers to the directory itself
- ".." refers to the parent directory

The workaround is to use [a-zA-Z0-0]:  
__cp -r test/.[a-zA-Z0-9]* target__

## Search expressions
Following line searches through man page of __bash__ and prints the matched line AND the first line after it:
```sh
man bash | grep -A1 '\-f file$'
```
`$` - defines end of line (the found line should end with the expression in the regexp)  
The OUTPUT is:  
```
       -f file
              True if file exists and is a regular file.
```

this time we omit the searched line and print only the first after it:  
```sh
man bash | grep -A1 '\-f file$'|grep -v "\-f file$"
```
The OUTPUT is:  
```
              True if file exists and is a regular file.
```

same principle for search in files:  
```sh
grep -A1 'blah' logfile|grep -v "blah"
```

## Text editing
sed - stream editor for filtering and transforming text. 
e.g. 
```sh
sed 's@.*/@asdf/@'
```
s - substitute  
@ - delimiter between sed parts (@S1@S2@ - find S1, replace with S2)  
".\*/" - (.) any character; (\*) - 0 or more; (/) - before /  
replace with asdf. So, sed will resolve into following:
```sh
echo qwer-txt/qwer | sed 's@.*/@asdf/@'
```
result: asdf/qwer

checkout more about sed [here](https://github.com/grelaxus/notes-pub/blob/master/sed-cheat-sheet.md)

## if
[Full intro to if](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html)  
[What is the difference between the Bash operators \[\[ vs \[ vs \( vs \(\(](https://unix.stackexchange.com/questions/306111/what-is-the-difference-between-the-bash-operators-vs-vs-vs)? Following a short answer:  

An __if__ statement typically looks like

```sh
if commands1
then
   commands2
else
   commands3
fi
```
The then clause is executed if the exit code of `commands1` is zero. If the exit code is nonzero, then the else clause is executed. `commands1` can be simple or complex. It can, for example, be a sequence of one or more pipelines separated by one of the operators `;`, ```&```, ```&&```, or ```||```. The if conditions shown below are just special cases of `commands1`:  

    1. if [ condition ]

    This is the traditional shell test command. It is available on all POSIX shells. The test command sets an exit code and the if statement acts accordingly. Typical tests are whether a file exists or one number is equal to another.

    2. if [[ condition ]]

    This is a new upgraded variation on test from ksh that bash and zsh also support. This test command also sets an exit code and the if statement acts accordingly. Among its extended features, it can test whether a string matches a regular expression.

    3. if ((condition))

    Another ksh extension that bash and zsh also support. This performs arithmetic. As the result of the arithmetic, an exit code is set and the if statement acts accordingly. It returns an exit code of zero (true) if the result of the arithmetic calculation is nonzero. Like [[...]], this form is not POSIX and therefore not portable.

    4. if (command)

    This runs command in a subshell. When command completes, it sets an exit code and the if statement acts accordingly.

    A typical reason for using a subshell like this is to limit side-effects of command if command required variable assignments or other changes to the shell's environment. Such changes do not remain after the subshell completes.

    5. if command

    command is executed and the if statement acts according to its exit code.
    
    
    

| Operator	| Meaning                                 | Example                          |
| ------------| :---                                    |:---------------------------------|
| >	       | Comes before alphabetically	       | if [[ $a > $b ]]
| <	       | Comes first alphabetically	       | if [[ cat < dog ]]
| =	       | Equal (strings)	                     | if [[ $path = "/usr/bin/" ]]
| -lt	       | less than (integers)	              | if [[ 1 -lt 10 ]]
| -gt	       | greater than (integers)	              | if [[ $number -gt 0 ]]
| -eq	       | Equal (integers)	                     | if [[ $status -eq 0 ]]
| -ne	       | Not equal (integers)	              | if [[ $year -ne 2018 ]]
| -e 	       | File exists	                            | if [[ -e "$bam_index" ]]
| -d	       | File exists, and its a directory	       | if [[ -d "/tmp" ]]
| -f 	       | File exists, and its a regular file	| if [[ -f ~/.bashrc ]]
| -s	       | File exists and its not empty	       | if [[ -s "$output" ]]

## Upstart

[__update-rc.d__](https://manpages.debian.org/wheezy/sysv-rc/update-rc.d.8.en.html) updates the System V style init script links /etc/rcrunlevel.d/NNname whose target is the script __/etc/init.d__/_name_. These links are run by init when it changes runlevels; they are generally used to start and stop system services such as daemons. runlevel is one of the runlevels supported by init, namely, 0123456789S, and NN is the two-digit sequence number that determines where in the sequence __init__ will run the scripts.

[How to reliably start a service with UpStart](https://zohaib.me/how-to-reliably-start-a-service-with-upstart/)  
[Upstart cookbook](http://upstart.ubuntu.com/cookbook/#job-states)
