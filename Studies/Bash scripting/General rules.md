
Parent: [[Studies/Bash scripting]]

## Important:

### Header (shebang)

```sh
#!/usr/bin/env bash
```

This is important when running the script as `./script.sh`, as it tells the executable programme to find Bash in the user's environment. This is a more robust approach compared to `#!/bin/bash`.

When executing the script as `bash script.sh`, then the shebang doesn't really matter.

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

---

### Script execution

Assume we have a script `hello.sh`. There are 2 ways to execute this script:

```
./hello.sh
# or
bash hello.sh
```


**Difference**:

| Feature            | `./hello.sh`                                                                                           | `bash hello.sh`                                                                                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Execution method   | Direct program execution using the interpreter defined in its internal hashbang (e.g., `#!/bin/bash`). | Interpreted argument execution: explicitly passes the file as an argument to the Bash interpreter, ignoring the hashbang and bypassing execution permission requirements. |
| Permissions Needed | Requires **read** and **execute** permissions. (r-x)                                                   | Requires **read** permission only.                                                                                                                                        |
| Interpreter Source | Read from the file's first line (the "hashbang" like `#!/bin/bash`).                                   | Forced to use the `bash` program.                                                                                                                                         |

### Read/Export results

**Export logs to `stdout` and `.log` file:**
```sh
echo "line" | tee -a file.log
```

---


