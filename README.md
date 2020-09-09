# django nginx server
Integrating django app with nginx

```
yum update && yum update wget
```
		
## 1. Install Python35	

```
$ yum install zlib-devel-y
$ yum install xz
$ yum install openssl openssl-devel-y
$ wget https://www.python.org/ftp/python/3.5.1/Python-3.5.1.tar.xz
$ xz -d Python-3.5.1.tar.xz
$ tar -xvf Python-3.5.1.tar
$ cd Python<F4-3.5.1.tar.xz
$ ./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
$ make && make altinstall
$ python3.5 --version
```
		
## 2. Install pip3		
		
```
$ curl -k -O https://bootstrap.pypa.io/get-pip.py
$ python3.5 get-pip.py
```
		
## 3. Install django		
		
```
$ pip3 install Django==2.2.16
$ python3.5
> import django
> print(django.get_version()) 
```
		
## 4. Install MariaDB		
		
```
$ /etc/yum.repo.d/
$ vi MariaDB.repo

# You can get this file the command: 
# $ curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
# And this command make a file named 'mariadb.repo' in /etc/yum.repo.d/
# So Change this file name 'mariadb.repo' to 'MariaDB.repo' if you need 
# But this command allowed to download version 10.5 that is not supported in my os version
# Otherwise, you enter the url: 
# 'https://downloads.mariadb.org/mariadb/repositories/#distro=CentOS&distro_release=centos6-amd64--centos6&mirror=harukasan&version=10.3'
# and choose a version that fits your OS version and specification

# MariaDB 10.3 CentOS repository list - created 2020-09-04 02:11 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.3/centos6-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

[mariadb-maxscale]
# To use the latest stable release of MaxScale, use "latest" as the version
# To use the latest beta (or stable if no current beta) release of MaxScale, use "beta" as the version
name = MariaDB MaxScale
baseurl = https://downloads.mariadb.com/MaxScale/2.4/centos/$releasever/$basearch
gpgkey = file:///etc/pki/rpm-gpg/MariaDB-MaxScale-GPG-KEY
gpgcheck = 1
enabled = 1

[mariadb-tools]
name = MariaDB Tools
baseurl = https://downloads.mariadb.com/Tools/rhel/$releasever/$basearch
gpgkey = file:///etc/pki/rpm-gpg/MariaDB-Enterprise-GPG-KEY
gpgcheck = 1
enabled = 1
# You have to write this repository before you install MariaDB
# This repo is about MariaDB's version and tools

# for [Errono 14]
$ mv mariadb.repo mariadb.repo.original
$ yum clean all
$ rm -rf /ver/cache/yum/*

# Delete mysql-server to avoid crash
$ rpm -qa | grep -i '^mysql-'
# If you find 'mysql-sever-(numbers)'
$ rpm --nodeps -ev mysql-server-(numbers)'

$ yum install MariaDB-server
$ yum install mysql

$ /etc/init.d/mysql start
$ /usr/bin/mysqladmin -u root password 'passwd'
$ netstat -anp | grep 3306
# check mysql service
$ mysql -u root -p
> Enter password: 
# Set auto start on boot: this command is different from centos7
$ chkconfig mysql on
$ chkconfig --list mysql
# mysql           0:off   1:off   2:on    3:on    4:on    5:on    6:off
$ service mysql status
# SUCCESS! MariaDB running (17356)
$ service mysql enable
$ mysql_secure_installation
> Enter current password for root (enter for none):
> OK, successfully used password, moving on...

> Setting the root password ensures that nobody can log into the MariaDB
> root user without the proper authorisation.

> You already have a root password set, so you can safely answer 'n'.

> Change the root password? [Y/n] n
> Remove anonymous users? [Y/n] Y
> Disallow root login remotely? [Y/n] Y
> Remove test database and access to it? [Y/n] Y
> Reload privilege tables now? [Y/n] Y
```
		
## 5. Install mysqlclient		
		
```
$ yum search python35 | grep devel
# Download python35 + devel
$ yum install  rh-python35-python-devel.x86_64  rh-python35-scldevel.x86_64
# I found that the problem is that mysqlclient requires mysql-devel packages, which is different from mariadb-devel. Don't install mariadb-devel!
# So you need to:
# Remove MariaDB-devel which is installed when we install MariaDB-server
$ yum list installed | grep mariadb
$ sudo yum erase MariaDB-devel.x84_64

# Add MySQL repository in yum
# Go to https://dev.mysql.com/downloads/repo/yum/ and select the RPM file for your CentOS 
# (for me, I choose "Red Hat Enterprise Linux 6 / Oracle Linux 6 (Architecture Independent), RPM Package"
# Go to you terminal and type:
$ wget  wget https://dev.mysql.com/get/mysql80-community-release-el6-3.noarch.rpm
$ sudo rpm -Uvh sudo rpm -Uvh mysql80-community-release-el6-3.noarch.rpm
$ sudo yum install mysql-community-devel.x86_64
$ sudo pip install mysqlclient
```
		
## 6. Set mysql		
		
```
$ mysql -u root -p
> Enter password: 
> create database db_name
> grant all privileges on db_name.* to user_name@localhost identified by 'password';
> grant all privileges on db_name.* to user_name@'%' identified by 'password';
> flush privileges;
> exit
```
		
## 7. Install nginx		
		
```
$ vi /etc/yum.repo.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

$ yum info nginx
$ yum install nginx
# Set auto start on boot: this command is different from centos7
$ chkconfig nginx on #systemctl enabl nginx 
$ service nginx restart # systemctl restart nginx
```
		
## 8. Install uwsgi			
		
```
$ pip3 install uwsgi
```
		
## 9. Set user&group				
			
