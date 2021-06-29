# Network
Start listening on a specified IP/port:
```sh
nc -v -ln 10.0.31.59 3333
```
Result:
```sh
ubuntu@ip-10-0-31-59:~$ netstat -tlpn
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 10.0.31.59:3333         0.0.0.0:*               LISTEN      1517/nc
```
Start listening a port (connections to all IPs):
```sh
nc -v -ln 3333
```
Result:
```sh
ubuntu@ip-10-0-31-59:~$ netstat -tlpn
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:3333            0.0.0.0:*               LISTEN      1391/nc
```

Connect to a remote IP/port:
```sh
nc -zvw10 10.0.19.226 6789
```

# Create a cron job

```sh
sudo crontab -e
```
[Spec and examples](https://www.tutorialspoint.com/unix_commands/crontab.htm)

## Some examples

To run /usr/bin/sample.sh at 12.59 every day and supress the output:
```sh
59 12 * * * simon /usr/bin/sample.sh > /dev/null 2>&1
```
To run sample.sh every Tuesday to Saturday at 1am (01:00)
```sh
0 1 * * 2-7 sample.sh 1>/dev/null 2>&1
```
To run sample.sh every 10 minutes.
```sh
*/10 * * * * sample.sh
```

Other samples:
```sh
\* * * * * sudo tar -cpzf /backup/backup-`date +\%Y\%m\%d\%H\%M\%S`.tar.gz /var/www/
```

# Working with block devices

```sh
# list all partition and their sizes on the specified device. 
# Units: sectors
sfdisk /dev/sdb

# same in json
sfdisk -J /dev/sdb

# create a partition starting from 5631901696B and ending at 6143606784B
parted -s /dev/sdb mkpart logical ext4 5631901696B 6143606784B 

# same create, but with naming. Note, one should know the partition number, being created. Better way would be to run it in 2 separate commands:
parted -s /dev/sdb mkpart logical ext4 5631901696B 6143606784B name 11 future05-3

# list all partition and their sizes on the specified device
# Units: B, MB, GB, etc; Note: the byte conversion into Mb (and Gb accordingly) is incorrect as they just divide by 1000 and round rather than dividing by 1024
# So, the 5632MB printed by this command is actually 5631901696B or 5371MB (not 5632MB)
parted /dev/sdb print 

# remove 11th partition
parted /dev/sdb rm 11
```
