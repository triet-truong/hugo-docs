# Git multiple SSH keys

Edit `~/.ssh/config` file, add the `# Personal account` block:

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

In the *personal* repository, configure `origin` using the specified host alias `github.com-triet_truong`:

```bash
git remote add origin github.com-triet_truong:triet-truong/hugo-docs
```

- If origin is already configured, then update it:

    ```bash
    git remote set-url origin github.com-triet_truong:triet-truong/hugo-docs
    ```

Verify by using `git push`

References:

- <https://ma.ttias.be/specify-a-specific-ssh-private-key-for-git-pull-git-clone/>

- <https://www.devdungeon.com/content/how-specify-ssh-key-git-repository>