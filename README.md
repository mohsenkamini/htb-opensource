# htb-opensource

# User Access
### LFI

`os.path.join` function has an input of path, if this input equals to an absolute path, the command will throw other inputs and only reads the absolute path.

so getting the following URL gives us `/etc/passwd`

~~~
/uploads/..//etc/passwd
~~~
