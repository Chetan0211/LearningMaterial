# How to have multiple Git accounts?

We can actually do that using ssh keys. First create ssh keys for the accounts you need.

for account 1:
```shell
ssh-keygen -t ed25519 -C "account1@gmail.com"
```
Save this key with name id_ed25519_account1
for account 2:
```shell
ssh-keygen -t ed25519 -C "account2@@gmail.com"
```
Save this key with name id_ed25519_account2

Now you need to get .pub for both of the ssh and save it in your respected github account.

Then you need to update `~/.ssh/config` file.

```file
Host github.com-{{ACCOUNT1}}
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_account1
    IdentitiesOnly yes

# Account 2 Configuration (e.g., your work account)
Host github.com-{{ACCOUNT2}}
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_account2
    IdentitiesOnly yes

# Optional: Default github.com entry if you still want one
# Host github.com
#     HostName github.com
#     User git
#     IdentityFile ~/.ssh/id_ed25519  # Or your primary key
#     IdentitiesOnly yes
```

### Cloning the repository
Now when you need to clone a repository you need to clone it using ssh link that is provided by the github but with a slight change.

when we copy the ssh we will be having like `git@github.com:OWNER/REPO.git`. Here we need to change `gitgit@github.com` to `git@github.com-account1:OWNER/REPO.git`



```shell
git clone git@github.com-account1:OWNER/REPO.git
```

You can go into the repo and you can specify the specific name and email for it.

```shell
git config --local user.name "Your Name For This Account"
git config --local user.email "your.email.for.this.account@example.com"
```


### Setting the repo with which has already present

When you have a repo present in your local and now you need correct credentials. Then you need to go into that repo and enter the command:
```shell
# Example if the repo is owned by 'user1' on GitHub
git remote set-url origin git@github.com-account1:user1/repository-name.git
```