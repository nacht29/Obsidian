# Concepts

Parent: [[Studies/Bash scripting]]

## Useful lines

### Exit on error

```sh
set -euo pipefail # exit on error - no custom error message
```

### Redirect to stdout and timestamp each line of log:

```sh
log() {
	echo "$(date '+%Y-%m-%d %H:%M:%S')	|	$1" | tee -a pipeline.log
}

# redirect: 2>&1 2: stderror | 1: stdout
python3 extract.py 2>&1 | while read line; do log "$line"; done
```
