
# RH124 - Comprehensive Review

PONTOS DE ATENÇÃO:

- PRESTAR BASTANTE ATENÇAO NOS NOMES E EXTENSÃO DE ARQUIVOS


## Lab: Managing Files from the Command Line

lab rhcsa-rh124-review1 start

Accomplish the following tasks on serverb to complete the exercise. 

### EXERCÍCIO 01 -Create a new directory called /home/student/grading.
```
mkdir  /home/student/grading
```

### EXERCÍCIO 02 - Create three empty files in the /home/student/grading directory named grade1, grade2, and grade3.
```
touch /home/student/grading/grade{1,2,3}
```

### EXERCÍCIO 03 -Capture the first five lines of the /home/student/bin/manage-files file in the /home/student/grading/manage-files.txt file.
```
head -5 /home/student/bin/manage-files > /home/student/grading/manage-files.txt
```

### EXERCÍCIO 04 -Append the last three lines of /home/student/bin/manage-files to the file /home/student/grading/manage-files.txt. You must not overwrite any ### EXERCÍCIO 01 -text already in the file /home/student/grading/manage-files.txt.
```
tail -3 /home/student/bin/manage-files >> /home/student/grading/manage-files.txt
```

### EXERCÍCIO 05 -Copy /home/student/grading/manage-files.txt to /home/student/grading/manage-files-copy.txt.
```
cp /home/student/grading/manage-files.txt /home/student/grading/manage-files-copy.txt
```

### EXERCÍCIO 06 -Edit the file /home/student/grading/manage-files-copy.txt so that there should be two sequential lines of text reading Test JJ.
```
vim /home/student/grading/manage-files-copy.txt
yyp
```

### EXERCÍCIO 07 -Edit the file /home/student/grading/manage-files-copy.txt so that the Test HH line of text must not exist in the file.
```
vim /home/student/grading/manage-files-copy.txt
```

### EXERCÍCIO 08 -Edit the file /home/student/grading/manage-files-copy.txt so that the line A new line should exist between the line reading Test BB and the line reading Test CC.
```
vim /home/student/grading/manage-files-copy.txt
esc + o
```

### EXERCÍCIO 09 -Create a hard link named /home/student/hardlink to the file /home/student/grading/grade1. You will need to do this after creating the empty ### EXERCÍCIO 01 -file /home/student/grading/grade1 as specified above.
```
touch /home/student/grading/grade1
ln /home/student/grading/grade1 /home/student/hardlink 
```

### EXERCÍCIO 10 -Create a soft link named /home/student/softlink to the file /home/student/grading/grade2.
```
ln -s /home/student/grading/grade2 /home/student/softlink
```

### EXERCÍCIO 11 - Save the output of a command that lists the contents of the /boot directory to the file /home/student/grading/longlisting.txt. The output should be a “long listing” that includes file permissions, owner and group owner, size, and modification date of each file. 
```
ls -l /boot >  /home/student/grading/longlisting
```

lab rhcsa-rh124-review1 grade
lab rhcsa-rh124-review1 finish

## Lab: Managing Users and Groups, Permissions and Processes

lab rhcsa-rh124-review2 start

Accomplish the following tasks on serverb to complete the exercise.

### EXERCÍCIO 01 - Terminate the process that is currently using the most CPU time.

```
top
k
```

### EXERCÍCIO 02 - Create a new group called database that has the GID 50000.

```
groupadd -g 50000 database
```

### EXERCÍCIO 03 - Create a new user called dbuser1 that uses the group database as one of its secondary groups. The initial password of dbuser1 should be set to redhat. Configure the user dbuser1 to force a password change on its first login. The user dbuser1 should be able to change its password after 10 days since the day of the password change. The password of dbuser1 should expire in 30 days since the last day of the password change.

```
useradd -G database dbuser1
passwd dbuser1
chage -d 0 dbuser1
chage -m 10 dbuser1
chage -M 30 dbuser1

chage -l dbuser1

date -d "+ 30 days" +%F
chage -E 2021-04-05 dbuser1
```

PONTOS DE ATENÇÃO?

- FAZER O chage -l PRA CONFERIR
- VERIFICAR O QUE ESTÁ PEDINDO DIREITINHO

### EXERCÍCIO 04 - Configure the user dbuser1 to use sudo to run any command as the superuser.

```
echo "dbuser1 ALL=(ALL) ALL" >> /etc/sudoers.d/dbuser1
```

### EXERCÍCIO 05 - Configure the user dbuser1 to have a default umask of 007.

