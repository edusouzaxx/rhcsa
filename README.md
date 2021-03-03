# RH124

# RH134

### Lab Final

### Exercicio 01

```
e
systemd.unit=emergency.target ou rd.break
crtl+x

[root@serverb ~]# mount -o remount,rw /
[root@serverb ~]# mount -a
[root@serverb ~]# vim /etc/fstab
#UUID=fake     /FakeMount  xfs   defaults    0 0
[root@serverb ~]# systemctl daemon-reload
[root@serverb ~]# mount -a
[root@serverb ~]# systemctl reboot
```

Dicas: 

### Exercicio 02

```
[student@serverb ~]$ systemctl get-default
[student@serverb ~]$ sudo systemctl isolate multi-user.target
[student@serverb ~]$ sudo systemctl set-default multi-user.target
[student@serverb ~]$ sudo systemctl reboot
[student@serverb ~]$ systemctl get-default
```

Dicas: 

### Exercicio 03 

```
[student@serverb ~]$ wget http://materials.example.com/labs/backup-home.sh
[student@serverb ~]$ chmod +x backup-home.sh
[student@serverb ~]$ crontab -e

0 19-21 * * Mon-Fri /home/student/backup-home.sh
ou
0 19-21 * * 1-5 /home/student/backup-home.sh

[student@serverb ~]$ crontab -l

0 19-21 * * Mon-Fri /home/student/backup-home.sh
```

Dicas: 

- Lembrar do chmod +x
- man crontab.5

### Exercicio 04 

```
[root@serverb ~]# parted /dev/vdb mklabel msdos
[root@serverb ~]# parted /dev/vdb mkpart primary 1GiB 3GiB
[root@serverb ~]# parted /dev/vdb set 1 lvm on

[root@serverb ~]# pvcreate /dev/vdb1
[root@serverb ~]# vgcreate extra_storage /dev/vdb1
[root@serverb ~]# lvcreate -L 1GiB -n vol_home extra_storage

[root@serverb ~]# mkdir /home-directories
[root@serverb ~]# mkfs -t xfs /dev/extra_storage/vol_home
[root@serverb ~]# lsblk -o UUID /dev/extra_storage/vol_home
[root@serverb ~]# echo "UUID=988c...0ab6 /home-directories xfs defaults 0 0" >> /etc/fstab
```

### Exercicio 05

```
[root@serverb ~]# mkdir /local-share
[root@serverb ~]# echo "servera.lab.example.com:/share /local-share nfs rw,sync 0 0" >> /etc/fstab
[root@serverb ~]# mount /local-share
```

### Exercicio 06

```
[root@serverb ~]# parted /dev/vdc mklabel msdos
[root@serverb ~]# parted /dev/vdc mkpart primary linux-swap 1MiB 513MiB
[root@serverb ~]# mkswap /dev/vdc1
[root@serverb ~]# lsblk -o UUID /dev/vdc1
[root@serverb ~]# echo "UUID=cc18...1b69 swap swap defaults 0 0" >> /etc/fstab
[root@serverb ~]# swapon -a
```

### Exercicio 07

```
[root@serverb ~]# groupadd production
[root@serverb ~]# for i in 1 2 3 4; do useradd -G production production$i; done
```

### Exercicio 08

```
[root@servera ~]# echo "d /run/volatile 0700 root root 30s" > /etc/tmpfiles.d/volatile.conf
[root@servera ~]# systemd-tmpfiles --create /etc/tmpfiles.d/volatile.conf
```

Dicas: 

man -k tmpfiles
man tmpfiles.d.5

### Exercicio 09 

```
[root@serverb ~]# mkdir /webcontent
[root@serverb ~]# setfacl -m u:production1:rx /webcontent
[root@serverb ~]# setfacl -m g:production:rwx /webcontent
[root@serverb ~]# setfacl -m d:u:production1:rx /webcontent
[root@serverb ~]# setfacl -m d:g:production:rwx /webcontent
[root@serverb ~]# getfacl /webcontent
```

Dicas: 

- Lembrar de colocar o x sempre que for pasta
- d pra defaults
- m sempre que for modificar

### Exercicio 10