```
$ groupadd group_name
$ grep group_name /etc/group

$ useradd -g group_name -s /sbin/nologin -p 'password' user_name
$ echo 'password' passwd --stdln user_name
$ cat /etc/passwd
```
			
## 10. make django project		
		
```
$ cd /home/user_name
$ mkdir main_project
$ cd main_proejct
$ pip3 install virtualenv
$ virtualenv myvenv
$ source myvenv/bin/activate
$ mkdir dir1 dir2
$ cd dir1
$ django-admin.py startproejct sample
$ cd dir1/sample/sample
$ vi settings.py
ALLOWED_HOSTS = ['your_domain', 'your_ip']
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'db_name',
        'USER': 'user_name',
        'PASSWORD': 'password',
        'HOST': '',
        'PORT': '',
    }
}
STATIC_URL = '/static/'
STATIC_ROOT = "/var/www/your_domain/static/"

MEDIA_URL = '/media/'
MEDIA_ROOT = "/var/www/you_domain/media/"
```
		
## 11. Set nginx		
	
```
$ cd /etc/nginx/conf.d/nginx.conf
$ mv default.conf default.conf.ogirinal
$ vi naignx.conf
upstream django {
   server unix:/home/user_dir/main_dir/dir2/uwsgi.sock;
}
server {
        listen 80;
        server_name your_domain_or_ip;
        charset utf-8;
        # location = /favicon.ico { access_log off; log_not_found off; }
        location /static/ {
                alias /var/www/your_domain_or_ip/static/;
		# alias /your_project_folder/static/;
        }
        location / {
            include /home/user_dir/main_dir/dir1/proejct_dir/uwsgi_params;
            uwsgi_pass      django;
        }
}
```
		
## 12. Set uwsgi		
		
There are two modes in `uwsgi`: Emperor mode and Default(General) mode	    		
The Emperor mode is to run server as a whole computer is one server   
Contrary, The Default(General) mode is to run server as django applications are each server	 	
The uWSGI program(both modes) also supports the UDS method		 	
	
Emperor mode		
-------------		
	
```
$ /etc/re.d/init.d/uwsgi
$ mkdir -p /etc/uwsgi/vassals
$ sudo vi etc/uwsgi/vassals/uwsgi.ini
[uwsgi]
uid = user_name
base = /home/%(uid)/main_dir
root /home/%(uid)/main_dir/dir1/project_dir
home = %(base)/myvenv
chdir = %(base)/dir1/proejct_dir
uwsgi-file = %(chdir)/proejct_dir/wsgi.py
module = sample.wsgi:application
env = DJANGO_SETTINGS_MODULE=sample.settings
callable = application
master = True
processes = 5
socket = %(base)/dir2/uwsgi.sock
;for UDS(Unix Domain Socket)
;Default mode also can use UDS 
chmod-socket = 666
vacuum = True
pidfile = /tmp/emperor.pid
deamonize = /home/%(uid)/main_dir/dir2/emperor.log
```
This is Emperor mode		
If you need to deploy a big number of apps on a single server, or a group of servers, the Emperor mode is just the ticket.	 	
Start command: sudo uwsgi --emperor /etc/wusgi/vassals --uid user_name gid group_name		 
If you have emperor_wusgi.ini file,		 
Start command: sudo nginx -s reload && sudo uwsgi --emperor /etc/uwsgi/vassals --uid user_name --gid group_name		 		
  
If you feel like Start command's options are too many and command is too long, tnen you can make *.ini file about Emperor process		
```
cd /etc/uwsgi
vi uwsgi_emperor.ini
[uwsgi]
emperor = /etc/uwsgi/vassals
uid = user_name
gid = group_name
master =True
pidfile = /tmp/emperor.pid
vacuum = Ture
daemonize = /var/log/uwsgi/emperor.log
```
Start command: sudo nginx -s reload && sudo uwsgi --ini /etc/uwsgi/uwsgi_emperor.ini	 	
Stop command: sudo nginx -s stop && sudo uwsgi --stop /tmp/emperor.pid		 	
			
General(Default) mode		
---------------------
		
````
$ cd /home/user_name/main_project/dir2/
$ vi uwsgi.ini
[uwsgi]
uid = user_name
base = /home/%(uid)/main_dir
root /home/user_name/main_dir/dir1/project_dir
home = %(base)/myvenv
chdir = %(base)/dir1/proejct_dir
uwsgi-file = %(chdir)/app_dir/wsgi.py
module = app_dir.wsgi:application
env = DJANGO_SETTINGS_MODULE=app_dir.settings
callable = application
master = true
processes = 5
pidfile=/tmp/sample_master.pid
socket = %(base)/dir2/uwsgi.sock
chmod-socket = 666
vacuum = true
deamonize = /home/user_dir/main_dir/dir2/sample.log
;This is uwsgi default mode not emperor
;It is possible to create and run config files with general user accounts
;Start or Stop commands are supposed to be executed in project directory
;Start command: uwsgi --ini dir2/uwsgi.ini
;Stop command: uwsgi --stop /tmp/sample_master.pid
```
		
## 13. sftp		
	
		
```
$ vi /etc/ssh/sshd_config
```

```
# Chagne or type

#Subsystem sftp /usr/libexec/opnssh/sftp-server  
Subsystem sftp internal -sftp

Match Group group_name
    X11Forwarding no
    AllowTcpForwarding no
    ForceCommand internal-sftp
    ChrootDirectory /home/user_dir
Match User user_name
    PasswordAuthentication yes
    ChrootDirectory /home/user_dir
```

```
$ chown -R user_name.group_name /home/usr_name
$ chown root.group_name /home/user_name
$ chmod -R 755 /home/user_name
$ service sshd start
$ cd /home
$ ls -al
drwxr-xr-x   3 root group 4096 Sep  4 14:45 user_name(directory)
```


