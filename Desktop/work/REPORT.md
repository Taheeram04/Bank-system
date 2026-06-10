# Git Project Report

## 1. Setup

```bash
git config --global user.name "Jim Weirich"
git config --global user.email "jim@weirichhouse.org"
```

---

## 2. Git Commits to Commit

```bash
mkdir -p work/hello && cd work/hello
git init
# Created hello.sh with: echo "Hello, World"
git add hello.sh
git commit -m "Initial commit"

# Updated hello.sh to use $1 argument
git add hello.sh
git commit -m "Use argument for name"

# Added comment line only (two-step commit)
git add hello.sh
git commit -m "Add default comment"

# Added lines 4-5 (name variable + echo)
git add hello.sh
git commit -m "Use name variable with World default"
```

---

## 3. History

```bash
git log                                                            # full history
git log --oneline                                                  # one-line
git log -2                                                         # last 2
git log --since="5 minutes ago"                                    # last 5 min
git log --pretty=format:"* %h %ad | %s%d [%an]" --date=short     # personalized
```

---

## 4. Check It Out

```bash
git checkout <first-commit-hash>   # first snapshot
cat hello.sh
git checkout HEAD~1                # second most recent
cat hello.sh
git checkout main                  # back to latest
```

---

## 5. TAG Me

```bash
git tag v1                # tag current as v1
git tag v1-beta HEAD~1    # tag previous as v1-beta
git checkout v1-beta      # navigate to v1-beta
git checkout v1           # navigate to v1
git tag                   # list all tags
git checkout main
```

---

## 6. Changed Your Mind?

```bash
# Revert unstaged
git checkout hello.sh

# Revert staged
git add hello.sh
git reset HEAD hello.sh
git checkout hello.sh

# Revert committed
git add hello.sh && git commit -m "Add unwanted comment"
git revert HEAD --no-edit

# Tag oops, reset to v1
git tag oops
git reset --hard v1
git log --all --oneline        # show deleted commits
git tag -d oops && git gc      # clean up

# Add author comment
git add hello.sh && git commit -m "Add author comment"

# Amend to add email (no new commit)
git add hello.sh
git commit --amend --no-edit
```

---

## 7. Move It

```bash
mkdir lib
git mv hello.sh lib/hello.sh
git commit -m "Move hello.sh to lib/"

# Created Makefile with tab-indented run target
git add Makefile
git commit -m "Add Makefile"
```

---

## 8. Blobs, Trees and Commits

```bash
ls .git/             # explore .git directory

# .git/objects/ — stores all file data as blobs, trees, and commits
# .git/refs/    — branch and tag pointers
# .git/HEAD     — points to current branch
# .git/config   — repository configuration

git cat-file -t <hash>           # print object type
git cat-file -p <hash>           # print object content
git ls-tree HEAD                 # dump directory tree
git ls-tree HEAD lib/            # dump lib/ tree
git cat-file -p HEAD:lib/hello.sh  # dump file content
```

---

## 9. Branching

```bash
git checkout -b greet             # create and switch to greet
# created lib/greeter.sh
git add lib/greeter.sh && git commit -m "Add greeter.sh"
# updated lib/hello.sh to use Greeter
git add lib/hello.sh && git commit -m "Update hello.sh to use Greeter"
# updated Makefile with comment
git add Makefile && git commit -m "Add comment to Makefile"

git checkout main
git diff main greet -- Makefile
git diff main greet -- lib/hello.sh
git diff main greet -- lib/greeter.sh

echo "This is the Hello World example from the git project." > README.md
git add README.md && git commit -m "Add README"

git log --all --oneline --graph --decorate    # commit tree
```

---

## 10. Conflicts, Merging and Rebasing

```bash
# Merge main into greet
git checkout greet
git merge main

# Update hello.sh on main, commit
git checkout main
git add lib/hello.sh && git commit -m "Prompt user for name"

# Merge main into greet (causes conflict)
git checkout greet
git merge main     # CONFLICT in lib/hello.sh
# Resolve conflict manually: keep the main version
git add lib/hello.sh
git commit -m "Resolve merge conflict, accept main changes"

# Rebase greet onto main
git reset --hard <commit-before-merge>
git rebase main

# Merge greet into main
git checkout main
git merge greet
```

### Fast-forwarding vs Merging vs Rebasing

**Fast-forward**: When the target branch has no new commits since the branch diverged, Git simply moves the branch pointer forward. No merge commit is created. Clean and linear.

**Merge**: Combines both branches with a new merge commit. Preserves the full history of both branches but creates a "diamond" shape in the log.

**Rebase**: Replays commits from one branch on top of another. Creates a linear history as if the work was always sequential. Rewrites commit hashes — do not rebase shared/public branches.

---

## 11. Local and Remote Repositories

```bash
cd work/
git clone hello cloned_hello          # clone
cd cloned_hello
git log --oneline                      # show logs
git remote                             # show remote name
git remote show origin                 # remote details
git branch -a                          # all branches

# After updating README in original:
git fetch origin
git log --all --oneline               # see new commits
git merge origin/main                 # merge remote changes

git branch --track greet origin/greet  # track remote greet
```

**The single command equivalent to fetch + merge is:**
```bash
git pull
```

---

## 12. Bare Repositories

A **bare repository** has no working directory — it contains only the Git data (like a `.git/` folder without the project files). It is used as a **central shared repository** that multiple developers push to and pull from. You cannot commit directly into a bare repo.

```bash
git clone --bare hello hello.git         # create bare repo

cd hello
git remote add shared ../hello.git       # add as remote

# Update README and push
git add README.md && git commit -m "Update README for shared"
git push shared main

# Pull in cloned_hello
cd ../cloned_hello
git remote add shared ../hello.git
git pull shared main
```
