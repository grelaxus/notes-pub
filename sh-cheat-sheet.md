## Wildcard workaround
The wildcard * operations don't include files with name beginning with ".". E.g. if following directory contains files ".gitignore" and ".travis.yml" - cp * won't copy them, rm * won't delete them.  
"__cp -r .* .__" - will fail, the problem is that every directory on a unix-type file system contain two special directories:  
- "." refers to the directory itself
- ".." refers to the parent directory

The workaround is to use [a-zA-Z0-0]:  
__cp -r test/.[a-zA-Z0-9]* target__
