## Python Tips and Tricks

### I. Write things in files to track your program activities


You might want to do it with your own hand, opening a filedescriptor and 
pass it to every functions that will on it. 

This is annoying becaus you need to change every function definition (if your are not using 
kwargs).

The easiest way to do this is to use the __logging__ package :

```python
import logging
```
To log in a file, define a basic logger : 

```python
logging.basicConfig(filename='/path/logfile.log', encoding='utf-8', level=logging.DEBUG)
```
__level__ difine the minimum level of message to log, DEBUG level will log DEBUG, INFO, WARN, ...
```python
logging.debug(f"Message {variable}")
logging.info(f"Message {variable}") 
```

This in working fine, but sometime, you log will be on the terminal output and not on the file. 
This is because some package can use logging and "overwrite" your parameters. 

To prevent you from that, you need to pass the __force__ argument. 
```python
logging.basicConfig(filename='/path/logfile.log', force=True, level=logging.DEBUG)
```
I did remove the __encoding__ argument on purpose, for some reason that i can't explain, it is not possible
to specifie it when using __force__. 

You may want to have like time informations, or a special way of formating each log line. 
This can be done with : 

```python 
logging.basicConfig(filename=...,
                    force=True,
                    level=...,
                    format='%(level)s : %(name)s : %(message)s')
logging.info(f"your message")
```
This will display in file like beside, where root indicate that the root file of your programm: 

```
INFO : root : your message
```

The classical way of log in file with logging package is more like that : 

```python 
logger = logging.getLogger(__name__)
logger.info(...)
```

The ouput will be : 

```
INFO:name_of_file_in_where_we_log: ...
```
### II. Dealing with virtual environement 

A really classical one i have to say, put as a pip user, so, not a conda user, i spent a lot of time 
without using it. Beside all the good and necessary advantages that using virtual environement for all your projects
provide, it also come this major drawbacks : 
  - You need to be a litle bit methodical (which i'm not) 
  - It's taking a looooot of space on your computeur, as every project will have their own site-package 
  with everythings installed in there. 

Moreover, we are likeley to use always the same, eavy library like numpy, Pytorch ... 
There are a lot of different way of dealing with python virtual environemment, virtualenv, conda, poetry ... 

The compromise i have found i to use __venv__, kind of the builtin way of doing it. 


This will create the virtual environnement : 
```
python3 -m venv path/to/MyProject
```
Then you need to "connect" to this virtual environnement : 

```
source path/to/MyProject/bin/activate 
```
Then is you type __pip3 list__, it will be almost empty. 

Then i don't want all my project to have their specific torch version installed (to save space), 
you can provide the virtual environenement the path to your "global" site-package :
```
python3 -m venv path/to/MyProject --system-site-packages [PATH2SITEPACKAGE]"
```
And edit the config file named pyvenv.cfg that should look like :
```
home = /usr/bin
include-system-site-packages = true
version = 3.XX.y
```
Now you can safly use install you desired package, specific to your project, but if 
you already have this package in your system python, with the same versions, i will use
the one from your system. Then, if you need to have a lower versions of a existing package 
in your system library, it will be install in the virtual environnement ignoring the one 
from your system. 

### III. Browsing in file (ML) 

To get all the files paths in a directory, i commonly use glob, however, glob did't certify 
that the ordering of the files will always be se same (depending on system they says). 

This is annoying when files names have a importance in your process, especially when you have a 
directrory with multiple data file in it and a second directory with labels files in it, and the 
mapping between the two is made by name.

```python
import glob
import os
path = "your/directory"
files = sorted(glob.glob(os.path.join(path, "regex*")), key=os.path.basename)
```
This wil sort your file by name, so the order returned by glob in not a problem anymore. 


### IV. Arguments Parser 

```python

parser = argparse.ArgumentParser(prog="Train ML model",
                                     description="Train my model")
parser.add_argument("path_to_training_data",
                    type=str,
                    help="path to the training data folder, by default, \
                        the data is expected to be organize like this : ... ")

parser.add_argument("--batch_size", 
                    type=int,
                    default=3,
                    help="batch_size for both training and validation dataloader")
args = parser.parse_args()
```
First argument __path_to_training_data__ need to be provided, the second __batchsize__ is optional. 
Argument with __--__ before will be optinal. 

You will call your python srcipt : 
```
python3 myscript.py path/2/folder --batch_size 16
```

Access those arguments with : 
```python 
args.path_to_training_data
args.batch_size 
```
To easy no ? 
The only thing you need to know is that it is not working like that for boolean values (you can't set __type=bool__) 

To use boolean : 
```
parser.add_argument('--batch_norm', action="store_true",
                    help="Rather to use batch normalization or not") 
```

And call with __--batch_norm__ to set it to True, otherwhile, it will be False : 
```
python3 myscript.py path/2/folder --batch_size 16 --batch_norm
```

### V. Dictionaries 

Use comprehension : 

```python
d = {k:v for k,v in zip(list_1,list2)}
```
Updating dictionnaries (in place method) 

```python 
d1 = {'Name':'Jhon', 'LastName':'Jones', 'Age':'Unknow'}
d2 = {'LastName':'Jones', 'Age':25}

d1.update(d2) 
print(d1) 
```
```
{'Name':'Jhon', LastName:'Jones', 'Age':'25'}
```
Merging dicrionnaries (not in place method).

Do the same thing as update but without modifying d1. 
```python
d3 = {**d1, **d2}
print(d3)
```
```
{'Name':'Jhon', LastName:'Jones', 'Age':'25'}
```
Note that you can pass more than 2 dictionnaries, and the order 
(from left to right) define which values will be taken when keys are the same.

### VI. TensorBoard 
