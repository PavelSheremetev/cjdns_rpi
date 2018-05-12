### Подготовка sd карты для raspbery pi amd64

## 1 скачиваем образ последний rasbian в место /dev/sda указываем путь до карты памяти
```
mkdir rpi
cd rpi
wget --content-disposition  https://downloads.raspberrypi.org/raspbian_lite_latest
unzip *-raspbian-stretch-lite.zip 
mv *-raspbian-stretch-lite.img raspbian-stretch-lite.img
sudo dd if=raspbian-stretch-lite.img of=/dev/sda bs=4M
sync
```
# 1.1 переподключаем sdcard
```
mkdir mnt
sudo mount /dev/sda2 ./mnt 
sudo mount /dev/sda1 ./mnt/boot 
```
##  2 активируем сервер ssh
```
sudo touch ./mnt/boot/ssh
```
# 2.2 добавляем rsi ключи для подключения по ssh 

```
sudo mkdir ./mnt/root/.ssh
sudo chmod 700 ./mnt/root/.ssh
sudo bash -c 'cat <<EOF > ./mnt/root/.ssh/authorized_keys
ssh-rsa 
AAAAB3NzaC1yc2EAAAADAQABAAABAQChd+ARrsZH9uxWqY5mpPqKhkYGkZvww4l+OLB4FZW5Cm82UFBub7TH7RE167RT63od1djCbUIH5sIj9Tgt8RszP1QYSXxweGITn/VVJ8XWo2oduiXrcHQiHH1AFxXeRZg611MRJ5wWObHEnpjv9ae1Eh7/FZzvs/du15A5Jx2p/09BBc8HVeCAZUl28rKfrbQtRJJwDXOBdoA1YlMF0zP95HC1JT9yv2FRtNlK4FStnayw9gMwOU6tU90rHVAMBHgMAYEcCcLvfzByFHd1QgJCa6VzX9B1TMv8txgTUFknGYxUf40IWSNXgmFU3kYMbXQ9LuV0MoJGwISMt22SvHAf 
spd@aira.life
ssh-rsa 
AAAAB3NzaC1yc2EAAAADAQABAAACAQC/FL9uTGxLKKVeMe1O2PSTzOgu23hRoHe4GA2hNreYo6lwkSO9T6GU2iU4nq9HHBLTN1uBe9ttp61QYiAuddyOWss9zkcu1WU5gMOXmBqFZPj69C/BpOzQZecq1P7AkKIu7esq+fe6ANKMinGJVQhrsrknwYVLznp8OfxIHzq6PLJADI8GZBBGq4hy4AsLbGZqt1jK8aDWoUrwSt+5QdQjMLxaljFNxonacmqoMa391V2vncN+npaQETI2nNxoXeLcPy0Yt0oPJccU9oYQh+fg52jSI3yXR8+fhDQyQLKVHHpYtH88wBSu4/fiwRWRAqRJYZg7AYAluEwNFePWnIH/UP1cUbiteWDKvwAoaaXKvGO5mBNi79G+Y42xv72RdHSIiBpvp4zuUCC5bt87k1QwjHeKGVhNMR2ba2Xz4AoJEw7VnCu8R8K6nif6LEr1kbIgmzM+LefnDggzONF5K7IGoMzjMfTJpdLDO3+MJiBsTW471HXgw2UzZKR2gpcHFkf3GcLsOcIOE9lqoDoFHvwpR8IhnK42qdV+m6VaXoWeSEjZEE99vqVNKyWHDAB4MZxNOrwIYSqnHExoNE8UixUYeL2F3Qv3Onjfg4sjDZVA+r0xjO97qm6IDk9NbWmna8E7vZGEh4GPTdHPwfFZHfJL3p8kFNh7AfZHSy1qrtXttw== 
alisher@aira.life
EOF'
sudo chmod 600 ./mnt/root/.ssh/authorized_keys 
```
## 3 прописываем данные для подключения к wifi  
```
sudo nano ./mnt/etc/wpa_supplicant/wpa_supplicant.conf
##add to end file
network={
    ssid="SSID"
    psk="SSIDPassword"
}
```
## 4 Устанавлмваем cjdns и скачиваем конфиг с ip6 fc50:f733:66ce:1955:b04f:8374:6b4a:b2a9
```
sudo wget https://raw.githubusercontent.com/PavelSheremetev/cjdns_rpi/master/bin/cjdroute -P ./mnt/usr/bin/
sudo chmod 755 ./mnt/usr/bin/cjdroute
sudo wget https://raw.githubusercontent.com/PavelSheremetev/cjdns_rpi/master/systemd/cjdns.service -P ./mnt/etc/systemd/system/
sudo wget https://raw.githubusercontent.com/PavelSheremetev/cjdns_rpi/master/systemd/cjdns-resume.service -P ./mnt/etc/systemd/system/
sudo wget  https://raw.githubusercontent.com/PavelSheremetev/cjdns_rpi/master/etc/cjdroute.conf -P ./mnt/etc/
```
# 4.1 прописываем активацию и запуск сервиса cjdns
в файле ./mnt/etc/rc.local
```
sudo nano ./mnt/etc/rc.local	
```
добавляем после стоки # By default this script does nothing.   
```
/bin/systemctl enable cjdns.service 
/bin/systemctl start cjdns.service 
sed -i '/systemctl/d' /etc/rc.local
```
## 5  освобождаем sd карту
```
sudo umount ./mnt/boot/
sudo umount ./mnt/
sync
```
## 6 Вставляем карту памяти в raspbery и включаем питание 

через несколько минут адрес fc50:f733:66ce:1955:b04f:8374:6b4a:b2a9 должен пинговаться 
```
ssh root@fc50:f733:66ce:1955:b04f:8374:6b4a:b2a9

```
или 
```
ssh pi@fc50:f733:66ce:1955:b04f:8374:6b4a:b2a9

```
пароль по умолчанию raspberry
