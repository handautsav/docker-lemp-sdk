############################################################
# Dockerfile: LEMP Development Environment
#
# LEMP = GNU/Linux + Nginx + MariaDB + PHP
#
############################################################

##############
# Base Image
##############
FROM centos:centos6
#FROM centos:centos6.7

MAINTAINER Utsav Handa <handautsav@gmail.com>

###########################
# Additional Repositories
###########################
RUN rpm -ivh http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
RUN rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
RUN rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
Add etc/yum.repos.d/* /etc/yum.repos.d/

#################
# Base Packages
#################
RUN yum -y install \
    nginx \
    MariaDB-server MariaDB-client \
    redis \
    beanstalkd \
    java elasticsearch \
    php56w php56w-cli php56w-common php56w-fpm php56w-gd php56w-imap php56w-intl php56w-mbstring php56w-mcrypt php56w-mysqlnd php56w-pdo php56w-phpdbg php56w-process php56w-tidy php56w-xml \
    php56w-pear php56w-pecl-redis


RUN echo -e "export JAVA_HOME=/usr/lib/jvm/jre/ \n export PATH=$JAVA_HOME/bin:$PATH" > /etc/profile.d/java_home.sh

#######################
# Additional Packages
#######################
RUN curl https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py | python && easy_install pip 
RUN pip install supervisor && pip install --upgrade Distribute

#################
# Configuration
#################
Add etc/ /etc/
Add var/www/html/ /var/www/html/
RUN cp /usr/share/mysql/my-medium.cnf /etc/my.cnf
RUN echo -e "mysqld_safe & \n mysqladmin --silent --wait=30 ping || exit 1 \n mysql -e 'GRANT ALL PRIVILEGES ON *.* TO \"root\"@\"%\" WITH GRANT OPTION;'" >> /tmp/mariadb-config
RUN bash /tmp/mariadb-config && rm -f /tmp/mariadb-config

# Modify supervisor's require.txt
#RUN sed -i "s/meld3 >= 0.6.5/#meld3 >= 0.6.5/g" /usr/lib/python2.6/site-packages/supervisor-3.1.3-py2.6.egg-info/requires.txt

###########
# Volumes
###########
VOLUME ["/var/lib/mysql", "/var/www/html", "/usr/share/elasticsearch/data"]

#########
# Ports
#########
EXPOSE 80 443 3306 6379 11300 9200 9300

##############
# Entrypoint
##############
ENTRYPOINT ["/usr/bin/supervisord", "-n","-c","/etc/supervisord.conf"]
