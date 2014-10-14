AndroidSeedBox
==============

This script will install everything necessary to run Transmission and Flexget from your Android device. However, it is not limited to using just these two programs. The script will install Python, openssh, samba, bash, etc.
For the forum discussion see [this discussion](http://minixforums.com/threads/how-to-linux-optware-ssh-samba-transmission-flexget.2825/#post-23301)

The script is build on the original [NSLU2-optware for Android bootstrap
script](http://sourceforge.net/p/optware-android/wiki/Home/)

This should work for any rooted device which has an adb connection enabled. This
will work on any android device that has an ARM processor as long as it is rooted with s-off.
Sorry but all the binaries are built on ARM.

**JUST AS EVERY OTHER DEVELOPER: I AM NOT RESPONSIBLE IF YOU BRICK YOUR DEVICE! MAKE A BACKUP!**

##Requirements:
* An arm based android device
* Linux box with adb (don't ask me about windows, I don't support bad habits although maybe cygwin would work)
* clockworkmod (for a backup)
* root
* s-off
* internet connection (on both your computer and Android Device)

##Process:
1. Make a backup of your ROM!
2. Download files (gitHub)
  You have two options here:
  1. Download the zip [here](https://bitbucket.org/erichlf/androidseedbox/archive/master.zip) and unzip it.
  2. Clone the repo using git via `git clone git@bitbucket.org:erichlf/androidseedbox.git`
3. Make script executable
    `chmod +x optware-etc.sh`
4. Obtain adb connection to device
5. Gain root access on local machine (adb seemed to require this for things to work)
    `sudo su`
6. Run script and follow directions
    `./optware-etc.sh`
7. Pre 0.97 there was a bug with setting the user password. This should be fixed. However if it is not refer to the following.

Currently there is a bug with setting the user password, but it is easily fixed. If the script hangs on asking for the password just press ctrl+c. After the script is done running enter the following in command line
    ```adb shell
    su
    /data/opt/bin/busybox passwd```
Enter your desired password and then
    `exit`
    `exit`
Now you should be able to use ssh to get into your device.
Use SManager to run `/opt/home/root/sysinit` at every restart (or run it using init.d).

###Notes:
* The script can be modified to change the various programs that I install. You could exchange transmission for rtorrent for example.
* Transmission can be accessed from the minix through `localhost:9091` or from some other machine using your ip-address and the port 9091. If that doesn't work you should edit the config file located at `/opt/home/root/.config/transmission-daemon/settings.json`
    ```username: root
    password: you provided this during install```
* Without SManager nothing will start automatically. However, if you have a ROM which has init.d support you can move the scripts in `/opt/etc/init.d` to `/etc/init.d` I would suggest maybe linking the two instead of just moving the scripts or possibly adding a script to `/etc/init.d` which runs the items in `/opt/etc/init.d` The reason is because when installing things using ipkg the startup scripts will be placed in `/opt/etc/init.d` and not `/etc/init.d` However, it is extremely important that optware is started, and this is partly what sysinit accomplishes.
* If you have init.d support you may have an issue with transmission-daemon executing before your USB is mounted. I solved this by creating a script which loops until the USB is mounted then executes transmission-daemon. This required me to call the script from within the startup script in init.d. Also, I created a symbolic link from `/etc/init.d` pointing to `/data/opt/etc/init.d` doing this adds any new optware daemons automatically to init.d.
* To list available packages
    `ipkg list`
* To install a new package use the command
    `ipkg install <new package>`
* To remove a package use the command
    `ipkg remove <package to remove>`
* cron is weird and I couldn't get it to work like it should, but I got it to work
    While on the Android device (ssh or terminal emulator)
    1. Create a .crond file with some schedule in it. Remember to leave a blank line at the end of the file.
    2. Tell cron about the .crond file
        `crontab -u root /opt/home/root/.crond`
    3. Make sure cron sees the cron file
        `crontab -l`
    4. If you want to edit your cronfile use a text editor and edit the file directly and then tell cron about the file again.
* The samba script located at /opt/etc/init.d/S08samba needs to be edited to activate samba on startup. Just change line 4 from `samba_active = 0` to `samba_active = 1`
* Many things are installed in what seem like strange places, so use
    `which <binary you are looking for>`
* Feel free to help develop the code. I think what would be best is an update.zip or a CWM flashable zip. Right now I don't know how to do this, but once I get more time I will look into it. So, any help on this front is welcomed.

###Change Log:
* v0.1 - Initial version adapted from the standard NSLU2-Optware for Android bootstrap script.
    * Added ssh
    * Added transmission
    * Added flexget
    * Added samba
    * Added bash
    * Added vim
* v0.5 - Removed all sorts of bugs that ended up preventing many tasks in the bootstrap script from completing.
* v0.6 - Fixed ssh
    * Added bash to /system/bin, so that profile would execute on login.
    * Added a much better busybox than the one included in NSLU2-optware. This busybox has many commands needed for ssh including passwd.
* v0.9 - Added /data/opt/local to PATH since this is where flexget resides.
* v0.95 - Switched to bash instead of shell script.
* v0.97 - Added a workaround to the password bug.
Enjoy!
