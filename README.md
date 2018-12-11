# How to install nuBuilder4 in an LXD container

This is a how-to install NuBuilder4 in an LXD Container on an Ubuntu 18.04 LXD "host" (whether that
host is a Server/Desktop/VM).

See:  https://www.nubuilder.com/  and the User Guide:  https://www.nubuilder.com/storage/pdf/nuBuilderForte_UserGuide.pdf


nuBuilder Forte is the 4th version of nuBuilder.

> A browser-based tool created by nuSoftware for developing web-based database applications.  
>  
> nuBuilder uses either MySQL or Maria DB databases and gives its users the ability to do database operations like...  
>  
>    Search  
>    Create  
>    Insert  
>    Read  
>    Update  
>    Delete  
>  
> With low-code tools that...  
>  
>    Drag and Drop objects.  
>    Create database queries with the SQL Builder.  
>    Create customised date and number formats with the Format Builder.  
>    Create calculated fields with the Formula Builder.  
>    Create Fast Forms.  
>    Create Fast Reports.  
>  
> And further customisation that can be done with...  
>  
>    Javascript  
>    PHP  


The benefit of an LXD container is that you can easily copy/clone the "nubuilder" container or migrate the
container to another LXD "host" locally, remotely or on a Cloud server.   If you use LXD with ZFS/BTRFS
(LXD will create & use a ZFS/BTRFS loop device if your file system isn't already ZFS/BTRS) you can
also utilize snapshot/restore etc for your NuBuilder4 LXD installation.  

NOTE:  LXD also supports various file systems including CEPH, BTRFS, ZFS, LVM, and plain
            directory-based EXF4 file systems.   LXD also can create/manage other Distro's in
            LXD containers including Fedora, CentOS, Debian, Alpine, Oracle, Suse etc.

Anyway, I put these together because I just couldn't find any real guide on either Youtube, the web, or
on the NuBuilder website with step-by-step instructions and had seen a lot of posts on the NuBuilder
Forum asking how to install it.  

The answer's always seemed to just point to the minimally informational instructions in the "setup" section
of the Website which if you are experienced enough in all the basic components (apache2, mysql, PHP etc
you could probably figure out even the missing informational pieces quickly.

As I worked on this I kept finding more and more pieces of important information related to how to install
NuBuilder4 successfully that just were never collated into one place/document.  So this is what I put
together.   Note:  I am now using Nubuilder4 in LXD containers running on local & remote (cloud servers).  So
far things look like they are working as they are supposed to.

Brian

\============================================================================================  
\==={ **To Install NuBuilder4 in an LXD container** }======================================================  
\============================================================================================  

First in an Ubuntu Server/Desktop/VM .. create an LXD ubuntu container named "nubuilder"

    lxc launch ubuntu:b nubuilder  

Then access the "nubuilder" container which logs you in as root and leaves you at a Bash prompt:

    lxc exec nubuilder bash  

Install required software for NuBuilder4:

     # apt install mysql-server apache2 php php-mysql unzip libapache2-mod-php php-mbstring -y  

From this point on everything is basically the same as the above instructions for installing NuBuilder4 in a
Server/Desktop/VM...

Make sure the mysql "root" password is set to use "passwords" and not "auth-socket".
You can do this by following Step 3 — (Optional) Adjusting User Authentication and Privileges

https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04

    # cd /var/www/html  

    # wget https://github.com/nuSoftware/nuBuilder4/archive/master.zip  

    # unzip ./master.zip  

Create an empty Database

    # mv ./nuBuilder4-master/* .  

    # rm ./index.html  

    # myql -u root -p  

    mysql> CREATE DATABASE nubuilder4;
    Query OK, 1 row affected (0.00 sec)

    mysql> quit
    Bye

Copy the nubuilder4.sql schema into that empty database

    # mysql -u root -p nubuilder4 < ./nubuilder4.sql  

Add the mysql root password to the nuconfig.conf file

    # nano nuconfig.php  

Make sure to open Ports 80 & maybe 8080 for html access to the container

    # ufw allow 80  
    # ufw allow 8080  

To make sure that the nubuilder4 setup tab is present when logging in as "globeadmin"
you need to edit my.cnf and add some config statements

     # nano /etc/alternatives/my.cnf

Then append/add...

    [client]
    port=3306
    socket=/tmp/mysql.sock

    [mysqld]
    port=3306
    socket=/tmp/mysql.sock
    key_buffer_size=16M
    max_allowed_packet=8M
    sql-mode=NO_ENGINE_SUBSTITUTION

    [mysqldump]
    quick

\==={ Reboot your LXD Container then Access/Use NuBuilder4 }===============================================  

Whether you installed NuBuilder4 in LXD in a VM/Desktop/or Server the setup should be complete so  
now reboot the LXD container (or your VM, Server etc to verify everything comes back up and you can  
now access NuBuilder4

shutdown -r now  

then...  
point your web browser to: http://<IP_of_Container>

and login as: globeadmin  
password: nu  
