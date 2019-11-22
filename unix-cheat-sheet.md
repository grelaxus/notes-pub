# Create a cron job

```sh
sudo crontab -e
```
\* * * * * sudo tar -cpzf /backup/backup-`date +\%Y\%m\%d\%H\%M\%S`.tar.gz /var/www/

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
