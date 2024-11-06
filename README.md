# NFS
Устанавливаем NFS-сервер
```
apt install nfs-kernel-server -y
```
Проверяем наличие открытх портов 111 и 2049
```
ss -tulpn |grep -e "111" -e "2049"
```
Создаём и настраиваем директорию, которая будет экспортирована
```
mkdir -p /srv/share/upload
chown -R nobody:nogroup /srv/share
chmod 0777 /srv/share/upload
```
Cоздаём в файле /etc/exports структуру, которая позволит экспортировать ранее созданную директорию
```
cat << EOF > /etc/exports 
/srv/share 192.168.50.11/32(rw,sync,root_squash)
EOF
```
Экспортируем созданную директорию
```
exportfs -r
```
Проверяем экспортированную директорию

![image](https://github.com/user-attachments/assets/7936301d-779d-4a4b-914c-fcd8727362b0)

На клиенте устанавливаем NFS-пакет
```
apt install nfs-common -y
```
Добавляем в /etc/fstab строку
```
echo "192.168.50.10:/srv/share/ /mnt nfs vers=3,noauto,x-systemd.automount 0 0" >> /etc/fstab
```
Выполняем команды
```
systemctl daemon-reload
systemctl restart remote-fs.target
```

Проверяем статус монтирования на клиенте

![image](https://github.com/user-attachments/assets/d9408b87-e104-475c-8cc3-4f171ac37649)

 `vers=3`, что соответствует NFSv3, как того требует задание.
