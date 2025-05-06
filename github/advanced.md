## Interactive Rebase
Helps you to manipulate commit history. You can squash, edit, or reorder commits.
1. Change a commit´s message
2. Delete commits
3. Reorder commits
4. Combine multiple commits into one
5. Edit/split an existing commit into multiple new ones

Do not use interactive rebase on commits that you have already pushed to a shared repository. 
It can cause confusion and conflicts for other collaborators.

Instead, use it for cleaning up your local commit history before merging it into a shared team branch.

How far back do you want to go?
```
c1 <--- c2 <--- c3 <--- c4
```

```
git rebase -i HEAD~3
```


- git revert
- git reset
- git rebase


Tools
```
git log --oneline
```