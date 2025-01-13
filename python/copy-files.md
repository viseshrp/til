# Efficiently copying a file

```python
import shutil
shutil.copyfileobj(open('file1', 'rb'), open('file2', 'wb'))
```
https://docs.python.org/3/library/shutil.html#shutil.copyfileobj
