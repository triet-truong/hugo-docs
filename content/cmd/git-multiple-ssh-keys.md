# Git multiple SSH keys

## Prerequesites

Assume we have two SSH private keys:

- Work account `~/.ssh/id_rsa`
- Personal account: `~/.ssh/id_rsa_triet_truong`

## Create config file

```bash
cat ~/.ssh/config
```

Content:

```text
# Work account, - the default config
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa
   IdentitiesOnly yes
   
# Personal account
Host github.com-triet_truong    
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_triet_truong
   IdentitiesOnly yes
```

## Navigate to a personal repository

```bash
cd ~/Documents/projects/my-personal-repo
```

## Configure local credential

  ```bash
  git config --local user.name "my-name"    
  ``` 

  ```bash
  git config --local user.email "my-personal-account-email"
  ```

## Update `origin` URL

```bash
git remote set-url origin github.com-triet_truong:triet-truong/hugo-docs
```

Verify by using `git push`

References:

- <https://ma.ttias.be/specify-a-specific-ssh-private-key-for-git-pull-git-clone/>

- <https://www.devdungeon.com/content/how-specify-ssh-key-git-repository>