
Parent: [[Studies/Bash scripting]]

## Quick references

### Prod style scripts for reference
- [`etl.sh`](https://github.com/nacht29/Studies/blob/main/Programming-Languages/Bash-Scripting/First-Script/etl.sh)

### Exit on error

```sh
set -euo pipefail # exit on error - no custom error message
```

### Redirect to `stdout` and timestamp each line of log:

```sh
log() {
	echo "$(date '+%Y-%m-%d %H:%M:%S')	|	$1" | tee -a pipeline.log
}

# redirect: 2>&1 2: stderror | 1: stdout
python3 extract.py 2>&1 | while read line; do log "$line"; done
```

### Export logs to `stdout` and file
```sh
echo "line" | tee -a file.log # -a = append
```


---

## Pipeline configuration

### Date settings

**Command**
```sh
date '+%Y-%m-%d %H:%M:%S'
date -u '+%Y-%m-%d %H:%M:%S' # UTC date
```

**Using `date` as a variable**
```sh
echo "$(date -u '+%Y-%m-%d %H:%M:%S')
```

**Using `date` in file naming**
```sh
pipeline_$(date -u '+%Y%m%d_%H%M%S').log # pipeline_20260620_100430.log
```

---
### Exit condition settings

Reference: [bash_strict_mode.md](https://gist.github.com/mohanpedala/1e2ff5661761d3abd0385e8223e16425?permalink_comment_id=3799230)

```sh
set -euo pipefail
set -x
```

#### set -e
* The `set -e` option instructs bash to immediately exit if any command [1] has a non-zero exit status. You wouldn't want to set this for your command-line shell, but in a script it's massively helpful. In all widely used general-purpose programming languages, an unhandled runtime error  
- whether that's a thrown exception in Java, or a segmentation fault in C, or a syntax error in Python - immediately halts execution of the program; subsequent lines are not executed.

    - By default, bash does not do this. This default behavior is exactly what you want if you are using bash on the command line 
    - you don't want a typo to log you out! But in a script, you really want the opposite. 
    - If one line in a script fails, but the last line succeeds, the whole script has a successful exit code. That makes it very easy to miss the error.
    - Again, what you want when using bash as your command-line shell and using it in scripts are at odds here. Being intolerant of errors is a lot better in scripts, and that's what set -e gives you.

#### set -x
* Enables a mode of the shell where all executed commands are printed to the terminal. In your case it's clearly used for debugging, which is a typical use case for set -x : printing every command as it is executed may help you to visualize the control flow of the script if it is not functioning as expected.

#### set -u
* Affects variables. When set, a reference to any variable you haven't previously defined - with the exceptions of `$*` and `$@` - is an error, and causes the program to immediately exit. Languages like Python, C, Java and more all behave the same way, for all sorts of good reasons. One is so typos don't create new variables without you realizing it. For example:

    ```sh
    #!/bin/bash
    firstName="Aaron"
    fullName="$firstname Maxwell"
    echo "$fullName"
    ```
* Take a moment and look. Do you see the error? The right-hand side of the third line says "firstname", all lowercase, instead of the camel-cased "firstName". Without the -u option, this will be a silent error. But with the -u option, the script exits on that line with an exit code of 1, printing the message "firstname: unbound variable" to stderr. 
* This is what you want: have it fail explicitly and immediately, rather than create subtle bugs that may be discovered too late.


#### set -o pipefail
* This setting prevents errors in a pipeline from being masked. If any command in a pipeline fails, that return code will be used as the return code of the whole pipeline. By default, the pipeline's return code is that of the last command even if it succeeds. Imagine finding a sorted list of matching lines in a file:

    ```sh
    $ grep some-string /non/existent/file | sort
    grep: /non/existent/file: No such file or directory
    % echo $?
    0
    ```

- Here, grep has an exit code of 2, writes an error message to stderr, and an empty string to stdout. 
- This empty string is then passed through sort, which happily accepts it as valid input, and returns a status code of 0. 
- This is fine for a command line, but bad for a shell script: you almost certainly want the script to exit right then with a nonzero exit code... like this:

    ```sh
    $ set -o pipefail
    $ grep some-string /non/existent/file | sort
    grep: /non/existent/file: No such file or directory
    $ echo $?
    2
    ```

---
