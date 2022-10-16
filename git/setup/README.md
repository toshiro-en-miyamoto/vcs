# Setting up `git`

## Single account

Refer to [git credential storage](https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage).

```bash
$ git config --global user.email "your mail.addr"
$ git config --global user.name "Your Name"
$ git config --global credential.helper store
```

Refer to [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

```bash
$ ssh-keygen -t ed25519 -C "your_email@example.com"
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_ed25519
```

Then add the public key in your git accout.

```bash
$ xsel --clipboard < ~/.ssh/id_ed25519.pub
```

Now you can use `ssh:` to clone repositories.

## [Multiple accounts](https://gist.github.com/oanhnn/80a89405ab9023894df7)

Generate ssh key pairs.

```bash
$ ssh-keygen -t ed25519 -C "your_email@example1.com"
> Enter "example1_ed25519" as the file name
> The passphrase can be empty

$ ssh-keygen -t ed25519 -C "your_email@example2.com"
> Enter "example2_ed25519" as the file name
> The passphrase can be empty
```

Add them to Github accounts.

```bash
on Linux
$ xsel --clipboard < ~/.ssh/example1_ed25519.pub
$ xsel --clipboard < ~/.ssh/example2_ed25519.pub

on Moac
$ pbcopy < ~/.ssh/example1_ed25519.pub
$ pbcopy < ~/.ssh/example2_ed25519.pub
```

Edit `.ssh/config` for the accounts.

```
# Default account
Host github.com
    Hostname github.com
    IdentityFile ~/.ssh/example1_ed25519
    IdentitiesOnly yes

# Other account
Host github-example2
    Hostname github.com
    IdentityFile ~/.ssh/example2_ed25519
    IdentitiesOnly yes
```

Add the private keys to the agent.

```bash
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/example1_ed25519
$ ssh-add ~/.ssh/example2_ed25519
```

[Test SSH connection](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection).

```bash
$ ssh -T git@github.com
```

If it is the first access to `github.com` with SSH, you may see a warning like this:

```
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Verify that the fingerprint in the message you see matches GitHub's public key fingerprint. If it does, then type `yes`:

```
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
```

Now the `~/.ssh/knwon_hosts` file contains `github.com`, and


```
> Hi [username]! You've successfully authenticated, but GitHub does not provide shell access.
```

Verify that the resulting message contains your `[username]`.

```bash
$ ssh -T git@github-example2
```

If the authentication failed, you would see an error message like this:

```
git@github.com: Permission denied (publickey).
```
