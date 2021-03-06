# Minimal run

This is a tiny C program for running Windows commands with a hidden
console. It's suitable for use in Windows shortcuts, i.e. lnk files.

I use `run.exe wsl --exec xterm -display :0` to launch an XTerm on Windows.

If you don't want to compile it yourself, check the [Releases](https://github.com/taviso/minirun/releases) section.

# Building

```
> rc.exe /nologo resource.rc
> cl.exe /nologo  /Zi /O2 /guard:cf /GF /c /Fo:run.obj run.c
> cl.exe /nologo  /Zi /O2 /guard:cf /GF /MT /Fe:run.exe run.obj resource.res /link  user32.lib
```

> Note: You need to build it as a 64bit application to access wsl.exe.

# Screenshot

You can't see it running because it's designed to be invisible! Your
shortcut should look something like this.

![Screenshot](xterm-example.png)

# Troubleshooting

First try this to make sure run is working:

```
run.exe calc
```

If that does work, but starting a wsl application doesn't work, it may be a
PATH issue.

Try using the full path instead, like this:

```
run.exe wsl --exec /usr/bin/program
```

If you need to set `$DISPLAY` or some other variable, use `env`. For example,
like this:

```
run.exe wsl --exec env DISPLAY=:0 /usr/bin/program
```

Are you trying to run a script? It might be a permissions issue, try this:

```
run.exe wsl --exec /bin/sh /full/path/to/script.sh
```

> If it's not a shell script, use the correct interpreter.. e.g. `/usr/bin/perl`.

Are you trying to use `$DISPLAY` in wsl2? You might need a script to get the right address, save this as display.sh

```sh
#!/bin/sh
DISPLAY=$(awk '/nameserver/ {print $2; exit}' /etc/resolv.conf):0 exec "${@}"
```

Now you can do `run.exe wsl --exec /bin/sh /path/to/display.sh /usr/bin/xterm`

