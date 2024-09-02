# Git commands

| ch | command  | description
|----|----------|-------------
| 01 | config   | Get and set repository or global options
| 02 | init     | Create an empty Git repository or reinitialize an existing one
| 02 | clone    | Clone a repository into a new directory
| 02 | status   | Show the working tree status
| 02 | add      | Add file contents to the index
| 02 | diff     | Show changes between commits, commit and working tree, etc
| 02 | commit   | Record changes to the repository
| 02 | rm       | Remove files from the working tree and from the index
| 02 | mv       | Move or rename a file, a directory, or a symlink
| 02 | log      | Show commit logs
| 02 | reset    | Reset current HEAD to the specified state
| 02 | checkout | Switch branches or restore working tree files
| 02 | restore  | Restore working tree file
| 02 | remote   | Manage set of tracked repositories
| 02 | push     | Update remote refs along with associated objects
| 02 | pull     | Fetch from and integrate with another repository or a local branch
| 02 | fetch    | Download objects and refs from another repository

## `config`

```bash
~ $ git config --list --show-origin
file:/home/user/.gitconfig  user.email=your.email@sample.com
file:/home/user/.gitconfig  user.name=your name
file:/home/user/.gitconfig  credential.helper=store

vcs $ git config --list --show-origin
file:/home/user/.gitconfig  user.email=your.email@sample.com
file:/home/user/.gitconfig  user.name=your name
file:/home/user/.gitconfig  credential.helper=store
file:.git/config  core.repositoryformatversion=0
file:.git/config  core.filemode=true
file:.git/config  core.bare=false
file:.git/config  core.logallrefupdates=true
file:.git/config  remote.origin.url=git@192.168.0.42:the-repo/vcs.git
file:.git/config  remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
file:.git/config  branch.main.remote=origin
file:.git/config  branch.main.merge=refs/heads/main
file:.git/config  branch.main.vscode-merge-base=origin/main
```

## `clone`

```bash
repo $ git clone git@192.168.0.42:the-repo/vcs.git
Cloning into 'vcs'...
remote: Enumerating objects: 11, done.
remote: Counting objects: 100% (11/11), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 11 (delta 0), reused 8 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (11/11), 4.54 KiB | 1.13 MiB/s, done.
```

## `status`

```bash
vcs $ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.vscode/
	git/chacon/commands.md

nothing added to commit but untracked files present (use "git add" to track)
```

## `add`

To begin tracking files in the `.vscode` directory:

```bash
vcs $ git add .vscode/

vcs $ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .vscode/settings.json

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	git/chacon/commands.md
```

Upon editing the `settings.json`:

```bash
vcs $ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .vscode/settings.json

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   .vscode/settings.json

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	git/chacon/commands.md
```

Git also has a short status flag so you can see your changes in a more compact way.

```bash
vcs $ git status -s
AM .vscode/settings.json
?? git/chacon/commands.md
```

## `diff`

To see what you’ve changed but not yet staged, type `git diff` with no other arguments:

```bash
vcs $ git diff
diff --git a/.vscode/settings.json b/.vscode/settings.json
index 261bc28..52ec4ca 100644
--- a/.vscode/settings.json
+++ b/.vscode/settings.json
@@ -1,7 +1,9 @@
 {
   "cSpell.words": [
+    "chacon",
     "filemode",
     "logallrefupdates",
-    "repositoryformatversion"
+    "repositoryformatversion",
+    "unstage"
   ]
 }
\ No newline at end of file
```

`git diff --staged` compares your staged changes to your last commit:

```bash
vcs $ git diff --staged
diff --git a/.vscode/settings.json b/.vscode/settings.json
new file mode 100644
index 0000000..261bc28
--- /dev/null
+++ b/.vscode/settings.json
@@ -0,0 +1,7 @@
+{
+  "cSpell.words": [
+    "filemode",
+    "logallrefupdates",
+    "repositoryformatversion"
+  ]
+}
\ No newline at end of file
```

## `commit`

Before committing, make sure to update the index using the current content found in the working tree, to prepare the content staged for the next commit.

```bash
vcs $ git add .

vcs $ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .vscode/settings.json
	new file:   git/chacon/commands.md

vcs $ git commit -m "git commands first commit"
[main 9858ef9] git commands first commit
  2 files changed, 162 insertions(+)
  create mode 100644 .vscode/settings.json
  create mode 100644 git/chacon/commands.md
```

## `log`

`git log` lists each commit with its SHA-1 checksum, the author’s name and email, the date written, and the
commit message.

```bash
vcs $ git log
commit 9858ef93c56719065fcc37e31cc4554a85f36a0a (HEAD -> main)
Author: your name <your.email@sample.com>
Date:   Mon Sep 2 19:04:29 2024 +0800

    git commands first commit
```

One of the more helpful options is `-p` or `--patch`, which shows the difference (the patch output) introduced in each commit.