```
[student@serverb ~]$ ssh-keygen
[student@serverb ~]$ ssh-copy-id student@servera
[student@serverb ~]$ ssh student@servera
```

### Exercicio 10

```
[student@servera ~]$ sudo vi /etc/sysconfig/selinux
#SELINUX=enforcing
SELINUX=permissive
[student@servera ~]$ sudo systemctl reboot
[student@servera ~]$ sudo sestatus
```

Dicas:

- sestatus
- 

### Exercicio 11

```
[root@serverb ~]# yum install autofs
[root@serverb ~]# echo "/- /etc/auto.production5" > /etc/auto.master.d/production5.autofs
[root@serverb ~]# echo "/localhome/production5 -rw servera.lab.example.com:/home-directories/production5" > /etc/auto.production5
[root@serverb ~]# getent passwd production5
[root@serverb ~]# systemctl restart autofs
[root@estacao ~]# ssh production5@serverb
[production5@serverb ~]# df -h
```

Dicas:

- No arquivo /etc/auto.blablabla só 3 parâmetros ( mountpoint, -opções, device )
- tem que ter o sinal de menos no rw (-rw)

### Exercicio 12

```
[production5@servera ~]$ ssh-keygen
[production5@servera ~]$ ssh-copy-id production5@serverb
[production5@servera ~]$ ssh -o pubkeyauthentication=yes -o passwordauthentication=no production5@serverb
Permission denied
[root@serverb ~]# setsebool -P use_nfs_home_dirs true
[production5@servera ~]$ ssh -o pubkeyauthentication=yes -o passwordauthentication=no production5@serverb
ok
```

Dicas:

- 
- 
### Exercicio 1

```
[root@serverb ~]# firewall-cmd --add-source=172.25.250.10/32 --zone=block --permanent
[root@serverb ~]# firewall-cmd --reload
[production5@servera ~]$ ssh serverb
No route to host
```

Dicas:

- 
- 
### Exercicio 1

```
[root@serverb ~]# systemctl restart httpd.service
Job for httpd.service failed

[root@serverb ~]# systemctl status httpd.service
Apr 15 06:42:41 serverb.lab.example.com httpd[27313]: (13)Permission denied: AH00072: make_sock: could not bind to address [::]:30080

[root@serverb ~]# sealert -a /var/log/audit/audit.log
# semanage port -a -t PORT_TYPE -p tcp 30080
    where PORT_TYPE is one of the following: http_cache_port_t, http_port_t, jboss_management_port_t, jboss_messaging_port_t, ntop_port_t, puppet_port_t.

[root@serverb ~]# semanage port -a -t http_port_t -p tcp 30080
[root@serverb ~]# systemctl restart httpd
[root@serverb ~]# firewall-cmd --add-port=30080/tcp --permanent
[root@serverb ~]# firewall-cmd --reload
[root@serverb ~]# curl localhost:30080
```

Dicas:

- 
- 
### Exercicio 1

```
[containers@serverb ~]$ podman login registry.lab.example.com
[containers@serverb ~]$ podman run -d --name web -p 8888:8080 -v /srv/web:/var/www:Z -e HTTPD_MPM=event registry.lab.example.com/rhel8/httpd-24:1-105
[containers@serverb ~]$ curl http://localhost:8888/


[student@workstation ~]$ ssh containers@serverb
[containers@serverb ~]$ mkdir -p ~/.config/systemd/user/
[containers@serverb ~]$ cd ~/.config/systemd/user/
[containers@serverb user]$ podman generate systemd --name web --files --new
[containers@serverb user]$ podman stop web
[containers@serverb user]$ podman rm web
[containers@serverb user]$ systemctl --user daemon-reload
[containers@serverb user]$ systemctl --user enable --now container-web.service
[containers@serverb user]$ curl http://localhost:8888/
[containers@serverb ~]$ loginctl enable-linger
[containers@serverb ~]$ systemctl reboot
[containers@serverb user]$ curl http://localhost:8888/
```

Dicas:

- Tem que logar já com o usuário senão dá erro de bus no comando systemctl
- porta do host primeiro assim com o diretório 
- nao esquecer do :Z depois dos volumes 


# RH199

## Lab -  autenticação baseada em chaves SSH

