Linux generic
=============

Misc
----

Reverse proxy is basically a load balancer

Chmod
-----

```bash
chmod +r # adds read permissions (to all)
chmod +w # adds write permissions (to all)
chmod +x # adds execute permissions (to all)
chmod +X # adds execute only to directories (to all)

chmod -x # removes execute permissions (from all)

chmod o+x # adds execute permissions just others
chmod g+x # adds execute permissions just for group members
chmod u+x # adds execute permissions just for the user
chmod a+x # adds execute permissions for all three (same as +x)
```

Grep
----

### Invert search

```bash
grep -v pattern file
```

### Just get filenames

```bash
grep -v pattern file
```

### Recursive search

```bash
grep -R "A"
```

### Case insensitive

```bash
grep -i pattern file
```

btrfs
-----

btrfs RAID1 will write 2 chunk groups/blocks at a time, each one in a different device, while trying make unused/unallocated space equal between devices.

```bash
# Add
sudo btrfs device add /dev/sdb /mnt/shared

# Balance
sudo btrfs balance start -dconvert=raid1c3 -mconvert=raid1c3 /mnt/shared

# Status
sudo btrfs filesystem show
sudo btrfs device usage /mnt
sudo btrfs balance status /mnt

# Scrub

sudo btrfs scrub start /mnt
sudo btrfs scrub status /mnt

# Subvolumes
sudo btrfs subvolume create /mnt/ROOT
sudo btrfs subvolume snapshot /mnt/ROOT /mnt/SNAPSHOTS

# Mount
sudo mount /dev/sdb /mnt
sudo mount -o degraded /dev/sdb /mnt

# Scan disks and inform kernel of detected filesystems
sudo btrfs device scan --all-devices

sudo btrfs filesystem defragment -r -c /mnt/root

# Info on profiles
man mkfs.btrfs
```


Drive maintenance
-----------------

```bash
# Disconnect
echo 1 > /sys/block/sdb/device/delete

# Rewrite drive with zeroes
sudo dd if=/dev/zero of=/dev/sdX bs=512 status=progress

# Rewrite drive with zeroes at a specific location
sudo dd if=/dev/zero of=/dev/sdX bs=512 count=1 seek=2776811256

# Run SMART tests
sudo smartctl -t short /dev/sdX
sudo smartctl -t select,2776811256+20 /dev/sdX
sudo smartctl -t long /dev/sdX
sudo smartctl -t long -C /dev/sdX

# Check SMART results
sudo smartctl -a /dev/sdX | grep -i sector
```


SELinux
-------

Don't disable SELinux. It's there for a reason, ya dingus. Just learn how it works.

One major SELinux concept is that of SELinux context labels you can view them with:

```
ls -lZ
```

Docker is very sensitive to SELinux settings, in particular when you are mounting volumes through to the filesystem.
You can append z and Z to docker-compose or docker mount options.
The z option indicates that the bind mount content is shared among multiple containers.
The Z option indicates that the bind mount content is private and unshared.

From what I've seen, SELinux will deny mounts unless you specify Z, which is the most restrictive.


Btrfs Rescue from live cd
-------------------------

```bash
# Get BTRFS and EFI partitions
lsblk

# Get a fully-functioning chroot session
sudo su
BTRFS_PARTITION=/dev/sda3
EFI_PARTITION=/dev/sda3
mount $BTRFS_PARTITION /mnt/ -t btrfs -o subvol=root
mount $BTRFS_PARTITION /mnt/home -t btrfs -o subvol=home
mount $EFI_PARTITION /mnt/boot/efi
for i in dev dev/pts proc sys run; do mount -B /$i /mnt/$i; done
cp -n /etc/resolv.conf /mnt/etc/
chroot /mnt

# Reinit grub
rm /boot/efi/EFI/fedora/grub.cfg
rm /boot/grub2/grub.cfg
dnf reinstall shim-* grub2-efi-* grub2-common
```


Customize grub timeouts
-----------------------

```bash
vi /etc/default/grub
# "GRUB_SAVEDEFAULT=true"
rm /boot/efi/EFI/fedora/grub.cfg
rm /boot/grub2/grub.cfg
dnf reinstall shim-* grub2-efi-* grub2-common
```


Systemd
-------

### Journal

`journalctl -u <unit>` to see logs for a particular unit

### See unit boot times from boot

```
sudo systemd-analyze blame
```

### Unit manipulation

```bash
systemctl
systemctl start unit
systemctl stop unit
systemctl restart unit
systemctl enable unit
systemctl disable unit
systemctl edit unit  # To override existing service options
systemctl show unit  # To see current service settings
```

### Creating a new unit

- `After` specifies an ordering of units
- `ExecStart` specifies the command to be run
- `Install` specifies what runlevel to associate with; multi-user.target is for a GUI environment

```bash
[Unit]
Description=X Virtual Frame Buffer Service
After=network.target

[Service]
ExecStart=/usr/bin/Xvfb :99 -screen 0 1024x768x24

[Install]
WantedBy=multi-user.target
```

pkill
-----

```
pkill -f 'to kill process matching multiple words'
```


dos2unix
--------

```bash
# Convert all windows line endings to unix line endings
find . -type f -exec dos2unix {} \;
```

Apply patch
-----------

```bash
patch -p1 < baz.patch
```

Benchmark IO
------------

