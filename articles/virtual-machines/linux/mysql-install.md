---
title: "Configuración de MySQL en una VM de Linux en Azure | Microsoft Docs"
description: "Aprenda a instalar la pila de MySQL en una máquina virtual de Linux (sistema operativo de la familia Ubuntu o RedHat) en Azure"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0ee70bda954cf0a193d43b5b47702e7b2c37844d
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-install-mysql-on-azure"></a>Instalación de MySQL en Azure
En este artículo aprenderá a instalar y configurar MySQL en una máquina virtual de Azure con Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Instalación de MySQL en la máquina virtual
> [!NOTE]
> Debe tener una máquina virtual de Microsoft Azure en la que se ejecuta Linux para completar este tutorial. Antes de continuar, consulte el [tutorial sobre máquinas virtuales Linux de Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para crear y configurar una máquina virtual Linux con `mysqlnode` como nombre de máquina virtual y `azureuser` como usuario.
> 
> 

En este caso, use el puerto 3306 como puerto MySQL.  

Conéctese a la máquina virtual Linux que creó mediante putty. Si es la primera vez que usa una máquina virtual Linux de Azure, vea cómo usar putty para conectarse a una máquina virtual Linux [aquí](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Usaremos el paquete de repositorio para instalar MySQL5.6 como un ejemplo de este artículo. En realidad, MySQL5.6 tiene un mejor rendimiento MySQL5.5.  Puede encontrar más información [aquí](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/):

### <a name="how-to-install-mysql56-on-ubuntu"></a>Instalación de MySQL5.6 en Ubuntu
Usaremos aquí la máquina virtual de Linux con Ubuntu de Azure.

* Paso 1: Instalación del modificador de MySQL Server 5.6 en el usuario `root`:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Instalación de mysql-server 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Durante la instalación, aparecerá una ventana de diálogo para pedirle que establezca la contraseña raíz de MySQL a continuación, tiene que establecer la contraseña aquí.
  
    ![imagen](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Vuelva a escribir la contraseña para confirmar.

    ![imagen](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Paso 2: Iniciar sesión en el servidor MySQL
  
    Una vez finalizada la instalación del servidor MySQL, el servicio MySQL se inicia automáticamente. Puede iniciar sesión en el servidor MySQL con el usuario `root` .
    Use el siguiente comando para la contraseña de inicio de sesión y la entrada.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Paso 3: Administrar el servicio MySQL en ejecución
  
    (a) Obtener el estado del servicio MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) Iniciar el servicio MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) Detener el servicio MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) Reiniciar el servicio MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Instalación de MySQL en la familia de sistemas operativos Red Hat, como Oracle Linux o CentOS
Aquí usaremos la máquina virtual de Linux con CentOS u Oracle Linux.

* Paso 1: Adición del modificador del repositorio de MySQL Yum al usuario `root`:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Descargue e instale el paquete de la versión de MySQL:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Paso 2: Editar el archivo a continuación para habilitar el repositorio de MySQL para descargar el paquete MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Actualizar cada valor de este archivo como se muestra a continuación:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Paso 3: Instalación de MySQL desde el repositorio de MySQL Install MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Se instalará el paquete RPM MySQL y todos los paquetes relacionados.
* Paso 4: Administrar el servicio MySQL en ejecución
  
    (a) Comprobar el estado del servicio del servidor MySQL:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) Comprobar si el puerto predeterminado de MySQL Server se está ejecutando:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) Iniciar el servidor de MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) Detener el servidor de MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) Establecer MySQL para iniciarse con el arranque del sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Instalación de MySQL en SUSE Linux
Aquí usaremos la máquina virtual de Linux con OpenSUSE.

* Paso 1: Descargar e instalar el servidor MySQL
  
    Cambiar a usuario `root` a través de comando siguiente:  
  
           #sudo su -
  
    Descargar e instalar el paquete MySQL:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Paso 2: Administrar el servicio MySQL en ejecución
  
    (a) Comprobar el estado del servidor de MySQL:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) Comprobar si el puerto predeterminado del servidor de MySQL se está ejecutando:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) Iniciar el servidor de MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) Detener el servidor de MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) Establecer MySQL para iniciarse con el arranque del sistema:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Paso siguiente
Buscar más información de uso y sobre MySQL [aquí](https://www.mysql.com/).


