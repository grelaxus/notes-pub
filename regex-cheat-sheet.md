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