```bash
vcs $ git log --patch
commit 9858ef93c56719065fcc37e31cc4554a85f36a0a (HEAD -> main)
Author: your name <your.email@sample.com>
Date:   Mon Sep 2 19:04:29 2024 +0800

    git commands first commit

diff --git a/.vscode/settings.json b/.vscode/settings.json
new file mode 100644
index 0000000..52ec4ca
--- /dev/null
+++ b/.vscode/settings.json
@@ -0,0 +1,9 @@
+{
+  "cSpell.words": [
+    "chacon",
+    "filemode",
+    "logallrefupdates",
+    "repositoryformatversion",
+    "unstage"
+  ]
+}
\ No newline at end of file

diff --git a/git/chacon/commands.md b/git/chacon/commands.md
new file mode 100644
index 0000000..7e39024
--- /dev/null
+++ b/git/chacon/commands.md
@@ -0,0 +1,153 @@
+# Git commands
+
+| ch | command  | description
+|----|----------|-------------
+| 01 | config   | Get and set repository or global options
+| 02 | init     | Create an empty Git repository or reinitialize an existing one
+| 02 | clone    | Clone a repository into a new directory
```

If you want to see some abbreviated stats for each commit, you can use the `--stat` option:

```bash
vcs $ git log --stat
commit 9858ef93c56719065fcc37e31cc4554a85f36a0a (HEAD -> main)
Author: your name <your.email@sample.com>
Date:   Mon Sep 2 19:04:29 2024 +0800

    git commands first commit

 .vscode/settings.json  |   9 +++
 git/chacon/commands.md | 153 +++++++++++++++++++++++++++++++++++++++++++++++++
 2 files changed, 162 insertions(+)

commit 860d484d3b898a18706a16ad77658c4cdd78dfb6 (origin/main, origin/HEAD)
Author: your name <your.email@sample.com>
Date:   Sun Oct 16 15:09:34 2022 +0800

    migrated from build repo

 git/chacon/README.md |   5 ++
 git/chacon/ch03.md   | 245 +++++++++++++++++++++++++++++++++++++++++++++++++++
 git/setup/README.md  | 117 ++++++++++++++++++++++++
 3 files changed, 367 insertions(+)

commit 7b3561fc5087867be111d72d39073f44a618df3c
Author: your name <your.email@sample.com>
Date:   Sun Oct 16 15:07:28 2022 +0800

    Initial commit

 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

Another really useful option is `--pretty`. This option changes the log output to formats other than the default. A few prebuilt option values are available for you to use. The `oneline` value for this option prints each commit on a single line, which is useful if you’re looking at a lot of commits.

```bash
vcs $ git log --pretty=oneline
9858ef93c56719065fcc37e31cc4554a85f36a0a (HEAD -> main) git commands first commit
860d484d3b898a18706a16ad77658c4cdd78dfb6 (origin/main, origin/HEAD) migrated from build repo
7b3561fc5087867be111d72d39073f44a618df3c Initial commit
```

The most interesting option value is `format`, which allows you to specify your own log output format.

```bash
vcs $ git log --pretty=format:"%h %cd %s"
9858ef9 Mon Sep 2 19:04:29 2024 +0800 git commands first commit
860d484 Sun Oct 16 15:09:34 2022 +0800 migrated from build repo
7b3561f Sun Oct 16 15:07:28 2022 +0800 Initial commit
```

The `oneline` and `format` option values are particularly useful with another log option called `--graph`. This option adds a nice little ASCII graph showing your branch and merge history:

```bash
$ git log --pretty=format:"%h %s" --graph
* 2d3acf9 Ignore errors from SIG on trap
* 5e3ee11 Merge branch 'master' of ...
|\
| * 420eac9 Add method for getting the current branch
* | 30e367c Timeout code and tests
```

## `remote`

```bash
vcs $ git remote --verbose
origin	git@192.168.0.42:the-repo/vcs.git (fetch)
origin	git@192.168.0.42:the-repo/vcs.git (push)

vcs $ git remote show origin
* remote origin
  Fetch URL: git@192.168.0.42:the-repo/vcs.git
  Push  URL: git@192.168.0.42:the-repo/vcs.git
  HEAD branch: main
  Remote branch:
    main tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (fast-forwardable)
```

## `push`

`git push` updates remote refs along with associated objects:

```
git push [<repository> [<refspec>...]]

vcs $ git add .

vcs $ git commit -m "git commands up to push"
[main b425d74] git commands up to push
 2 files changed, 200 insertions(+)

vcs $ git push
Enumerating objects: 17, done.
Counting objects: 100% (17/17), done.
Delta compression using up to 4 threads
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 4.09 KiB | 838.00 KiB/s, done.
Total 14 (delta 3), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (3/3), done.
To git@192.168.0.42:the-repo/vcs.git
   860d484..b425d74  main -> main
```

When the command line does not specify where to push with the `repository` argument, `branch.*.remote` configuration for the current branch is consulted to determine where to push. If the configuration is missing, it defaults to `origin`.

```bash
vcs $ git config --list --local
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
remote.origin.url=git@192.168.0.42:the-repo/vcs.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.main.remote=origin
branch.main.merge=refs/heads/main
branch.main.vscode-merge-base=origin/main
```
