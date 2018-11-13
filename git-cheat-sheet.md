[Why do I need to explicitly push a new branch?](https://stackoverflow.com/questions/17096311/why-do-i-need-to-explicitly-push-a-new-branch)  

[how to delete all commit history in github?](https://stackoverflow.com/questions/13716658/how-to-delete-all-commit-history-in-github):  


Checkout

    git checkout --orphan latest_branch

Add all the files

    git add -A

Commit the changes

    git commit -am "commit message"

Delete the branch

    git branch -D master

Rename the current branch to master

    git branch -m master

Finally, force update your repository

    git push -f origin master

PS: this will not keep your old commit history around
shareimprove this answer




(from [here](https://stackoverflow.com/questions/19980631/what-is-git-checkout-orphan-used-for)) The core use for __git checkout --orphan__ is to reach a git init-like situation on a non-new repository.

Without this ability, all of your git branches would have a common ancestor, your initial commit. This is a common case, but in no way the only one. For example, git allows you to track multiple independent projects as different branches in a single repository.

That's why your files are being reported as “changes to be committed”: in a git init state, the first commit isn't created yet, so all files are new to git.
