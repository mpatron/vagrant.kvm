# Counter-Strike:Source

curl -OL https://mms.alliedmods.net/mmsdrop/1.11/mmsource-1.11.0-git1155-linux.tar.gz

## OGP Panel installation

[https://www.opengamepanel.org/install_guide/panel.html](https://www.opengamepanel.org/install_guide/panel.html)

Sous vagrant, il y a un utilisateur vagrant d'installer avec le groupe sudo, mais pour réaliser les actions on peut utiliser un autre compte et lui ajouter le groupe sudo :

~~~bash
su -
apt-get install sudo
usermod -aG sudo $USER
~~~

~~~bash
vagrant@node0:~$ whiUsermod=$(which usermod)
vagrant@node0:~$ if [ -z "$whiUsermod" ]; then
vagrant@node0:~$     sudo mkdir -p /root/backup/etc
vagrant@node0:~$     sudo cp /etc/profile /root/backup/etc
vagrant@node0:~$     sudo sed -i 's#PATH=".*"#PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/sbin:/usr/sbin:/usr/local/sbin"#g' /etc/profile
vagrant@node0:~$     source /etc/profile
vagrant@node0:~$ fi
vagrant@node0:~$ sudo apt-get install -yqq apache2 curl subversion php8.2 php8.2-gd php8.2-zip libapache2-mod-php8.2 php8.2-curl php8.2-mysql php8.2-xmlrpc php-pear mariadb-server php8.2-mbstring git php-bcmath
vagrant@node0:~$ sudo sed -i "s/^bind-address.*/bind-address=0.0.0.0/g" "/etc/mysql/mariadb.conf.d/50-server.cnf"
vagrant@node0:~$ sudo mysql_secure_installation <<EOF

y
vagrant
vagrant
y
y
y
y
EOF
~~~

~~~bash
vagrant@node0:~$ wget -N "https://github.com/OpenGamePanel/Easy-Installers/raw/master/Linux/Debian-Ubuntu/ogp-panel-latest.deb" -O "ogp-panel-latest.deb"
vagrant@node0:~$ sudo dpkg -i "ogp-panel-latest.deb"
~~~

~~~bash
Please enter the MySQL root user password so that a MySQL database can be preconfigured for OGP: vagrant

Please use the following database information when prompted during installation:

MySQL Host: localhost
MySQL User: ogpuser
MySQL User Password: JXMAXIO8wPvw
MySQL Database Name: ogp_panel

Please browse to http://localhost/index.php to begin the panel installation wizard.

Operations have been completed successfully.
~~~

~~~bash
vagrant@node0:~$ sudo cat /root/ogp_panel_mysql_info
OGPDBPass=JXMAXIO8wPvw
~~~

## OGP Agent installation

[https://www.opengamepanel.org/install_guide/agent.html](https://www.opengamepanel.org/install_guide/agent.html)

Sous vagrant, il y a un utilisateur vagrant d'installer avec le groupe sudo, mais pour réaliser les actions on peut utiliser un autre compte et lui ajouter le groupe sudo :

~~~bash
su -
apt-get install sudo
usermod -aG sudo $USER
~~~

~~~bash
vagrant@node1:~$ whiUsermod=$(which usermod)
vagrant@node1:~$ if [ -z "$whiUsermod" ]; then
vagrant@node1:~$     sudo mkdir -p /root/backup/etc
vagrant@node1:~$     sudo cp /etc/profile /root/backup/etc
vagrant@node1:~$     sudo sed -i 's#PATH=".*"#PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/sbin:/usr/sbin:/usr/local/sbin"#g' /etc/profile
vagrant@node1:~$     source /etc/profile
vagrant@node1:~$ fi
vagrant@node1:~$ sudo apt-get install -yqq libxml-parser-perl libpath-class-perl perl-modules screen rsync sudo e2fsprogs unzip subversion pure-ftpd libarchive-zip-perl libc6 libgcc1 git curl
vagrant@node1:~$ sudo apt-get install -yqq libc6-i386
vagrant@node1:~$ sudo apt-get install -yqq lib32stdc++6
vagrant@node1:~$ sudo apt-get install -yqq lib32gcc1
vagrant@node1:~$ sudo apt-get install -yqq lib32gcc-s1
vagrant@node1:~$ sudo apt-get install -yqq libhttp-daemon-perl
vagrant@node1:~$ sudo apt-get install -yqq libarchive-extract-perl
vagrant@node1:~$ wget -N "https://github.com/OpenGamePanel/Easy-Installers/raw/master/Linux/Debian-Ubuntu/ogp-agent-latest.deb" -O "ogp-agent-latest.deb"
vagrant@node1:~$ sudo dpkg -i "ogp-agent-latest.deb"
vagrant@node1:~$ sudo cat /root/ogp_user_password
ogpUser=ogp_agent
ogpPass=wn0437rxQJPYUqb
ogpEnc=Db2TZeFs
~~~

## Connect

[http://192.168.56.140/install.php](http://192.168.56.140/install.php)