```
[operator1@serverb ~]$ ssh-keygen
sem senha
[operator1@serverb ~]$ ssh-copy-id operator1@servera
[operator1@serverb ~]$ ssh operator1@servera hostname

[operator1@serverb ~]$ ssh-keygen -f .ssh/key2
senha
[operator1@serverb ~]$ ssh-copy-id -i .ssh/key2.pub operator1@servera
[operator1@serverb ~]$ ssh -i .ssh/key2 operator1@servera hostname
pede senha

[operator1@serverb ~]$ eval $(ssh-agent)
[operator1@serverb ~]$ ssh-add .ssh/key2
senha
[operator1@serverb ~]$ ssh -i .ssh/key2 operator1@servera hostname
não pede senha
```

Dicas: 

- ssh-agent só vale pra sessão atual
- $ fora do parêntese eval $(ssh-agent)
- ssh-add .ssh/chave

## Lab - Obter ajuda do Red Hat Customer Portal

```
sudo systemctl status cockpit.socket
https://servera.lab.example.com:9090
 Diagnostic Reports
 Create Report
 Download Report
```

Dicas: 

- 
- 

## Lab - Gerenciamento de arquivos usando ferramentas de linha de comando

```

```

Dicas: 

-  links físicos A contagem de links deve ser 2 para ambos os arquivos e já o link simbólico é 1.
-  links físicos só podem ser usados com arquivos regulares e nao pra diretorios
-  links físicos só podem ser usados se ambos os arquivos estiverem no mesmo sistema de arquivos.
-  links simbólicos podem vincular dois arquivos em diferentes sistemas de arquivos. 
-  links simbólicos podem apontar para um diretório ou arquivo especial


## Lab - 

```
etc/sudoers.d/user01

user01  ALL=(ALL)  ALL
%group01  ALL=(ALL)  ALL
ansible  ALL=(ALL)  NOPASSWD:ALL

```

Dicas: 

- su solicita senha do usuário e configurações de ambiente do usuário original
- su - define o ambiente do shell como se fosse um novo login como esse usuário
- sudo usuário original informa sua própria senha
- o sudo pode ser configurado para permitir que usuários específicos executem qualquer comando como outro usuário
- O comando sudo su - e sudo -i não se comportam exatamente da mesma maneira.
- sudo su - configura o ambiente root exatamente como um login normal
- sudo -i variável de ambiente PATH diferente.
- Páginas do man su(1), sudo(8), visudo(8) e sudoers(5) 

## Lab - Gerenciamento de contas de usuários locais

```
[root@servera ~]# useradd operator3
[root@servera ~]# tail /etc/passwd
[root@servera ~]# passwd operator3
[root@servera ~]# tail /etc/passwd
[root@servera ~]# usermod -c "Operator One" operator1
[root@servera ~]# tail /etc/passwd
[root@servera ~]# userdel -r operator3
[root@servera ~]# tail /etc/passwd
```

Dicas: 

- A UID 0 sempre é atribuída à conta de superusuário, root. 
- As UID de 1 a 200 usuários do sistema Redhat
- As UIDs de 201 a 999 usuários do sistema Terceiros
- As UIDs 1000 e superiores usuários regulares. 

## Lab - Gerenciamento de contas de grupos locais

```
[root@servera ~]# groupadd -g 30000 operators
[root@servera ~]# tail /etc/group
[root@servera ~]# groupadd admin
[root@servera ~]# tail /etc/group
3001

[root@servera ~]# id sysadmin1
[root@servera ~]# usermod -aG admin sysadmin1
[root@servera ~]# id sysadmin1

[root@servera ~]# echo "%admin ALL=(ALL) ALL" >> /etc/sudoers.d/admin
[sysadmin1@servera ~]$ sudo cat /etc/sudoers.d/admin
[sudo] password for sysadmin1: redhat
%admin ALL=(ALL) ALL
```

Dicas: 

- GIDs de sistema válidos listados no arquivo /etc/login.defs
- 

## Lab - Gerenciamento de senhas de usuários