```
echo "umask 007" >> /home/dbuser1/.bash_profile
echo "umask 007" >> /home/dbuser1/.bashrc
```

PONTOS DE ATENÇÃO?

- TEM QUE FAZER NOS DOIS ARQUIVOS 


### EXERCÍCIO 06 - Create a new directory called /home/student/grading/review2 with student and database as its owning user and group respectively. Configure the permissions on that directory so that any new file in it inherits database as its owning group irrespective to the creating user. The permissions on /home/student/grading/review2 should allow the group members of database and the user student to access the directory and create contents in it. All other users should have read and execute permissions on the directory. Also, ensure that the users are only allowed to delete the files, they own, from /home/student/grading/review2 and not others' files. 

```
mkdir /home/student/grading/review2
chown student:database /home/student/grading/review2
chmod g+s /home/student/grading/review2
chmod 775 /home/student/grading/review2
chmod o+t /home/student/grading/review2
```

lab rhcsa-rh124-review2 grade
lab rhcsa-rh124-review2 finish


## Lab: Configuring and Managing a Server

 lab rhcsa-rh124-review3 start

 Accomplish the following tasks on serverb to complete the exercise.

### EXERCÍCIO 01 - Generate SSH keys for the user student on serverb. Do not protect the private key with a passphrase. The private and public key files should be named /home/student/.ssh/review3_key and /home/student/.ssh/review3_key.pub respectively.
```
cd .ssh
ssh-keygen -f review3_key
ls -l
```

### EXERCÍCIO 02 - On servera, configure the user student to accept logins authenticated by the SSH key pair you created for the user student on serverb. The user student on serverb should be able to log in to servera using SSH without entering a password.
```
ssh-copy-id -i .ssh/review3_key.pub servera
ssh -i .ssh/review3_key servera
```

PONTOS DE ATENÇÃO?

- NA HORA DE FAZER SSH USA A CHAVE PRIVADA OK ? PRI-VA-DA !!!!! 

### EXERCÍCIO 03 - On serverb, configure the sshd service to prevent users from logging in as root via SSH.
### EXERCÍCIO 04 - On serverb, configure the sshd service to prevent users from using their passwords to log in. Users should still be able to authenticate logins using an SSH key pair.
```
vim /etc/ssh/sshd_config
PermitRootLogin no
PassworAuthentication no
systemctl restart sshd
```

PONTOS DE ATENÇÃO?

- 

### EXERCÍCIO 05 - Create a tar archive named /tmp/log.tar containing the contents of /var/log on serverb. Remotely transfer the tar archive to /tmp directory on servera, authenticating as student using the student user’s private key of the SSH key pair.
```
tar -cvf /tmp/log.tar /var/log/*
scp -i .ssh/review3_key /tmp/log.tar servera:/tmp/
```

PONTOS DE ATENÇÃO?

- CUIDADO COM A SEQUÊNCIA DOS PARÂMETROS DO COMANDO!! PRIMEIRO A CHAVE -i

### EXERCÍCIO 06 - Configure the rsyslog service on serverb to log all messages it receives that have the priority level of debug or higher to the file /var/log/grading-debug. This configuration should be set in an /etc/rsyslog.d/grading-debug.conf file, which you need to create.
```
echo "*.debug /var/log/grading-debug" > /etc/rsyslog.d/grading-debug.conf
systemctl restart rsyslog
loggr -p debug "teste"
tail /var/log/grading-debug
```

### EXERCÍCIO 07 - Install the zsh package, available in the BaseOS repository, on serverb.
```
yum list --available zsh
yum install zsh
yum list --installed zsh
```

### EXERCÍCIO 08 - Enable the default module stream for the module python36 and install all provided packages from that stream on serverb.
```
yum module list pyton36
yum module enable pyton36
yum module install pyton36
yum module list pyton36
```

### EXERCÍCIO 09 - Set the time zone of serverb to Asia/Kolkata. 

```
tzselect
timedatectl set-timezone Asia/Kolkata
```

 lab rhcsa-rh124-review3 grade
 lab rhcsa-rh124-review3 finish

## Lab: Lab: Managing Networks

lab rhcsa-rh124-review4 start

### EXERCÍCIO 01 - Determine the name of the Ethernet interface and its active connection profile on serverb.

```
nmcli conn
```

### EXERCÍCIO 02 - On serverb, create a new connection profile called static for the available Ethernet interface that statically sets network settings and does not use DHCP. Use the settings in the following table.
### EXERCÍCIO 03 - Set the server’s Ethernet interface to use the updated network settings displayed in the table above.

    IPv4 address	172.25.250.111
    Netmask	255.255.255.0
    Gateway	172.25.250.254
    DNS server	172.25.250.254

