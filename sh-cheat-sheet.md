## Wildcard workaround
The wildcard * operations don't include files with name beginning with ".". E.g. if following directory contains files ".gitignore" and ".travis.yml" - cp * won't copy them, rm * won't delete them.  
"__cp -r .* .__" - will fail, the problem is that every directory on a unix-type file system contain two special directories:  
- "." refers to the directory itself
- ".." refers to the parent directory

The workaround is to use [a-zA-Z0-0]:  
__cp -r test/.[a-zA-Z0-9]* target__

## Search expressions
Following line searches through man page of __bash__ and prints the matched line and the first line after it:
```sh
man bash | grep -A1 '\-f file$'
```
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
