# Git commands

| ch | command  | description
|----|----------|-------------
| 01 | config   | Get and set repository or global options
| 02 | init     | Create an empty Git repository or reinitialize an existing one
| 02 | clone    | Clone a repository into a new directory
| 02 | status   | Show the working tree status
| 02 | add      | Add file contents to the index
| 02 | diff     | Show changes between commits, commit and working tree, etc

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
