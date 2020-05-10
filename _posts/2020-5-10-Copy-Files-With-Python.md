---
title: "Can we copy files with python?"
date: 2020-5-10
categories:
  - blog
tags:
  - answer me everything
  - copy files with python
---

Although I said the series will not be about coding, it is an important part of my life! Thus, the first question had to be about it.  
Here goes the first question:

## Can we copy/move files using python?

Most of us know that we can open, close, and even write to files using python. I was searching for a way to copy or move large number of files directly from python instead of opening the bash shell/cmd (I use both windows and ubuntu on my PC). After a bit of researching, I found this beautiful library, "[shutils](https://docs.python.org/3/library/shutil.html)" and also "[os](https://docs.python.org/3/library/os.html)".

One can simply use the following code to copy a file:  
```python
import shutil

# source = full source of the file
# destination = full destination of the file
dest = shutil.copy(source, destination)
```  
The operation returns the path to the newly created file that is stored in the "dest" variable

For moving files:
```python
import os
import shutil

# source and destination are the full source and destination
os.rename(source, destination)
# Similar to shutil.copy, the shutil.move returns the path to the newly created file
dest = shutil.move(source, destination)
```  
The os function doesn't return any value. I personally find the shutil more usable as I get a string reference to the destination file, which I may or may not have stored in a variable before.

Hope some of you also had this doubt in your mind and got it cleared.
