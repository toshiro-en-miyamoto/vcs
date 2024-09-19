# Anatomy of Git

Refer to:

- [Pro Git, 2nd Edition](https://git-scm.com/book/en/v2) by Scott Chacon
- Git [Reference](https://git-scm.com/docs/)
- Git Repository [Layout](https://git-scm.com/docs/gitrepository-layout)
- A Git [Glossary](https://git-scm.com/docs/gitglossary)
- [`git-init`](https://git-scm.com/docs/git-init)
  [`git-add`](https://git-scm.com/docs/git-add)

## Newborn Git repositories

A Git repository comes in two different flavours [Layout]:

- a `.git` directory at the root of the working tree;
- a `<project>.git` directory that is a bare repository (i.e. without its own working tree), that is typically used for exchanging histories with others by pushing into it and fetching from it.

Let's look what the `git init` command for a plain directory does:

```bash
repo $ mkdir abc
repo $ cd abc

abc $ git init
Initialized empty Git repository in ~/repo/abc/.git/
```

With that,

- `~/repo/abc/` has become a new working directory
- `~/repo/abc/.git/` is a Git repository associated with the working directory
- the initial branch in the repository is `master`

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
ref: refs/heads/master
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

### Adding files

Let's add three test files:

```bash
bc $ cat README
== Testing library
This library is used to test Ruby projects.

abc $ cat LICENSE
The MIT License
Copyright (c) 2000 Scott Chacon

abc $ cat ut/test.rb
require 'logger'
require 'test/unit'

class Test::Unit::TestCase

abc $ git add .

abc $ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   LICENSE
	new file:   README
	new file:   ut/test.rb
```

The `git add .` has created four files:

- `.git/index`
- `.git/objects/10/b2ec8f74f8b3ba7dd3997f88fb5083b35c7e6d`
- `.git/objects/3c/ea8dcb6e1e956f82ea54f6b5b5a387269bd092`
- `.git/objects/70/45d335f681333340d0c089942aa38dd296ddf6`

### `index` file

The `git add .` has created `.git/index` file:

```bash
abc $ hexdump -C .git/index
00000000  44 49 52 43 00 00 00 02  00 00 00 03 66 ec 08 e0  |DIRC........f...|
00000010  16 29 de 37 66 ec 08 e0  15 fc 17 7d 00 00 b3 02  |.).7f......}....|
00000020  00 08 1f d8 00 00 81 a4  00 00 03 ea 00 00 03 ea  |................|
00000030  00 00 00 30 70 45 d3 35  f6 81 33 33 40 d0 c0 89  |...0pE.5..33@...|
00000040  94 2a a3 8d d2 96 dd f6  00 07 4c 49 43 45 4e 53  |.*........LICENS|
00000050  45 00 00 00 66 ec 08 b0  24 4b 9f 1f 66 ec 08 b0  |E...f...$K..f...|
00000060  24 1d d8 64 00 00 b3 02  00 08 1c 60 00 00 81 a4  |$..d.......`....|
00000070  00 00 03 ea 00 00 03 ea  00 00 00 3f 3c ea 8d cb  |...........?<...|
00000080  6e 1e 95 6f 82 ea 54 f6  b5 b5 a3 87 26 9b d0 92  |n..o..T.....&...|
00000090  00 06 52 45 41 44 4d 45  00 00 00 00 66 ec 09 14  |..README....f...|
000000a0  32 b2 f3 b0 66 ec 09 14  2f 7a fc 6d 00 00 b3 02  |2...f.../z.m....|
000000b0  00 08 21 7f 00 00 81 a4  00 00 03 ea 00 00 03 ea  |..!.............|
000000c0  00 00 00 41 10 b2 ec 8f  74 f8 b3 ba 7d d3 99 7f  |...A....t...}...|
000000d0  88 fb 50 83 b3 5c 7e 6d  00 0a 75 74 2f 74 65 73  |..P..\~m..ut/tes|
000000e0  74 2e 72 62 00 00 00 00  00 00 00 00 84 5b 69 55  |t.rb.........[iU|
000000f0  79 2c 43 e4 f9 fc 40 0e  a4 04 7f e3 14 04 95 70  |y,C...@........p|
00000100
```

The `git add` command adds files contents to the *index*. [`git-add`]

- This command updates the index using the current content found in the working tree, to prepare the content staged for the next commit.
- The index holds a snapshot of the content of the working tree, and it is this snapshot that is taken as the contents of the next commit.
-  Thus after making any changes to the working tree, and before running the `commit` command, you must use the `add` command to add any new or modified files to the index.

The index is a collection of files with stat information, whose contents are stored as objects. The index is a stored version of your working tree. [Glossary]

The index is the current index file for the repository. It is usually not found in a bare repository. [Layout]

[Git format: Index](https://git-scm.com/docs/gitformat-index)

| field name		| length  	| value | value | value
|---------------|----------:|-------|-------|-------
| signature			| 4 bytes 	| "DIRC" |
| version				| 32 bits 	| `00 00 00 02` |
| # of entries	| 32 bits		| `00 00 00 03` |
| ctime sec			| 32 bits		| `66 ec 08 e0` | `66 ec 08 b0` | `66 ec 09 14` |
| ctime nano		| 32 bits 	| `16 29 de 37` | `24 4b 9f 1f` | `32 b2 f3 b0` |
| mtime sec			| 32 bits		| `66 ec 08 e0` | `66 ec 08 b0` | `66 ec 09 14` |
| mtime nano		| 32 bits		| `15 fc 17 7d` | `24 1d d8 64` | `2f 7a fc 6d` |
| dev						| 32 bits		| `00 00 b3 02` | `00 00 b3 02` | `00 00 b3 02` |
| ino						| 32 bits		| `00 08 1f d8` | `00 08 1c 60` | `00 08 21 7f` |
| mode					| 32 bits 	| `00 00 81 a4` | `00 00 81 a4` | `00 00 81 a4` |
| uid						| 32 bits		| `00 00 03 ea` | `00 00 03 ea` | `00 00 03 ea` |
| gid						| 32 bits		| `00 00 03 ea` | `00 00 03 ea` | `00 00 03 ea` |
| file size			| 32 bits		| `00 00 00 30` | `00 00 00 3f` | `00 00 00 41` |
| object name		| 160 bits	| `70 45 d3 ..`	| `3c ea 8d ..` | `10 b2 ec ..` |
| flag					| 16 bits		| `00 07`				| `00 06` 			| `00 0a` 			|
| path name			| variable	| LICENSE 			| README 				| ut/test.rb 		|
| filler				| variable	| 3 * `00`			| 4 * `00` 			| 8 * `00` 			|
| index checksum	| 160 bits	| `84 5b 69 ..` |

### `blob` objects

The `git add .` has created three object files as well:

```bash
abc $ find .git/objects -type f
.git/objects/10/b2ec8f74f8b3ba7dd3997f88fb5083b35c7e6d
.git/objects/3c/ea8dcb6e1e956f82ea54f6b5b5a387269bd092
.git/objects/70/45d335f681333340d0c089942aa38dd296ddf6
```

They are named with the SHA-1 checksum of the content and its header. The subdirectory is named with the first 2 characters of the SHA-1, and the filename is the remaining 38 characters.

```bash
abc $ cat README | git hash-object --stdin
3cea8dcb6e1e956f82ea54f6b5b5a387269bd092

abc $ cat LICENSE | git hash-object --stdin
7045d335f681333340d0c089942aa38dd296ddf6

abc $ cat ut/test.rb | git hash-object --stdin
10b2ec8f74f8b3ba7dd3997f88fb5083b35c7e6d
```

But the object files are not plain text files:

```bash
abc $ hexdump -C .git/objects/3c/ea8dcb6e1e956f82ea54f6b5b5a387269bd092
00000000  78 01 4b ca c9 4f 52 30  33 66 b0 b5 55 08 49 2d  |x.K..OR03f..U.I-|
00000010  2e c9 cc 4b 57 c8 c9 4c  2a 4a 2c aa e4 0a c9 c8  |...KW..L*J,.....|
00000020  2c 86 71 14 80 cc d2 e2  d4 14 85 92 7c 85 12 a0  |,.q.........|...|
00000030  3a 85 a0 d2 a4 4a 85 82  a2 fc ac d4 e4 92 62 3d  |:....J........b=|
00000040  2e 00 61 53 18 a1                                 |..aS..|
00000046

abc $ hexdump -C .git/objects/70/45d335f681333340d0c089942aa38dd296ddf6
00000000  78 01 4b ca c9 4f 52 30  b1 60 08 c9 48 55 f0 f5  |x.K..OR0.`..HU..|
00000010  0c 51 f0 c9 4c 4e cd 2b  4e e5 72 ce 2f a8 2c ca  |.Q..LN.+N.r./.,.|
00000020  4c cf 28 51 d0 48 d6 54  30 32 30 30 50 08 4e ce  |L.(Q.H.T0200P.N.|
00000030  2f 29 51 70 ce 48 4c ce  cf e3 02 00 ed 9a 11 56  |/)Qp.HL........V|
00000040

abc $ hexdump -C .git/objects/10/b2ec8f74f8b3ba7dd3997f88fb5083b35c7e6d
00000000  78 01 4b ca c9 4f 52 30  33 65 28 4a 2d 2c cd 2c  |x.K..OR03e(J-,.,|
00000010  4a 55 50 cf c9 4f 4f 4f  2d 52 e7 82 0b 94 a4 16  |JUP..OOO-R......|
00000020  97 e8 97 e6 65 96 a8 73  71 25 e7 24 16 17 2b 84  |....e..sq%.$..+.|
00000030  00 45 ac ac 42 81 42 56  56 20 b6 73 62 71 2a 17  |.E..B.BVV .sbq*.|
00000040  00 94 73 18 d2                                    |..s..|
00000045
```

Once you have content in your object database, you can examine that content with the `git cat-file` command. Passing `-p` to `cat-file` instructs the command to first figure out the type of content, then display it appropriately: [§10.2, Pro Git 2e]

```bash
abc $ git cat-file -p 3cea8d
== Testing library
This library is used to test Ruby projects.

abc $ git cat-file -p 7045d3
The MIT License
Copyright (c) 2000 Scott Chacon

abc $ git cat-file -p 10b2ec
require 'logger'
require 'test/unit'

class Test::Unit::TestCase
```

You can have Git tell you the object type of any object in Git, given its SHA-1 key, with `git cat-file -t`: [§10.2, Pro Git 2e]

```bash
abc $ git cat-file -t 3cea8d
blob

abc $ git cat-file -t 7045d3
blob

abc $ git cat-file -t 10b2ec
blob
```

### `commit` objects

```bash
abc $ cat .git/HEAD

abc $ cat .git/refs/heads/main

abc $ git cat-file -t xyz

abc $ git cat-file -p xyz
```


