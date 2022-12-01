

Git is the version control system invented by Linus Torvalds.

### Fundamentals

![](Pasted%20image%2020220830231635.png)

When you add files or change existing files in a repo they must be added to the current snapshot with `git add`. You can see what is current in the snapshot with `git status`. Then once you wish to commit the snapshot, use `git commit`. To then view the history of snapshots you may use `git log`. These three actions form the core of Git's functionality. You can also tag a commit e.g. labelling a specific snapshot as a stable release with something like `git tag -a v1.0 -m "Stable version 1"`.

To view a different snapshot in time you use `git checkout [commit checksum]` and your repo will revert to the chosen commit. If the commit has an associated tag you also do `git checkout [tag name]`.

To revert to a previous commit, use `git revert`. This creates a new commit that is a copy of the commit before the revert. Git never erases history.

![](Pasted%20image%2020220831000055.png)
`git reset --hard` will resets all tracked files to the last commit
`git clean -f` will remove all untracked files. 
Be careful with these as they delete things without keeping a record like `revert`.

### Branches

![](Pasted%20image%2020220831002436.png)
You create branches to work on different features without having to edit the main branch.

`git branch` to view all branches
`git branch [name]` this creates a branch
`git checkout [name]` this moves your current working branch
`git checkout -b [name]` this both creates a branch and moves the HEAD to that branch

At their core, branches are just a way to maintain different file repositories.  The rule of thumb to follow is:
1. Create a new branch for every major addition to the project
2. don't create a branch if you can't give it a specific name

### Merge

Merging two branches is simple if one is linearly ahead of the other, this is called a fast-forward merge.

![](Pasted%20image%2020220831002933.png)
When there is a conflict, like the image above, a 3-way merge is performed and you must resolve any conflicts that arise by selecting which changes to keep from each branch. This could be a messy problem if there are a lot of conflicts

An alternative is rebasing.
![](Pasted%20image%2020220831003241.png)
With `git rebase [target]` you can stick your current branch in front of the target branch. This makes the merge a simple fast-forward merge and makes it much simpler to accomplish. No merge conflicts will exist


### Remote

You can add a remote repo like **GitHub** with `git remote add [remote] [link]`. This will add the remote repo, which you can interact with

![](Pasted%20image%2020220831004612.png)

`git push [remote] [branch]` pushes your branch to the remote repo. 
`git fetch [remote]` pulls in changes from the remote repo, you can then merge in those changes. 
`git pull [remote]` first fetches changes and then merges into your local repo. 
So Pull = Fetch + Merge

### Tips & Tricks

- Commit a snapshot for each significant addition to your project
- Don't commit a snapshot if you cant come up with a single, specific message for it
- Remove a remote with `git remote rm [remote]`
- Never ever rebase commits that have been pushed to a shared public repo i.e. GitHub
- When there is a difference of your code and the public repo, make sure you fetch code periodically
- You can rebase your branch on the public repo so the merge would just be a fast forward
- `git log master..origin/master --stat` says what the remote repo has that you don't
- `git log origin/master..master --stat` says what you have that the remote repo doesn't