```

[user01@host ~]$ sudo usermod -L -e 2019-10-05 user03   
-L bloqueia senha
-e define data desativacao conta

[student@servera ~]$ sudo usermod -U operator1
-U desbloqueia

[user01@host ~]$ usermod -s /sbin/nologin user03
[user01@host ~]$ su - user03
Last login: Wed Feb  6 17:03:06 IST 2019 on pts/0
This account is currently not available.

[student@servera ~]$ sudo chage -M 90 operator1
Idade máxima

[student@servera ~]$ sudo chage -d 0 operator1
Atualizar proximo login

[student@servera ~]$ date -d "+180 days" +%F
2019-07-24
[student@servera ~]$ sudo chage -E 2019-07-24 operator1
[student@servera ~]$ sudo chage -l operator1

Defina PASS_MAX_DAYS como 180 em /etc/login.defs
sudo vim /etc/login.defs
PASS_MAX_DAYS   180
```

Dicas: 

-  A senha padrão e as configurações de vencimento da conta serão efetivas para novos usuários, mas não para usuários existentes. 
- 

## Lab - Final 

```
 sudo vim /etc/login.defs
 PASS_MAX_DAYS   30

[student@serverb ~]$ sudo groupadd -g 35000 consultants
echo "%consultants ALL=(ALL) ALL" > /etc/sudoers.d/consultants

[student@serverb ~]$ sudo useradd -G consultants consultant1
[student@serverb ~]$ sudo useradd -G consultants consultant2
[student@serverb ~]$ sudo useradd -G consultants consultant3

[student@serverb ~]$ date -d "+90 days" +%F
2019-04-28
[student@serverb ~]$ sudo chage -E 2019-04-28 consultant1
[student@serverb ~]$ sudo chage -E 2019-04-28 consultant2
[student@serverb ~]$ sudo chage -E 2019-04-28 consultant3

[student@serverb ~]$ sudo chage -M 15 consultant2

[student@serverb ~]$ sudo chage -d 0 consultant1
[student@serverb ~]$ sudo chage -d 0 consultant2
[student@serverb ~]$ sudo chage -d 0 consultant3
```

Dicas: 

- Lembrar do arquivo /etc/login.defs
- ALL igual a ALL entre parentese espaço ALL
- lembrar date -d "+90 days" +%F


## Capitulo 4 -  Controle de acesso a arquivos

```
[root@host opt]# chmod -R g+rwX demodir     execução somente diretorios
[user@host ~]$ chmod go-rw file1            remove permissão
[user@host ~]$ chmod a+x file2              permissão pra todos
[root@servera ~]# chown :consultants /home/consultants

[user@host ~]# chmod u+s file           -> sera executado sempre com usuario
[user@host ~]# chmod u+s directory      -> sem efeito
[user@host ~]# chmod g+s file           -> sera executado sempre com o grupo
[user@host ~]# chmod g+s directory      -> arquivos criados sempre com grupo dono
[user@host ~]# chmod o+t file           -> sem efeito
[user@host ~]# chmod o+t directory      -> usuarios controlam somente seus proprios arquivos

[user@host ~]$ umask
0002

- mascara padrão de onde o umask subtrai
0777    -> pastas
0666    -> arquivos
[user@host ~]$ umask 0
[user@host ~]$ touch zero.txt
-rw-rw-rw-. 
[user@host ~]$ mkdir zero
drwxrwxrwx.

[root@host ~]# cat /etc/profile.d/local-umask.sh
# Overrides default umask configuration
if [ $UID -gt 199 ] && [ "`id -gn`" = "`id -un`" ]; then
    umask 007
else
    umask 022
fi

[operator1@servera ~]$ echo "umask 007" >> ~/.bashrc
```

Dicas: 

- S maiúsculo significa que não existe a permissão x por baixo
- 
- 


Laboratório do Capítulo:

```
[root@serverb ~]# chmod 2770 /home/techdocs  -> setgid

cp /etc/profile /etc/profile.d/local-umask.sh
vim /etc/profile.d/local-umask.sh
deixa só script
```

Dicas: 

- Copia /etc/profile /etc/profile.d/local-umask.sh e edita
- Alem de definir o group owner tem que alterar a permissão de grupo senão não adianta :)
- 

## Capítulo 5. Gerenciamento da segurança do SELinux

Teoria rápida: 

