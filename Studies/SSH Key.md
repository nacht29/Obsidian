
## Generate SSH key

```sh
ssh-keygen -t rsa -b 4096 -C "label"
ssh-keygen -t rsa -b 4096 -C "label" -f ~/.ssh/key_file_name
```

Example:

```sh
ssh-keygen -t rsa -b 4096 -C "Type: GitLab | Account: yanzhe@dataflows.co | URL: gitlab.dataflows.com | Device: Macbook Air M5 | File path: ~/.ssh/dataflows-gitlab-ssh-1" -f ~/.ssh/dataflows-gitlab-ssh-1
```
