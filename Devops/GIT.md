 ![[Pasted image 20260304155635.png]]

command for see difference in two git commit
git diff commit_id1..commit_id2

changes between working tree and stage area

git diff

changes between stage area and local repository

git diff --staged

changes between working tree and local

git diff HEAD


who did the commit 

git blame filename

changing the last git commit message
git commit --amend -m "msg"

Delete the latest commit 

git reset --hard HEAD~1

if we want to modify git commit messages

git rebase -i HEAD~Number of commit we want to modify


Combine commit messages

squash
we need atleast one commit to merge into 

restore command only works in stage 1 and 2 means working and staging area

git restore --staged filename is oppostite of git add means undo the git add


##`git reset` 

`git reset` moves the **HEAD pointer** (and optionally affects staging/working directory) to a previous commit. It's mainly used to undo commits.

![[Pasted image 20260311202729.png]]



## git reset --soft commit-number
after do the command  it removes **two** commit and both head and master point to **one**commit all of the modified version stay in the staged area


![[Pasted image 20260311212401.png]]


## git reset --mixed commit-number


it clean both staging and the commit that why we see red modified in the staging area of index

![[Pasted image 20260311212743.png]]

## git reset --hard commit-number

it clean both staging and working area means it just moves to commit position that we give in the command so all the file gone of the previous head

![[Pasted image 20260311213017.png]]


### `HEAD~1` is just a shortcut

bash

````bash
git reset HEAD~1
# is the same as
git reset 3757a54
```

---

### What `HEAD~` means:
```
HEAD    = where you currently are
HEAD~1  = 1 commit behind current
HEAD~2  = 2 commits behind current
HEAD~3  = 3 commits behind current
```

Visually:
```
HEAD~3  HEAD~2  HEAD~1  HEAD
  ↓       ↓       ↓      ↓
801ddcd→3757a54→f8465cc→09239c0
"first" "second" ...   "deleted"
````

visualization
https://dsinecos.github.io/blog/Git-basics

c
# Branching and merging

local master and remote main is default

**Create a brancht*

```bash
git branch <branch_name>
```

**switch to another branch**

````bash
git checkout <branch_name>
````

combine this two command we can use

```bash
git checkout -b <branch_name>
```

if we want to 

merge all of the files in branch 2 to branch 1 

we have to be in branch 2 and then
```bash
git merge branch1
```

if we want to revert the branch we can do

```bash
git revert branch2 # from branch1
```

**Change the branch name from master to branch**

```bash
git branch -m master branch
```

**Cherry-pick**

we can merge specific file that were commited in that commit id.
here 3 branch master, list, branch1. but list has only one file and i want to merge only java file that are in the branch1 then we switch to **list** and do ```
```bash
git cherry-pick <commit-id>
```




![[Pasted image 20260312144014.png]]

```bash
git 
```

# git clean

git clean can clean untracked files
![[Pasted image 20260312144936.png]]