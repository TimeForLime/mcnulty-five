#Master Document

**Connecting from Jupyter to RDS instance**
1. Create a settings file with the following code:

2. Run this code in your jupyter
```python
import pandas as pd

from sqlalchemy import create_engine
from sqlalchemy.engine.url import URL
import settings

def db_connect():
    """
    Performs database connection using database settings from settings.py.
    Returns sqlalchemy engine instance
    """
    return create_engine(URL(**settings.DATABASE))

db = db_connect()
pd.read_sql("""select * from pokemon""", db)
```


**How to connect to RDS from EC2 instance**
```python
psql -h metis.cabju7mub8cg.us-west-2.rds.amazonaws.com -p 5432 -U mimic -d MIMIC_ICU
```
