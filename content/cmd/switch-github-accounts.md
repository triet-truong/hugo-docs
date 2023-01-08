---
weight: 3
---

# Switch github accounts

## Prerequesites

Assume we have two SSH private keys:

- Work account `~/.ssh/id_rsa`
- Personal account: `~/.ssh/id_rsa_triet_truong`

## Create config file

  ```bash
  cat ~/.ssh/config
  ```

<details open>
    <summary>Content</summary>  

  ```bash
  # Work account, - the default config
  Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

  # Personal account
  Host github.com-triet_truong    
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_triet_truong
  ```
</details>

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

## Switch key

  In case `git push` failed due to incorrect account, then switch key:

  ```bash
  # Remove all keys
  ssh-add -D
  ```

  ```bash
  # Add personal account key
  ssh-add ~/.ssh/id_rsa_triet_truong
  ```

  ```bash
  # List current keys
  ssh-add -l
  ```

`git push` again, this time will work.

## More details

<https://www.freecodecamp.org/news/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca/>