```
nmcli conn add type ethernet ifname eth0 con-name static ipv4.address 172.25.250.111/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.250.254 ipv4.method manual connection.autoconnect yes
nmcli connection up static

ou 

nmtui
```

### EXERCÍCIO 04 - Ensure that the host name of serverb is statically set to server-review4.lab4.example.com.

```
hostnamectl
hostnamectl set-hostname server-review4.lab4.example.com
hostname server-review4.lab4.example.com
hostnamectl
hostname
```

### EXERCÍCIO 05 - On serverb, set client-review4 as the canonical host name for the IPv4 address 172.25.250.10 of the host servera.lab.example.com.

```
vi /etc/hosts 

172.25.250.10 servera.lab.example.com client-review4

getent hosts client-review4
```

### EXERCÍCIO 06 - Configure the additional IPv4 address 172.25.250.211 with the netmask 255.255.255.0 on the same interface of serverb that has the existing static network settings. Do not remove the existing IPv4 address. Make sure that serverb responds to all addresses when the connection you statically configured on its interface is active.

```
nmcli con modify static +ipv4.address 172.25.250.211/24
nmcli con show static
ip addr
nmcli con up static
ip addr
ping 172.25.250.111
```

### EXERCÍCIO 07 - On serverb, restore the original network settings by activating the original network connection and deactivating the static network connection you created manually. 

```
nmcli con down static
nmcli con up "Wired connection 1"
nmcli con modify static connection.autoconnect no
nmcli con modify "Wired connection 1" connection.autoconnect yes

```

lab rhcsa-rh124-review4 grade
lab rhcsa-rh124-review4 finish


## Lab: Mounting Filesystems and Finding Files

lab rhcsa-rh124-review5 start

### EXERCÍCIO 01 - On serverb, a block device containing the XFS file system exists but is not yet mounted. Determine the block device and mount it on the /review5-disk directory. Create the /review5-disk directory, if necessary.

```
mkdir /review5-disk
blkid 
echo "UUID=blablabla /review5-disk xfs defaults 0 0" >> /etc/fstab
mount -a
```
### EXERCÍCIO 02 - On serverb, locate the file called review5-path. Create a file named /review5-disk/review5-path.txt that contains a single line consisting of the absolute path to the review5 file.

```
find / -iname review5-path 2>/dev/null
find / -name review5-path 2>/dev/null
find / -name review5-path 2>/dev/null > /review5-disk/review5-path.txt
cat /review5-disk/review5-path.txt
```

### EXERCÍCIO 03 - On serverb, locate all the files having contractor1 and contractor as the owning user and group, respectively. The files must also have the octal permissions of 640. Save the list of these files in /review5-disk/review5-perms.txt.

```
find / -group contractor -user contractor1 -perm 640 2>/dev/null > /review5-disk/review5-perms.txt
cat /review5-disk/review5-perms.txt
```

### EXERCÍCIO 04 - On serverb, locate all files 100 bytes in size. Save the absolute paths of these files in /review5-disk/review5-size.txt. 

```
find / -size 100c 2>/dev/null
find / -size 100c 2>/dev/null >  /review5-disk/review5-size.txt
cat  /review5-disk/review5-size.txt
```

lab rhcsa-rh124-review5 grade
lab rhcsa-rh124-review5 finish

# RH134 - Comprehensive Review

## Lab: Fixing Boot Issues and Maintaining Servers

lab rhcsa-compreview1 start

Perform the following tasks on serverb to complete the comprehensive review:

### EXERCÍCIO 01 - On workstation, run the lab rhcsa-compreview1 break1 command. This break script causes the boot process to fail on serverb. It also sets a longer timeout on the GRUB2 menu to help interrupt the boot process, and reboots serverb.
### EXERCÍCIO 02 - Troubleshoot the possible cause and repair the boot failure. The fix must ensure that serverb reboots without intervention. Use redhat as the password of the superuser, when required.

```
systemd.unit=emergency.target
mount -o remount,rw /
vi /etc/fstab
systemctl reboot
```

### EXERCÍCIO 03 - On workstation, run the lab rhcsa-compreview1 break2 command. This break script causes the default target to switch from the multi-user target to the graphical target on serverb. It also sets a longer timeout for the GRUB2 menu to help interrupt the boot process, and reboots serverb.
### EXERCÍCIO 04 - On serverb, fix the default target to use the multi-user target. The default target settings must persist after reboot without manual intervention. Use the sudo command, as the student user with student as the password, for performing privileged commands.