- O Security Enhanced Linux (SELinux) é uma camada adicional de segurança do sistema.
- Determina qual processo pode acessar quais arquivos, diretórios e portas.
- Todo arquivo, processo, diretório e porta tem um rótulo de segurança especial, chamado de context do SELinux.

Prática Resumida:

```
[root@host ~]# ps axZ
[root@host ~]# systemctl start httpd
[root@host ~]# ps -ZC httpd
system_u:system_r:httpd_t:s0     1608 ?        00:00:05 httpd

[root@host ~]# ls -Z /home
drwx------. visitor visitor unconfined_u:object_r:user_home_dir_t:s0 visitor
[root@host ~]# ls -Z /var/www
drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 html

[user@host ~]# getenforce
[user@host ~]# setenforce
[user@host ~]# setenforce 0

vi /etc/selinux/config
SELINUX=enforcing
SELINUXTYPE=targeted
```

Teoria rápida: 

- Novos arquivos normalmente herdam seu contexto do SELinux do diretório pai
- Se criar em outro diretorio e mover, ou copiar com opção -a (cp -a) não herda

```
[root@host ~]# chcon -t httpd_sys_content_t /virtual
[root@host ~]# restorecon -v /virtual

[root@host ~]# semanage fcontext -l

[root@host ~]# ls -Z /virtual/      -> arquivos
[root@host ~]# ls -Zd /virtual/     -> pasta
[root@host ~]# semanage fcontext -a -t httpd_sys_content_t '/virtual(/.*)?'  

```

Dicas: 

- Utilização do Z no ps e ls 
- semanage fcontext -a -t httpd_sys_content_t '/virtual(/.*)?'
- Altera o padrão do diretorio que será revertido no restorecon 
- 

```
    [root@servera ~]# mkdir /custom
    [root@servera ~]# echo 'This is SERVERA.' > /custom/index.html
    [root@servera ~]# grep custom /etc/httpd/conf/httpd.conf
    DocumentRoot "/custom"
    <Directory "/custom">
    [root@servera ~]# systemctl enable --now httpd
    [root@servera ~]# systemctl status httpd
    [root@servera ~]# curl http://servera/index.html
    permission denied
    [root@servera ~]# semanage fcontext -a -t httpd_sys_content_t '/custom(/.*)?'
    [root@servera ~]# restorecon -Rv /custom
    [root@servera ~]# curl http://servera/index.html
    This is servera
```

Dicas: 

- ls -Zd /var/www pra pegar o padrão
- -a -t blabla_t aspas simples dir parentese barra ponto asterisco fecha interrogação aspas
- não esquecer do restorecon -Rv depois

Teoria rápida:

-  Os boleanos do SELinux são regras que podem ser habilitadas ou desabilitadas.
- 
- 

```
[user@host ~]$ getsebool -a
[user@host ~]$ getsebool httpd_enable_homedirs
[user@host ~]$ sudo setsebool httpd_enable_homedirs on
[user@host ~]$ getsebool httpd_enable_homedirs

[user@host ~]$ sudo semanage boolean -l | grep httpd_enable_homedirs
(on   ,  off)
[user@host ~]$ setsebool -P httpd_enable_homedirs on
[user@host ~]$ sudo semanage boolean -l | grep httpd_enable_homedirs
(on   ,  on)
[user@host ~]$ sudo semanage boolean -l -C
```

Exercicio:

```
[root@servera ~]# grep '^ *UserDir' /etc/httpd/conf.d/userdir.conf
UserDir public_html
[root@servera ~]# systemctl enable --now httpd

[student@servera ~]$ mkdir ~/public_html
[student@servera ~]$ echo 'This is student content on SERVERA.' > ~/public_html/index.html
[student@servera ~]$ chmod 711 ~
[student@workstation ~]# curl servera/~student/index.html
[root@servera ~]# getsebool -a | grep home
[root@servera ~]# setsebool -P httpd_enable_homedirs on
[student@workstation ~]# curl servera/~student/index.html

```

Teoria rápida : Investigação e solução de problemas do SELinux

