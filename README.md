# make_chroot_jail
Script to easily setup a chroot jail for ssh / scp / sftp with Linux

The original commit contains unmodified copy of the script from  
http://www.fuschlberger.net/programs/ssh-scp-sftp-chroot-jail/  
You'll find nice explanation and usage instructions there.  
More specific examples are on the net, search for "make_chroot_jail".

Second commit has changes needed for Ubuntu 12.04, and was originally published at
http://www.devcu.com/forums/topic/560-chrootjail-users-for-sshscp-ubuntu-1204/

Finally, current version made the script non-interactive, better suitable for automated operation:
 * create user without a password
 * if the user exists and jailed then do nothing
 * if the user exists then jail her
 * do not create shell if already exists

Additional changes:
 * tested on Ubuntu 8.04 to 16.04
 * added APPS: cat more less nano
 * copied /lib/terminfo
 
## Usage

git clone https://github.com/jcmartinez/make_chroot_jail.git
cd make_chroot_jail

### Jail a new user
sudo bash make_chroot_jail.sh user_name

## Add additional packages to the jailed user

Download l2chroot script:

wget http://www.cyberciti.biz/files/lighttpd/l2chroot.txt

Edit the BASE variable to match the “Default chroot-path = /home/jail" that comes defined on the previous script.

nano l2chroot.txt

Change from 

BASE="/webroot"

to 

BASE="/home/jail"

then make it available globally: 

mv l2chroot.txt l2chroot
sudo cp l2chroot /bin
sudo chmod +x /bin/l2chroot

### Add new packages to the chroot (also for updates)

E.g. Adding Git

Some of the following was copied from: https://gist.github.com/borisaka/6076a0f85e310935728c20b9ee86e63b

sudo l2chroot /usr/bin/git
sudo l2chroot /usr/bin/git-shell
sudo l2chroot /usr/bin/git-upload-pack

sudo l2chroot /bin/ls 
sudo l2chroot /bin/bash 
sudo l2chroot /usr/bin/git*
sudo l2chroot /usr/bin/curl

CHROOT=/home/jail
cp -r --parents /usr/share/git-core $CHROOT
cp -r --parents /usr/lib/x86_64-linux-gnu/libcurl*.so* $CHROOT
cp --parents /etc/nsswitch.conf $CHROOT
cp --parents /etc/resolv.conf $CHROOT

#### @Todo for Git

After this Git produces an error: Problem with the SSL CA cert (path? access rights?)
A non-ideal workaround is:

git config --global http.sslVerify false

We need to find a way of solving this.

