Windows Subsystem for Linux (WSL)
=================================

Backup
------

Directly from each distro:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ cd /
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ sudo tar vzcpf /mnt/c/wsl-backup/ubuntu_`date +%Y%m%d_%H%M%S`.tar.gz --exclude=/proc --exclude=/dev --exclude=/mnt --exclude=/media --exclude=/lost+found  --exclude=/tmp --exclude=/sys  --exclude=/run / > /mnt/c/wsl-backup/ubuntu_`date +%Y%m%d_%H%M%S`.log 2> /mnt/c/wsl-backup/ubuntu_`date +%Y%m%d_%H%M%S`.error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Source: based on [this issue
report](https://github.com/DDoSolitary/LxRunOffline/issues/29).

Restore
-------

Using [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline):

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> LxRunOffline install -n ubuntu2 -d c:\wsl\ubuntu2 -f c:\wsl-backup\ubuntu_20181206_10.tar.gz
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Do not forget to update the default user (by default *root*):

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> LxRunOffline su -n ubuntu2 -v 1000
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

And run the already installed distribution:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> LxRunOffline run -n ubuntu2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Restart
-------

Just open cmd.exe as adminstrator and run these commands:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> net stop LxssManager
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> net start LxssManager
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Source:
[superuser](https://superuser.com/questions/1126721/rebooting-ubuntu-on-windows-without-rebooting-windows/1347725)

Reset PAM counter if user is locked
-----------------------------------

Restart WSL as explained before, and then set the default user to root (when the user is locked 'sudo' command doesn't work) running a cmd.exe:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> ubuntu.exe config --default-user root
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run a WSL terminal:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ sudo pam_tally2 -u your_user_name -r
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

And from cmd.exe again, restore the default user, with your username instead of root (as before)

Terminal
--------

I really like [wsl-terminal](https://github.com/goreliu/wsl-terminal) (based on
[wsltty](https://github.com/mintty/wsltty)), but the one I like the most is
[this one](bin/terminal.exe). Internally, it simply calls *bash.exe*, but the
icon and name are really appreciated. To have a “Open a terminal here...” just
modify this registry in *regedit*:

>   *HKEY_CLASSES_ROOT\\Directory\\Background\\shell*

>   Right-click the *shell* key and select *New -\> Key*

>   Name the key: *bash*

>   Select: *bash*

>   Double-click *Default*, and then type: *Open a terminal here...*

>   Next, right-click the *bash* key and select *New \> String value*

>   Name it *Icon* and value data: c:\\bin\\terminal.ico (you can get the same
>   icon from
>   [IconFinder](https://www.iconfinder.com/icons/111238/red_soda_terminal_icon)
>   and rename it)

>   Next, right-click the *bash* key and select *New \> Key*

>   Name it: *command*

>   With the *command* key selected in the left pane, double-click *Default*,
>   and then type: *c:\\bin\\terminal.exe*

OpenMPI installation and running
--------------------------------

First, install the openMPI package and *mpirun*:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ sudo apt install openmpi-bin openmpi-common openmpi-doc libopenmpi-dev
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Optionally, you can also install *cmoka* unit testing:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ sudo apt install libcmocka-dev
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For compiling and running (3 copies from *mpirun*), find below an example (*mpicc* instead of *gcc*)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ mpicc example.c -o example
$ mpirun -n 3 example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This would return an error related to the Linux Kernel CMA support, which is not available due to restrictive ptrace settings, so you can either add the following parameter to *mpirun* or permanently set ptrace_scope to 0 (by default 1)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ mpirun --mca btl_vader_single_copy_mechanism none -n 3 example

OR

$ echo 0 > /proc/sys/kernel/yama/ptrace_scope
OR
$ sudo echo 0 > /proc/sys/kernel/yama/ptrace_scope
OR
$ echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### Links

Links I’ve found very useful:

-   [Awesome WSL](https://github.com/sirredbeard/Awesome-WSL)

-   [Getting Crazy with Windows Subsystem for
    Linux](https://brianketelsen.com/getting-crazy-with-windows-subsystem-for-linux/)

-   TBC...
