# ij tool
## Installation
### Install Derby tools

Following steps are based off of [this](https://db.apache.org/derby/papers/DerbyTut/install_software.html) tutorial. In this Example we use db-derby-10.14.2.0-bin, which can be downloaded [here](https://mirror.nodesdirect.com/apache//db/derby/db-derby-10.14.2.0/db-derby-10.14.2.0-bin.tar.gz).
Check for other versions [here](http://db.apache.org/derby/derby_downloads.html)
```sh
wget https://mirror.nodesdirect.com/apache//db/derby/db-derby-10.14.2.0/db-derby-10.14.2.0-bin.tar.gz
tar xzvf db-derby-10.14.2.0-bin.tar.gz
export DERBY_INSTALL=/opt/Apache/db-derby-10.14.2.0-bin
export CLASSPATH=$DERBY_INSTALL/lib/derby.jar:$DERBY_INSTALL/lib/derbytools.jar:$DERBY_INSTALL/lib/derbyoptionaltools.jar:$DERBY_INSTALL/lib/derbyshared.jar:.
cd $DERBY_INSTALL/bin
export DERBY_HOME=/opt/Apache/db-derby-10.14.2.0-bin
. setEmbeddedCP
```
### Verify Derby
```sh
java org.apache.derby.tools.sysinfo
```

### Using ij tool
More info can be found [here](https://db.apache.org/derby/docs/10.14/tools/derbytools.pdf) and [here](https://db.apache.org/derby/papers/DerbyTut/ij_intro.html)
```sh
java org.apache.derby.tools.ij
ij> connect 'jdbc:derby:/usr/share/mytest.db';
ij> show tables;
ij> select * from mytable.devices;
```
