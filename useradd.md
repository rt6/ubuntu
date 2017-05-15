# CREATE PASSWORDLESS SSH USER

```sh
sudo su
adduser --disabled-password mikesmith
usermod -aG sudo mikesmith
mkdir -p /home/mikesmith/.ssh
touch /home/mikesmith/.ssh/authorized_keys
chmod 700 /home/mikesmith/.ssh
chmod 600 /home/mikesmith/.ssh/authorized_keys
# copy public key to the authorized_keys file
```

#### Remove password promopts for sudo commands
If this is sudo user, run  `visudo` and add the below to the end of the file
```sh
mikesmith ALL=(ALL) NOPASSWD:ALL
````
