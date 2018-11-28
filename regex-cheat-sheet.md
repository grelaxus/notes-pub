^ - start of line
$ - end of line

E.g. `man bash | grep -A1 '\-f file$'` returns (more on shell commands [here](https://github.com/grelaxus/notes-pub/blob/master/sh-cheat-sheet.md#search-expressions))  
```sh
       -f file
              True if file exists and is a regular file.
```
we say that the line should contain "-f file" expression and it must end with this expression. The same command, without `$` at the end will return a few more found items, including `-f filename`.
The same idea for starting `^` expressions.

## gedit
**Find:**  
([A-Z]+)([0-9.,]{5}) - find any number of A-Z letters, followed by 5 digits (with dot or comma) - groupped by parentheses

**Replace:**  
\1 - use only the first group from the expression above

## From experience
### Backreference followed by a number (in Ansible)
When using regex in Ansible, e.g. replace command:
```
- replace: dest='tmp.cfg' regexp='(^tick.*)=([0-9]*)' replace='\1=qq'
```
groups are a very convenient way to use in replcae (this is called [backreference](https://www.regular-expressions.info/backref.html) and see also [here](https://www.regular-expressions.info/refcapture.html)).
Common usage is `\1` - up to \99 groups can be used. BUT, if number follows by the group Ansible (and possibly not only Ansible) gets confused: e.g. I want to add `0` after group `\1`, so following expression would confuse Ansible:
```
- replace: dest='tmp.cfg' regexp='(^tick.*)=([0-9]*)' replace='\10'
```
because it tries to find group `10`, rather than group `1`  
There are options to try (different languages may support slihtly different syntax'). Following works for Ansible:  
```
- replace: dest='tmp.cfg' regexp='(^tick.*)=([0-9]*)' replace='\g<1>0'
```
also `${1}0` can be considered (didn't work for Ansible though).  
[Here](https://www.regular-expressions.info/refcapture.html) is table of syntaxs' per language/platform
