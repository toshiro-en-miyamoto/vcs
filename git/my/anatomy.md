# Anatomy of Git

Refer to:

- Git [Reference](https://git-scm.com/docs/)
- Git Repository [Layout](https://git-scm.com/docs/gitrepository-layout)
- A Git [Glossary](https://git-scm.com/docs/gitglossary)
- [`git-init`](https://git-scm.com/docs/git-init)

## Newborn Git repositories

A Git repository comes in two different flavours [Layout]:

- a `.git` directory at the root of the working tree;
- a `<project>.git` directory that is a bare repository (i.e. without its own working tree), that is typically used for exchanging histories with others by pushing into it and fetching from it.

Let's look what the `git init` command for a plain directory does:

```bash
repo $ mkdir abc
repo $ cd abc

abc $ git init -b main
Initialized empty Git repository in ~/repo/abc/.git/
```

With that,

- `~/repo/abc/` has become a new working directory
- `~/repo/abc/.git/` is a Git repository associated with the working directory
- the initial branch in the repository is `main`

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

This command creates an empty Git repository - basically a `.git` directory with subdirectories for `objects`, `refs/heads`, `refs/tags`, and template files. An initial branch without any commits will be created (see the `--initial-branch` option below for its name). [`git-init`]

- If the `$GIT_DIR` environment variable is set then it specifies a path to use instead of `./.git` for the base of the repository.
- If the object storage directory is specified via the `$GIT_OBJECT_DIRECTORY` environment variable then the sha1 directories are created underneath; otherwise, the default `$GIT_DIR/objects` directory is used.

### `HEAD` file

The `HEAD` file specifies the current branch. In more detail: Your working tree is normally derived from the state of the tree referred to by `HEAD`. `HEAD` is a reference to one of the heads in your repository, except when using a detached `HEAD`, in which case it directly references an arbitrary commit. [Glossary]

The `HEAD` is a *symref* (see below) to the `refs/heads/` namespace describing the currently active branch. It does not mean much if the repository is not associated with any working tree (i.e. a bare repository), but a valid Git repository must have the `HEAD` file; some porcelains may use it to guess the designated *default* branch of the repository (usually `master`). [Layout]

```bash
abc $ cat .git/HEAD
ref: refs/heads/main
```

The symref stands for *symbolic reference*: instead of containing the SHA-1 id itself, it is of the format `ref: refs/some/thing` and when referenced, it recursively dereferences to this reference. `HEAD` is a prime example of a `symref`. [Glossary]

A *ref* is a name that points to an object name or another ref (the latter is called a symbolic ref). The ref namespace is hierarchical. Ref names must either start with `refs/` or be located in the root of the hierarchy. For the latter, their name must follow these rules: [Glossary]

- The name consists of only upper-case characters or underscores.
- The name ends with `_HEAD` or is equal to `HEAD`.

### `config` file

Repository specific configuration file. [Layout]

```bash
abc $ cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true

abc $ git config --list --local --show-origin
file:.git/config        core.repositoryformatversion=0
file:.git/config        core.filemode=true
file:.git/config        core.bare=false
file:.git/config        core.logallrefupdates=true
```

### Object database

An *object database* stores a set of *objects*, and an individual object is identified by its object name. The objects usually live in `$GIT_DIR/objects/`. [Glossary]

- The object is the unit of storage in Git. It is uniquely identified by the SHA-1 of its contents. Consequently, an object cannot be changed.
- The *object name*, the unique identifier of an object, is usually represented by a 40 character hexadecimal string

The type of an object falls into `commit`, `tree`, `tag` or `blob`. [Glossary]

- A commit objects contains the information about a particular revision, such as parents, committer, author, date and the tree object which corresponds to the top directory of the stored revision.
- A tree object contains a list of file names and modes along with refs to the associated blob and/or tree objects. A tree is equivalent to a directory.
- A tag object contains a ref pointing to another object, which can contain a message just like a commit object. It can also contain a (PGP) signature, in which case it is called a *signed tag object*.
- A blob object is an untyped object, e.g. the contents of a file.

*Ish*'es are:

- *Commit-ish* is a commit object or an object that can be recursively dereferenced to a commit object. The following are all commit-ish'es: a commit object, a tag object that points to a commit object, a tag object that points to a tag object that points to a commit object, etc.
- *Tree-ish* ia a tree object or an object that can be recursively dereferenced to a tree object. Dereferencing a commit object yields the tree object corresponding to the revision's top directory. The following are all tree-ish'es: a commit-ish, a tree object, a tag object that points to a tree object, a tag object that points to a tag object that points to a tree object, etc.

