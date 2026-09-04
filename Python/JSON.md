
### 1. `json.loads()`
- Loads JSON string data as `dict`
- `loads` s = string

### 2. `json.load()`
- Load JSON from file (into `dict`)
```python
with open('data.json', 'r') as file:
	data_dict = json.load(file)
```

### 3. `json.dumps(value, indent=4)`
- Converts dict data to string


