# Setting Up GitHub With Multiple Accounts

Original Source:: https://www.freecodecamp.org/news/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca

## Generate SSH keys (If Needed)

If you already have keys (typically stored in `~/.ssh`), you can skip this step or just perform it for the other accounts.

Generate a key pair for the "default" account:

```
ssh-keygen -t rsa
```

This will create the following two files:

```
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

The first file is the private key. Do not share this.

The second file is the public key. This is the one to share.

Create a key pair for the "other" account:

```
ssh-keygen -t rsa -C "other@other_email.com" -f "id_rsa_other"
```

This will create the following two files:

```
~/.ssh/id_rsa_other
~/.ssh/id_rsa_other.pub
```

These are the key pair files for the "other" account. I'm only using "other" as an example. You can use whatever you'd like and you can generate more than 2 key pairs.

## Add keys to GitHub accounts

For each GitHub account:

1. Log in
2. Go to Settings > SSH and GPG keys
3. Click on "New SSH Key"
4. Paste contents of `id_rsa.pub` (or `id_rsa_other.pub`) into the form (easiest command to copy to clipboard is `pbcopy < ~/.ssh/id_rsa.pub`)
5. Click "Add Key"

## Register SSH keys with ssh-agent

```
ssh-add ~/.ssh/id_rsa
ssh-add ~/.ssh/id_rsa_other
```

## Create an SSH config file

SSH can use different login files for different sites. Although github.com is the same domain, you can still assign different host names by creating a file `~/.ssh/config` with the following content:

```
# Default account
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa
   
# Other account
Host github.com-other
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_other
```

## Configure git with user.name and user.email

To list the current git default configs, you can run `git config --list`. To set the default global setting:

```
git config --global user.name "Me" // GitHub username
git config --global user.email "me@me.com"
```

To set a local user for a directory / repo that overrides the default:

```
git config --local user.name "Other" // GitHub username
git config --local user.email "other@other_email.com"
```

## Clone a repository

To clone a directory with the default SSH key pair:

```
git clone git@github.com:me/my_repo.git
```

You can get the proper string from a GitHub repo under the "Code" menu. Look under "Clone" and "SSH".

To clone a directory with the "other" SSH key pair:

```
git clone git@github.com-other:me/my_repo.git
```

Note that you have to modify the string to reflect the hostname that you used in the `~/.ssh/config` file.

## Update existing local repositories

You can show the current repository via

```
git remote -v
```


To update an existing local repository:

```
git remote set-url origin git@github.com-other:other/repo_name.git
```

Obviously use the actual SSH string from GitHub with the appropriate modifications to match your `~/.ssh/config` hostname entries.

## Create a new local repository

Initialize the directory:

```
git init
```

Add the remote:

```
git remote add origin git@github.com-other:other/repo_name.git 
```

Create a file (maybe a `README.md`?) and push it:

```
git branch -M main
git add .
git commit -m "Initial commit"
git push -u origin main
```

## NOTE

If you are on Mac OS X and wondering why git isn't using your local user.name, it's very likely because you are using the https mode of GitHub and the OS X Keychain. Read more about it [here](https://docs.github.com/en/get-started/getting-started-with-git/updating-credentials-from-the-macos-keychain). But the bottom line is to delete the github.com references from your osxchain:

```
git credential-osxkeychain erase
```

Then switch to using ssh following this how-to document.