```
[root@host ~]# touch /root/file3
[root@host ~]# mv /root/file3 /var/www/html
[root@host ~]# systemctl start httpd
[root@host ~]# curl http://localhost/file3
<p>You don't have permission to access /file3

[root@host ~]# tail /var/log/audit/audit.log
[root@host ~]# tail /var/log/messages
[root@host ~]# sealert -l 613ca624-248d-48a2-a7d9-d28f5bbe2763
[root@host ~]# ausearch -m AVC -ts recent
```

Exercício : Investigue porque o apache não sobe

```
[root@servera ~]# less /var/log/messages
[root@servera ~]# sealert -l b1c9cc8f-a953-4625-b79b-82c4f4f1fee3
[root@servera ~]# ausearch -m AVC -ts recent
[root@servera ~]# semanage fcontext -a -t httpd_sys_content_t '/custom(/.*)?'
[root@servera ~]# restorecon -Rv /custom
```

Laboratório do Capítulo:

```
curl serverb/lab.html
permission denied
[root@serverb ~]# less /var/log/messages
[root@serverb ~]# sealert -l 8824e73d-3ab0-4caf-8258-86e8792fee2d
[root@serverb ~]# ausearch -m AVC -ts recent
[root@serverb ~]# ls -dZ /lab-content /var/www/html
[root@serverb ~]# semanage fcontext -a -t httpd_sys_content_t '/lab-content(/.*)?'
[root@serverb ~]# restorecon -R /lab-content/
curl serverb/lab.html
ok!
```

## Capitulo 06 - Ajuste do desempenho do sistema

Teoria rápida: Encerramento de processos

- 
- 
- 

Prática Resumida:

```
[user@host ~]$ kill -l
[user@host ~]$ kill 5194
[user@host ~]$ kill -9 5199
[user@host ~]$ kill -SIGTERM 5205
[user@host ~]$ killall control
[user@host ~]$ pkill control
[user@host ~]$ pkill -U user

[root@host ~]# pgrep -l -u bob
[root@host ~]# pkill -SIGKILL -u bob

[root@host ~]# pstree -p bob
```

Exercicio:

```
[student@servera bin]$ jobs
[student@servera bin]$ kill -SIGSTOP %1
[student@servera bin]$ kill -SIGTERM %2
[student@servera bin]$ kill -SIGCONT %1

```

Monitoramento de atividade de processo

```
[user@host ~]$ uptime
[user@host ~]$ lscpu
[user@host ~]$ top
```

Adjusting Tuning Profiles

```
[root@host ~]$ yum install tuned
[root@host ~]$ systemctl enable --now tuned
[root@host ~]# tuned-adm active
[root@host ~]# tuned-adm list
[root@host ~]$ tuned-adm profile throughput-performance
[root@host ~]$ tuned-adm active

[root@host ~]$ tuned-adm recommend

[root@host ~]$ tuned-adm off
[root@host ~]$ tuned-adm active
No current active profile.
```

Influencing Process Scheduling

- The nice level values range from -20 (highest priority) to 19 (lowest priority).

```
[user@host ~]$ ps axo pid,comm,nice,cls --sort=-nice
[user@host ~]$ sha1sum /dev/zero &
[user@host ~]$ ps -o pid,comm,nice 3480

[user@host ~]$ nice sha1sum /dev/zero & -> começa com default nice
[user@host ~]$ nice -n 15 sha1sum &
[user@host ~]$ renice -n 19 3521

[student@servera ~]$ ps u $(pgrep sha1sum)  -> porcentagem cpu processos sha1sum
```

Laboratório do Capítulo: Lab: Tuning System Performance

```
[student@serverb ~]$ yum install tuned
[student@serverb ~]$ systemctl enable --now tuned
[student@serverb ~]$ sudo tuned-adm profile balanced

[student@serverb ~]$ ps aux --sort=pcpu
[student@serverb ~]$ sudo renice -n 10 2967 2983
[student@serverb ~]$ ps -o pid,pcpu,nice,comm $(pgrep sha1sum;pgrep md5sum)
```

## Chapter 7. Installing and Updating Software Packages

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```

## Capitulo XX - assunto

Teoria rápida: 

- 
- 
- 

Prática Resumida:

```

```

Dicas: 

- 
- 
- 

Laboratório do Capítulo:

```

```
