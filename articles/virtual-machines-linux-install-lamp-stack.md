<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Instalación de la pila LAMP en una máquina virtual con Linux en Azure
=====================================================================

Una pila LAMP consta de los siguientes elementos diferentes:

-   **L**inux - Sistema operativo
-   **A**pache - Servidor web
-   **M**ySQL - Servidor de base de datos
-   **P**HP - Lenguaje de programación

Instalación en Ubuntu
---------------------

Necesitará los siguientes paquetes instalados:

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`
-   `libapache2-mod-auth-mysql`
-   `libapache2-mod-php5`
-   `php5-xsl`
-   `php5-gd`
-   `php-pear`

Puede ejecutar esto como un solo comando `apt-get install`:

    apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear

Instalación en CentOS
---------------------

Necesitará los siguientes paquetes instalados:

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

Puede ejecutar esto como un solo comando `yum install`:

    yum install httpd mysql mysql-server php-php-mysql

Instalación
-----------

1.  Instale **Apache**.

    1.  Necesitará reiniciar el servidor web Apache. Ejecute el siguiente comando:

             sudo /etc/init.d/apache2 restart

    2.  Compruebe que la instalación se está ejecutando. Dirija el explorador a: <http://localhost>. Debe decir "It works!".

2.  Instale **MySQL**.
    1.  Establezca la contraseña raíz para mysql mediante la ejecución del siguiente comando:

             mysqladmin -u root -p password yourpassword

    2.  Inicie sesión en la consola usando `mysql` o una variedad de clientes MySQL.

3.  Instale **PHP**.

    1.  Habilite el módulo PHP de Apache con la ejecución del siguiente comando:

             sudo a2enmod php5

    2.  Vuelva a iniciar Apache con la ejecución del siguiente comando:

             sudo service apache2 restart

Lecturas adicionales
--------------------

Existen muchos recursos para instalar una pila LAMP en Ubuntu.

-   <https://help.ubuntu.com/community/ApacheMySQLPHP>
-   <http://fedorasolved.org/server-solutions/lamp-stack>