```
sudo systemctl get-default
sudo systemctl set-default multi-user.target
sudo systemctl isolate multi-user.target
sudo systemctl reboot
sudo systemctl get-default
```

### EXERCÍCIO 05 - Schedule a recurring job as the student user that executes the /home/student/backup-home.sh script on an hourly basis between 7 p.m. and 9 p.m. on all days except Saturday and Sunday.
### EXERCÍCIO 06 - Download the backup script from http://materials.example.com/labs/backup-home.sh. The backup-home.sh backup script backs up the /home/student directory from serverb to servera in the /home/student/serverb-backup directory. Use the backup-home.sh script to schedule the recurring job as the student user on serverb.

```
wget http://materials.example.com/labs/backup-home.sh
chmod +x backup-home.sh
crontab -e
0 19-21 * * Mon-Fri /home/student/backup-home.sh
```

### EXERCÍCIO 07 - Reboot the system and wait for the boot to complete before grading. 

```
sudo systemctl reboot
```

PONTOS DE ATENÇÃO?

- 

## Lab: Configuring and Managing File Systems and Storage

lab rhcsa-compreview2 start

 Perform the following tasks on serverb to complete the comprehensive review.

### EXERCÍCIO 01 - Configure a new 1 GiB logical volume called vol_home in a new 2 GiB volume group called extra_storage. Use the unpartitioned /dev/vdb disk to create partitions. The logical volume vol_home should be formatted with the XFS file-system type, and mounted persistently on /home-directories.

```
parted /dev/vdb mklabel msdos
parted /dev/vdb mkpart primary 1GiB 3GiB
parted /dev/vdb set 1 lvm on
parted /dev/vdb print

udevadm settle
pvcreate /dev/vdb1
vgcreate extra_storage /dev/vdb1 
lvcreate -n vol_home -L 1GiB extra_storage
mkfs.xfs /dev/mapper/extra_storage-vol_home

mkdir /home-directories
vi /etc/fstab
mount -a
```

### EXERCÍCIO 02 - Ensure that the network file system called /share is persistently mounted on /local-share across reboot. The NFS server servera.lab.example.com exports the /share network file system. The NFS export path is .

```
mkdir /local-share
mount -t nfs servera.lab.example.com:/share /local-share
mount | grep nfs
umount /local-share

vi /etc/fstab
servera.lab.example.com:/share /local-share nfs defaults 0 0
mount -a 
mount | grep nfs
```

### EXERCÍCIO 03 - Create a new 512 MiB partition on the /dev/vdc disk to be used as swap space. This swap space must be automatically activated at boot.

```
parted /dev/vdc mklabel msdos
parted /dev/vdc mkpart primary 1MiB 513MiB
parted /dev/vdc print

mkswap /dev/vdc1
echo "UUID=blablabla swap swap defaults 0 0" >> /etc/fstab
swapon
swapon -a
swapon
```

### EXERCÍCIO 04 - Create a new group called production. Create the production1, production2, production3, and production4 users. Ensure that they use the new group called production as their supplementary group.

```
groupadd production
useradd -G production production1
useradd -G production production2
useradd -G production production3
useradd -G production production4
getent passwd 
getent group
```

### EXERCÍCIO 05 - Configure your system so that it uses a new directory called /run/volatile to store temporary files. Files in this directory should be subject to time based cleanup if they are not accessed for more than 30 seconds. The octal permissions for the directory must be 0700. Make sure that you use the /etc/tmpfiles.d/volatile.conf file to configure the time based cleanup for the files in /run/volatile.

```
echo "d /run/volatile 0700 root root 30s" > /etc/tmpfiles.d/volatile.conf
systemd-tmpfiles --create /etc/tmpfiles.d/volatile.conf
```

### EXERCÍCIO 06 - Create the new directory called /webcontent. Both the owner and group of the directory should be root. The group members of production should be able to read and write to this directory. The production1 user should only be able to read this directory. These permissions should apply to all new files and directories created under the /webcontent directory. 

```
mkdir /webcontent
setfacl -Rm d:g:production:rwx /webcontent
setfacl -Rm g:production:rwx /webcontent
setfacl -Rm d:u:production1:rx /webcontent
setfacl -Rm u:production1:rx /webcontent

getfacl /webcontent

su - production1 
cd /webcontent
touch teste

su - production12
cd /webcontent
touch teste
```

## Lab: Configuring and Managing Server Security

