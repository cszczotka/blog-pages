## Databricks notebook tricks

### Get params from notebook ( without widgets)

```python
params = dbutils.notebook.entry_point.getCurrentBindings()
workspace = 'dbw-dev-westeu-001' if params['workspace'] == None else params['workspace']
backup_location = '/Volumes/sandbox/target/backup' if params['backup_location'] == None else params['backup_location']
```

---

### Get access token from current context

```python
    databricksURL = dbutils.notebook.entry_point.getDbutils().notebook().getContext().apiUrl().getOrElse(None)
    myToken = dbutils.notebook.entry_point.getDbutils().notebook().getContext().apiToken().getOrElse(None)
```


---

### Get current context as json

This method doesn't work on shared mode clusters

```python
dbutils.notebook.entry_point.getDbutils().notebook().getContext().toJson()
```

Safe mothod which works on shared mode clusters

```python
dbutils.notebook.entry_point.getDbutils().notebook().getContext().safeToJson()
```

```python
from dbruntime.databricks_repl_context import get_context
dict_result = get_context().__dict__ 
```