# How sed Works
[6.1 How sed Works](https://www.gnu.org/software/sed/manual/sed.html#Execution-Cycle)   
sed maintains two data buffers: the active pattern space, and the auxiliary hold space. Both are initially empty.

sed operates by performing the following cycle on each line of input: first, sed reads one line from the input stream, removes any trailing newline, and places it in the pattern space. Then commands are executed; each command can have an address associated to it: addresses are a kind of condition code, and a command is only executed if the condition is verified before the command is to be executed.

When the end of the script is reached, unless the -n option is in use, the contents of pattern space are printed out to the output stream, adding back the trailing newline if it was removed. Then the next cycle starts for the next input line.

Unless special commands (like ‘D’) are used, the pattern space is deleted between two cycles. The hold space, on the other hand, keeps its data between cycles (see commands ‘h’, ‘H’, ‘x’, ‘g’, ‘G’ to move data between both buffers).


From GNU man [page](https://www.gnu.org/software/sed/manual/sed.html#Overview):  
{ *commands* }  
A group of commands may be enclosed between { and } characters. This is particularly useful when you want a group of commands to be triggered by a single address (or address-range) match.
Example: perform substitution then print the second input line:
```sh
$ seq 3 | sed -n '2{s/2/X/ ; p}'
X
```

want to comment out multiple lines? Here you go:  
want to [prefix # to all the lines having text \'\[myprocess\' and 4 lines that follows it](https://stackoverflow.com/questions/11703900/sed-comment-a-matching-line-and-x-lines-after-it) expected output:  
```
#[myprocess-a]
#property1=1
#property2=2
#property3=3
#property4=4

[anotherprocess-b]
property1=gffgg
property3=gjdl
property2=red
property4=djfjf

#[myprocess-b]
#property1=1
#property4=4
#property2=2
#property3=3
```
This command will do that:  
```sh
sed -e '/myprocess/,+4 s/^/#/' 
```
Without verbous output:  
```sh
sed -i '/myprocess/,+4 s/^/#/' 
```
Following command replace a line on the third line after the matched one:  
```sh
sed -i '/- name: ansible task name/{n;n;s/what to replace on 3rd line after the first match/replace with/}' filepath
```

Delete the next after the matched:
```sh
sed -i '/find a match/{n;/action: shell/d}' filepath
```

NOTE: in order to substitute variables in sed command, use **double quotes**! Single quotes don't substitute!:  
```sh
sed -i "/find a match/shell echo ${CUSTOM_HOSTNAME}"
```
Append a line after the match (using a after the last delimiter):
```sh
sed -i "/find a match/awhat to append"
```

Some more [handy sed one-liners](https://github.com/grelaxus/notes-pub/blob/master/shell-notes/SED_handy_one-liners.html) (the [source](https://edoras.sdsu.edu/doc/sed-oneliners.html))
