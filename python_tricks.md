## Python Tips and Tricks

### Write things in files to track your program activities


You might want to do it with your own hand, opening a filedescriptor and 
pass it to every functions that will on it. 

This is annoying becaus you need to change every function definition (if your are not using 
kwargs).

The easiest way to do this is to use the __logging__ package :

```python
import logging
```



