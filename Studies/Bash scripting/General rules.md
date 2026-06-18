# General rules

Parent: [[Studies/Bash scripting]]

## Important:

### Header

```sh
#!/usr/bin/env bash
```
---

### Give permission to script

```sh
# rwx------
chmod -R 700 folder_1/
chmofd 700 script_1.sh

# rwx-r-x-r-x
chmod -R 755 folder_2/
chmod 755 script_2.sh
```

**Important note:**

#### Case 1:

```sh
chmod 700 folder/
```
- Folder permissions changed.
- File permissions unchanged.

#### Case 2:
```sh
chmod 700 folder/*
```
- Folder permissions unchanged.
- File permissions changed.

#### Case 3:
```sh
chmod 700 -R folder/
```
- Folder permissions changed.
- File permissions changed.

---

### Give folder ownership

```sh
sudo chown -R username:username /path/to/folder

# for current user
sudo chown -R "$USER:$USER" /path/to/folder
```
