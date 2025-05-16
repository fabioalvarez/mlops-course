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

Once you have selected the commits you want to edit, you will be presented with a list of commits in your text editor.
Each commit will be listed with a command next to it, such as "pick" or "edit". You can change the command for each commit
to perform different actions.

## Cherry Picking
Allows you to integrate specific commits from one branch into another branch.
This is useful when you want to apply a specific change from one branch to another without merging the entire branch.
Another situation is when you merge a commit in the wrong branch and you want to move it to the right one.

For example, if you have a commit in the "main" branch that you want to apply to the "feature" branch, you can cherry-pick that commit.
1. Identify the commit you want to cherry-pick
2. Switch to the target branch, in this case is feature
3. Cherry-pick the commit: `git cherry-pick <commit-hash>`

But the commit is still in the main branch:
1. Switch to the main branch
2. git reset --hard HEAD~1


Next steps:
1. When to use git reset?
2. Tutorial min: 12:04 (Advanced)

=======================================================================

- git revert
- git reset
- git rebase




Tools
```
git log --oneline
```