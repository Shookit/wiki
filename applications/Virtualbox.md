Virtualbox
==========

Compress Virtualbox image
-------------------------

### Reclaim space

Look for big directories

```
du -hs .[^.]*
```

mysql

```
mysql -ppass -u root
SHOW BINARY LOGS;
PURGE BINARY LOGS TO 'mysql-bin.000293';
```

docker

```
sudo systemctl stop docker
sudo rm -rf /var/lib/docker
sudo systemctl start docker
```

mongo

```
mongo --quiet --eval 'db.getMongo().getDBNames().forEach(function(i){db.getSiblingDB(i).dropDatabase()})'
```

Journal

```
sudo journalctl --vacuum-time=2d
```

pacman

```
sudo pacman -Sc
sudo rm /var/cache/pacman/pkg/*
```

### Linux

```bash
dd if=/dev/zero of=test.file
rm test.file
sudo shutdown now
```

### Windows

```
VBoxManage modifyhd --compact "C:\Users\Shook\VirtualBox VMs\Arch\Arch 2-disk1.vdi"
```

VM options
----------

Use all available logical CPUs (not just physical ones)
