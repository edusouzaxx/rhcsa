
# RH124 - Comprehensive Review

# RH134 - Comprehensive Review

# RH199 - Comprehensive Review

## Lab: Fixing Boot Issues and Maintaining Servers

### EXERCÍCIO 01 - Troubleshoot the possible cause and repair the boot failure

```
Press e 
systemd.unit=emergency.target ou rd.break
Press Ctrl+x 

mount -o remount,rw /
mount -a
vi /etc/fstab
mount -a
systemctl reboot
```

### EXERCÍCIO 02 - On serverb, switch to the multi-user target. Set the default target to multi-user.

```
[student@serverb ~]$ systemctl get-default
[student@serverb ~]$ sudo systemctl isolate multi-user.target
[student@serverb ~]$ sudo systemctl set-default multi-user.target
[student@serverb ~]$ sudo systemctl reboot
```

### EXERCÍCIO 03 - Schedule a recurring job as the student user that executes the /home/student/backup-home.sh script on an hourly basis between 7 p.m. and 9 p.m. on all days except Saturday and Sunday. 

```
wget http://materials.example.com/labs/backup-home.sh
chmod +x backup-home.sh
crontab -e
0 19-21 * * 1-5 /home/student/backup-home.sh 
0 19-21 * * Mon-Fri /home/student/backup-home.sh
```

PONTO DE ATENÇAO! 

- PRESTA ATENÇÃO NA HORA p.m. ou a.m.

## Lab: Configuring and Managing File Systems and Storage

### EXERCÍCIO 01 - Configure a new 1 GiB logical volume called vol_home in a new 2 GiB volume group called extra_storage. Use the unpartitioned /dev/vdb disk to create partitions. 

```
parted /dev/vdb mklabel msdos
parted /dev/vdb mkpart primary 1GiB 3Gib
parted /dev/vdb set 1 lvm on

vgcreate extra_storage /dev/vdb1
lvcreate -n vol_home -L 1GiB extra_storage
```

### EXERCÍCIO 02 - The logical volume vol_home should be formatted with the XFS file-system type, and mounted persistently on /home-directories. 

```
mkfs.xfs /dev/extra_storage/vol_home
mkdir /home-directories
blkid
echo "UUID=blablabla /home-directories xfs defaults 0 0" >> /etc/fstab
mount -a
```

### EXERCÍCIO 03 - Ensure that the network file system  servera.lab.example.com:/share is persistently mounted on /local-share across reboot. 

```
mkdir /local-share
mount -t nfs servera.lab.example.com:/share /local-share
mount | grep nfs

umount /local-share
echo "servera.lab.example.com:/share /local-share nfs rw,sync 0 0" >> /etc/fstab
mount -a
df -h
```

### EXERCÍCIO 04 - Create a new 512 MiB partition on the /dev/vdc disk to be used as swap space. This swap space must be automatically activated at boot. 

```
parted /dev/vdc mklabel msdos 
parted /dev/vdc mkpart primary linux-swap 1MiB 513MiB
parted /dev/vdc 

mkswap /dev/vdc1
blkid
echo "UUID=blablabla swap swap defaults 0 0" >> /etc/fstab
free
swapon -a
free
```

PONTO DE ATENÇÃO:

- Toda partição msdos (MBR) precisa dizer se é primary ou extended

### EXERCÍCIO 05 - Create a new group called production. Create the production1, production2, production3, and production4 users. Ensure that they use the new group called production as their supplementary group. 

```
groupadd production
for i in 1 2 3 4; do useradd -G production production$i; done
getent passwd production1
getent passwd production
```

### EXERCÍCIO 06 - Configure your system so that it uses a new directory called /run/volatile to store temporary files. Files in this directory should be subject to time based cleanup if they are not accessed for more than 30 seconds. The octal permissions for the directory must be 0700. Make sure that you use the /etc/tmpfiles.d/volatile.conf file to configure the time based cleanup for the files in /run/volatile. 

```
echo "d /run/volatile 0700 root root 30s" >> /etc/tmpfiles.d/volatile.conf
systemd-tmpfiles --create /etc/tmpfiles.d/volatile.conf
```

PONTO DE ATENÇÃO:

- man -k tmpfiles 
- /etc/tmpfiles.d/varquivo.conf
- systemd-tmpf  --create 


## Lab: Configuring and Managing Server Security

### EXERCÍCIO 01 - Generate SSH keys for the student user on serverb. Do not protect the private key with a passphrase. On servera, configure the student user to accept login authentication using the SSH key pair created for student on serverb. The student user on serverb should be able to log in to servera using SSH without entering a password. 

```
ssh-keygen
ssh-copy-id servera
ssh serverb

ssh -o pubkeyauthentication=yes -o passwordauthentication=no production5@serverb
```

### EXERCÍCIO 02 -  On servera, change the default SELinux mode to permissive. 

```
vim /etc/sysconfig/selinux
SELINUX=permissive
systemctl reboot
```

PONTO DE ATENÇÃO:

- REBOOT

### EXERCÍCIO 03 - Configure serverb to automatically mount the home directory of the production5 user when the user logs in, using the network file system /home-directories/production5. This network file system is exported from servera.lab.example.com. Adjust the appropriate SELinux Boolean so that production5 can use the NFS-mounted home directory on serverb after authenticating via SSH key-based authentication.

```
mkdir -p /localhome/production5
mount -t nfs servera.lab.example.com:/home-directories/production5 /localhome/production5
umount /localhome/production5

yum install autofs -y
echo "/- /etc/auto.production5" >> /etc/auto.master.d/production5.autofs
echo "/localhome/production5 -rw,sync servera.lab.example.com:/home-directories/production5" > /etc/auto.production5
systemctl enable --now autofs
su - production5
df -h

ssh-keygen
ssh-copy-id serverb
ssh serverb
PEDE SENHA

no destino
getsebol -a | grep home
getsebol use_nfs_home_dirs off
setsebol -P use_nfs_home_dirs on

ssh serverb OU ssh -o pubkeyauthentication=yes -o passwordauthentication=no serverb
OK!
```

### EXERCÍCIO 04 - On serverb, adjust the firewall settings so that the SSH connections originating from servera are rejected. 

```
firewall-cmd --add-source 172.25.250.10/32--zone=block --permanent
```

PONTO DE ATENÇÃO:

- LEMBRA DO /32 

### EXERCÍCIO 05 - On serverb, investigate and fix the issue with the Apache HTTPD daemon, which is configured to listen on port 30080/TCP, but which fails to start. Adjust the firewall settings appropriately so that the port 30080/TCP is open for incoming connections.  

```
systemctl restart httpd
systemctl status -l httpd
journalctl -u httpd --no-pager
sealert -a /var/log/audit/audit.log

semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 30080
systemctl restart httpd

firewall-cmd --add-port=30080/tcp --permanent
firewall-cmd --reload
```

PONTO DE ATENÇÃO:

- ESQUECE DO RELOAD NÃO ABESTADO