```
fio --name=seqread   --rw=read      --direct=1 --ioengine=libaio --bs=32k --numjobs=16 --size=512m --runtime=600 --group_reporting
fio --name=seqwrite  --rw=write     --direct=1 --ioengine=libaio --bs=32k --numjobs=16 --size=512m --runtime=600 --group_reporting
fio --name=randread  --rw=randread  --direct=1 --ioengine=libaio --bs=32k --numjobs=16 --size=512m --runtime=600 --group_reporting
fio --name=randwrite --rw=randwrite --direct=1 --ioengine=libaio --bs=32k --numjobs=16 --size=512m --runtime=600 --group_reporting
fio --name=randrw    --rw=randrw    --direct=1 --ioengine=libaio --bs=32k --numjobs=16 --size=512m --runtime=600 --group_reporting --rwmixread=90
```

Run test coverage in python
----------------------------

```bash
coverage run tests.py test -n module.target
coverage report --skip-covered --include=target_module/*
coverage html --skip-covered --include=target_module/*
firefox htmlcov/index.html
```

jq
--

### Pretty print JSON in file

```bash
cat ok.json | jq
```

### Read JSON from file, print formatted output to another file

```bash
cat ok.json | jq '.' > out.json
```

### Pretty print from CURL

```bash
curl -u admin:0biIIz4XMMFc http://localhost:15673/api/queues | jq '.' > out.json
```

Rsync
-----

### Rsync entire remote system to local directory

```bash
rsync -aAXv --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found"} pi@192.168.1.110:/ ./raspi
```

DNS
------------------

```bash
# Reverse lookup
dig +short +answer -x 172.16.1.12

# dig, but specify what DNS to use
dig @192.168.4.100 google.com
```

Pacman
------

### Remove orphaned packages

```bash
sudo pacman -Rns $(pacman -Qtdq)
```

### See manually installed packages

```bash
pacman -Qet
```

iptables
--------

```
# Give access to a port
sudo iptables -I INPUT 8 -p tcp -m tcp --dport 8082 -j ACCEPT

# Dump table information
sudo iptables -S
sudo iptables -nL

# Dump table information w/ NAT entries
sudo iptables -nL -t nat
```

fail2ban
--------

```bash
sudo fail2ban-client status sshd
sudo fail2ban-client set sshd unbanip 75.15.249.246
```


firewalld (an iptables successor)
---------------------------------

```
# Query
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all-zones
sudo firewall-cmd --list-all --zone=public
sudo firewall-cmd --list-all --zone=trusted

# Must always reload after any changes (do stop/start if changes aren't reflected)
sudo firewall-cmd --reload
sudo systemctl stop firewalld; sudo pkill -f firewalld; sudo systemctl start firewalld
sudo systemctl status firewalld

# Remove access
sudo firewall-cmd --zone=public --remove-service=https --permanent

# Add access
sudo firewall-cmd --zone=public --add-interface=eth0 --permanent

sudo firewall-cmd --zone=trusted --add-source=172.16.0.0/16
sudo firewall-cmd --zone=trusted --add-source=172.16.0.0/16 --permanent

sudo firewall-cmd --zone=public --add-service=http
sudo firewall-cmd --zone=public --add-service=http --permanent

sudo firewall-cmd --zone=public --add-service=http
sudo firewall-cmd --zone=public --add-service=http --permanent

sudo firewall-cmd --zone=public --add-service=https
sudo firewall-cmd --zone=public --add-service=https --permanent

```

Scan ports via nmap
-------------------
```
# One port
sudo nmap -Pn -p 8080 web1.4cinsights.com

# All ports
sudo nmap -Pn -p- web1.4cinsights.com

# Top ports
sudo nmap -Pn web1.4cinsights.com
```

Loopback filesystems
--------------------

```bash
# FS-in-a-file
mkdir testdir
dd if=/dev/zero of=test bs=1M count=20
sudo losetup -f test
mkfs.ext4 test
mount -o loop /dev/loop0 testdir
```


LVM
---

```
# See physical volume size
pvs

# See logical volume size
lvs

# Create logical volume using 100% of remaining free space
lvcreate -l 100%FREE -n docker-pool some-physical-pool

# Remove a logical volume
lvremove /dev/some-physical-pool/docker-pool

# Reduce the size of a volume
lvreduce --size -500G /dev/cl_r3-r310-1/vol
```


mdadm
-----

Some of the btrfs parity options (RAID 5/6) have some problematic edge case scenarious during unclean power-off.
I originally used mdadm and LVM on a simple btrfs partition to try to work around this problem.
However, it seems like mdadm (software RAID) also have the same problem unless you use a separate journaling device, which I don't.
In the future, it looks like btrfs with RAID 1 or RAID 1c3 (higher copy count) is a simpler option than dealing with LVM and mdadm.

```
mdadm --examine /dev/sda
mdadm -D /dev/md0

mdadm --assemble --scan /dev/md0
mdadm --add /dev/md0 /dev/sda
mdadm --stop /dev/md0

# One-time hack to fix a randomly broken mdadm array
mdadm --assemble /dev/md0 --update=devicesize /dev/sda /dev/sdc /dev/sdd
```


Drive status checks
-------------------

```
hddtemp /dev/sd?
cat /proc/mdstat
smartctl -H /dev/sda
smartctl -H /dev/sdb
smartctl -H /dev/sdc
smartctl -H /dev/sdd
```


Connect two machines behind NAT
-------------------------------

Reverse SSH from the Target PC (htpc) to the middleman:

```
ssh -N -R {PortOnMiddlePC}:localhost:{PortOnTargetPC} {IPofMiddlePC}
ssh -N -R 19999:localhost:22 shook.id
```

Now from the Client PC (any shell) pull the port down from the middleman:

```
ssh -N -L {PortOnClientPC}:localhost:{PortOnMiddlePC} {IPofMiddlePC}
ssh -N -L 19999:localhost:19999 shook.id
```

Now you can ssh the Target PC from the Client PC:

```
ssh localhost -p {PortForwardedFromTargetPC}
ssh localhost -p 19999
```


