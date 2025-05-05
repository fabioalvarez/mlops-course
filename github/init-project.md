## Configure git
```bash
  git config --global user.name "Fabio Alvarez"
```

```bash
  git config --global user.email username@exmaple.com
```

## Create a new repository on the command line
```bash
  echo "# my-project" >> README.md
```

```bash
  git init
```

```bash
  git add README.md
```

```bash
  git commit -m "first commit"
```

```bash
  git branch -M main
```

```bash
    git remote add origin https:github.com/fabioalvarez/my-project.git
```

## Github commands

`git status`: Check the status of the repository

```bash
  git status
```

`git add <file>...`: Add a file to the staging area

```bash
  git add <file>...
```

`git restore --staged <file>...`: Unstage a file

```bash
  git restore --staged <file>...
```

`git commit -m "<message>"`: Commit changes to the repository

```bash
  git commit -m "<message>"
```

`git branch`: List all branches

```bash
  git branch
```

`git remote -v`: The git remote -v command displays a list of all remote repositories that
your local repository is connected to, along with their URLs. The -v flag stands for "verbose",
which shows both the fetch and push URLs for each remote. 

```bash
  git remote -v
```

`git push`: Push changes to the remote repository

The -u flag in git push -u origin main stands for --set-upstream. This flag does two things:

Pushes your local "main" branch to the "main" branch on the remote repository named "origin"
Sets up tracking between your local branch and the remote branch
Setting up tracking is important because:

It establishes a relationship between your local branch and the remote branch
After setting this, you can use shorter commands like just git push and git pull without specifying the remote and branch each time
Git will remember which remote branch your local branch should sync with
The first time you push a branch to a remote, you should use the -u flag. For subsequent pushes to the same branch, you can simply use git push.

```bash
  git push -u origin main
```

`git pull`: Pull changes from the remote repository

```bash
  git pull
```

