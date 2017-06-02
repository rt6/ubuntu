# SETUP SFTP SERVER WITH RESTRICTED ACCESS

### Create sftp user (no ssh access) and restrict write access to user home directory only

1) Create new user and SSH keys
```sh
sudo su
addgroup --system sftponly
adduser --disabled-password sftpuser
usermod -G sftponly sftpuser
groups sftpuser
mkdir -p /home/sftpuser/.ssh
ssh-keygen -t rsa -b 4096 -C "sftpuser" -f /home/sftpuser/.ssh/newkey
touch /home/sftpuser/.ssh/authorized_keys
cat /home/sftpuser/.ssh/newkey.pub >> /home/sftpuser/.ssh/authorized_keys
chown -R sftpuser:sftpuser /home/sftpuser/.ssh
chmod 700 /home/sftpuser/.ssh
chmod 600 /home/sftpuser/.ssh/authorized_keys
```


2) Prepare home directory for restricted access
```sh
# the home directory must be owned by root
chown root:root /home/sftpuser
# the home directory must not be writeable by group and others
chmod go-W /home/sftpuser
mkdir /home/sftpuser/uploads
chown sftpuser:sftpuser /home/sftpuser/uploads
#optional, allow group members to write to uploads directory
chmod ug+rwX /home/sftpuser/uploads
```


3) Turn off SSH Access, and restrict sftp access to Home directory **/home/sftpuser**:

Edit `/etc/ssh/sshd_config` and comment out this line, like this:
```
# Subsystem sftp /usr/lib/openssh/sftp-server
```


Edit `/etc/ssh/sshd_config` and add to the **bottom** of the file:
```sh
Subsystem sftp internal-sftp
Match group sftponly
    ChrootDirectory /home/%u
    X11Forwarding no
    AllowTcpForwarding no
    ForceCommand internal-sftp
```


4) Restart SSH and if it working.  Check the process id is displayed, otherwise there was some errors
```sh
service ssh restart
```


5) Connect via sftp command line client using private key
```sh
sftp -o IdentityFile=newkey sftpuser@IP.NUMBER

```



6) Attempts to connect via ssh will be denied
```sh
$ ssh sftpuser@IP.NUMBER -i newkey
Could not chdir to home directory /home/sftpuser: No such file or directory
This service allows sftp connections only.
Connection to IP.NUMBER closed.
```