lab rhcsa-compreview3 start

Perform the following tasks to complete the comprehensive review:

### EXERCÍCIO 01 - Generate SSH keys for the student user on serverb. Do not protect the private key with a passphrase.
### EXERCÍCIO 02 - On servera, configure the student user to accept login authentication using the SSH key pair created for student on serverb. The student user on serverb should be able to log in to servera using SSH without entering a password. Use student as the password of the student user, if required.

```
ssh-keygen
ssh-copy-id servera
ssh servera
```

### EXERCÍCIO 03 - On servera, change the default SELinux mode to permissive.

```
getenforce
setenforce 0
vi /etc/sysconfig/selinux
SELINUX=permissive
```

### EXERCÍCIO 04 - Configure serverb to automatically mount the home directory of the production5 user when the user logs in, using the network file system /home-directories/production5. This network file system is exported from servera.lab.example.com. Adjust the appropriate SELinux Boolean so that production5 can use the NFS-mounted home directory on serverb after authenticating via SSH key-based authentication. The production5 user's password is redhat.

```
mount -t nfs servera.lab.example.com:/home-directories/production5 /mnt

yum install autofs -y

echo "/- /etc/auto.production5" > /etc/auto.master.d/production5.autofs 
echo "/localhome/production5 -rw,sync servera.lab.example.com:/home-directories/production5" > /etc/auto.production5
systemctl enable --now autofs
su - production5 
df -h

getsebol -a | grep nfs
use_nfs_home_dirs --> off

setsebool use_nfs_home_dirs on -P
getsebo0l use_nfs_home_dirs
```

- ATENÇÃO: NÃO ESQUECER DO BOOLEAN DO SELINUX

### EXERCÍCIO 05 - On serverb, adjust the firewall settings so that the SSH connections originating from servera are rejected.

```
firewall-cmd --zone=block --list-all
firewall-cmd --zone=block --add-source=172.25.250.10/32
servera $ ssh-serverb
no route to host
```

### EXERCÍCIO 06 - On serverb, investigate and fix the issue with the Apache HTTPD daemon, which is configured to listen on port 30080/TCP, but which fails to start. Adjust the firewall settings appropriately so that the port 30080/TCP is open for incoming connections. 

```
sealert -a /var/log/audit/audit.log > log.txt
more log.txt
semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 30080

servera $ curl http://serverb:30080
No route to host

firewall-cmd --add-port=30080/tcp --permanent
firewall-cmd --reload
```

Se tivesse problema no diretório

```
ls -alZ /var/www/html
semanage -a -t http_sys_content_t '/webcontent(/.*)&'
restorecon -Rv /webcontent
```

## Lab: Running Containers

lab rhcsa-compreview4 start

The password for the containers user is redhat. To access the container image registry at registry.lab.example.com, use the admin account with redhat321 as the password. You can copy and paste the web container parameters from the /home/containers/rhcsa-compreview4/variables file on serverb. 

### EXERCÍCIO 01 - On serverb, create the /srv/web/ directory, and then extract the /home/containers/rhcsa-compreview4/web-content.tgz archive in that directory. Configure the directory so that a rootless container can use it for persistent storage.

```
mkdir /srv/web
cd /srv/web
cp /home/containers/rhcsa-compreview4/web-content.tgz .
gunzip web-content.tgz
tar -xvf web-content.tar

chown -R containers:containers /srv/web
```

### EXERCÍCIO 02 - On serverb, install the container tools.

```
yum module install container-tools
```

### EXERCÍCIO 03 - On serverb, as the containers user, create a detached Apache HTTP Server container named web. Use the rhel8/httpd-24 image with the tag 1-105 from the registry.lab.example.com registry. Map port 8080 in the container to port 8888 on the host. Mount the /srv/web directory on the host as /var/www in the container. Declare the environment variable HTTPD_MPM with event for the value.

```
su - containers

mkdir -p .config/systemd/user

cd .config/systemd/user

podman login registry.lab.example.com

podman run -d -p 8888:8080 -v /srv/web:/var/www:Z -e HTTPD_MPM=event --name web registry.lab.example.com/rhel8/httpd-24:1-105

podman ps

curl http://localhost:8888

```

### EXERCÍCIO 04 - On serverb, as the containers user, configure systemd so that the web container starts automatically with the server. 

```
podman ps

podman generate systemd -n web --files --new

logar com usuário containers

loginctl enable-linger

systemctl --users daemon-reload 

systemctl --users enable --now container-web.service

curl http://localhost:8888
```


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


