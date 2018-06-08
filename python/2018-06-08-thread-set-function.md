### Set compare

```python
#!/usr/bin/env python
a = [1, 2, 3]
b = [2, 1, 5, 6]
aset = set(a)
bset = set(b)
print aset ^ bset
# -------------------
# set([3, 5, 6])
```

### Decompose things into functions

I have make a refraction of a function which been written one year before. There is a while(1) loop which do two things: 

1. collect some data every 10s.
2. collect some information about status.

Those two things are done in the traveling of a list of object which may vary from 0 to 600.

Original edition i wrapped those code in the while loop which contains more than dozen lines.
It's hard for me to understand the meaning of those code.

I even trave the list for two times, first to collect some data every 10s, second time to collect information about status.



- Write code at a  top to bottom view, decompose things into functions.
- Abstract common module into little function for reuse.


### Thread creation

```pyhthon
import threading

threading.Thread(target=functionToRun, name="newThreadToRun").start()

```

### Object and attribution 

```python
#!/usr/bin/python 
obj = {"name":"hello", "age": 17}
print obj['name'] + "'s age is: " + str(obj['age'])
# ------------------
# hello's age is 17
```
