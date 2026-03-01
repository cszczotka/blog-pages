## How to revert last commit from remote branch

###  Option with git reset

To remote last commit  (16864573) in git remote branch (develop) run in console

```bash

git fetch .

git reset --hard 16864573

git push origin develop --force

```

###  Option with git revert


To remote last commit  (16864573) in git remote branch (develop) run in console

```bash

git fetch .

git revert --no-commit 16864573..HEAD

git commit -m "Revert to commit a16864573"

git push origin develop

```

