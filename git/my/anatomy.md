# Anatomy of Git

Refer to:

- Git [Reference](https://git-scm.com/docs/)
- Git Repository [Layout](https://git-scm.com/docs/gitrepository-layout)
- A Git [Glossary](https://git-scm.com/docs/gitglossary)

## newborn Git repositories

A Git repository comes in two different flavours [Layout]:

- a `.git` directory at the root of the working tree;
- a `<project>.git` directory that is a bare repository (i.e. without its own working tree), that is typically used for exchanging histories with others by pushing into it and fetching from it.

The `git init` in a plain directory sets up a Git repository in there:

```bash
repo $ mkdir abc
repo $ cd abc

abc $ git init -b main
Initialized empty Git repository in ~/repo/abc/.git/
```

With that,

- `~/repo/abc/` has become a new working directory
- `~/repo/abc/.git/` is its Git repository

In the repository, nine directories and four files have been created:

```bash
abc $ find .git -type d
.git
.git/refs
.git/refs/tags
.git/refs/heads
.git/info
.git/hooks
.git/branches
.git/objects
.git/objects/info
.git/objects/pack

abc $ find .git -type f | sed /sample/d
.git/info/exclude
.git/description
.git/HEAD
.git/config
```

### `HEAD` file

A `symref` (see below) to the `refs/heads/` namespace describing the currently active branch. It does not mean much if the repository is not associated with any working tree (i.e. a bare repository), but a valid Git repository must have the `HEAD` file; some porcelains may use it to guess the designated *default* branch of the repository (usually `master`). [Layout]

```bash
abc $ cat .git/HEAD
ref: refs/heads/main
```

`symref` stands for *symbolic reference*: instead of containing the SHA-1 id itself, it is of the format `ref: refs/some/thing` and when referenced, it recursively dereferences to this reference. `HEAD` is a prime example of a `symref`. [Glossary]

### `config` file

Repository specific configuration file. [Layout]

```bash
abc $ cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true

abc $ git config --list --show-origin
file:~/.gitconfig   user.email=your@mail.com
file:~/.gitconfig   user.name=John Doe
file:~/.gitconfig   credential.helper=store
file:.git/config    core.repositoryformatversion=0
file:.git/config    core.filemode=true
file:.git/config    core.bare=false
file:.git/config    core.logallrefupdates=true
```

