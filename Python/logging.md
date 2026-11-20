
### Logging Class 
```python
class SGTFormatter(logging.Formatter):
	def formatTime(self, record, datefmt=None):
		dt = datetime.fromtimestamp(record.created, tz=ZoneInfo("Asia/Singapore"))
		return dt.strftime(datefmt or "%Y-%m-%d %H:%M:%S")

logger = logging.getLogger("bq_to_drive")
logger.setLevel(logging.INFO)
_handler = logging.StreamHandler()
_handler.setFormatter(SGTFormatter('%(asctime)s\t|\t"%(message)s"'))
logger.addHandler(_handler)
```

### Run `.py` scripts with log file and terminal output

#### Truncate log file
```sh
python main.py 2>&1 | tee -a log
```

#### Append log file

```sh
python main.py 2>&1 | tee -a log
```


