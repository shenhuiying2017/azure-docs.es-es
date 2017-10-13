---
title: "Ejecución de un clúster MariaDB (MySQL) en Azure | Microsoft Docs"
description: "Creación de un clúster MariaDB + Galera MySQL en Azure Virtual Machines"
services: virtual-machines-linux
documentationcenter: 
author: sabbour
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 53e9bf18b26338212411ea7c4f260eb308486738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Clúster MariaDB (MySQL): tutorial de Azure
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Azure Resource Manager](../../../resource-manager-deployment-model.md) y el clásico. Este artículo trata sobre el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Azure Resource Manager.

> [!NOTE]
> El clúster de MariaDB Enterprise está ahora disponible en Azure Marketplace. La nueva oferta implementará automáticamente un clúster MariaDB Galera en Azure Resource Manager. Debe usar la nueva oferta de [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/).
>
>

En este artículo se muestra cómo crear un clúster [Galera](http://galeracluster.com/products/) de varios maestros de [MariaDB](https://mariadb.org/en/about/) (una sustitución robusta, escalable y confiable de MySQL) para trabajar en un entorno de alta disponibilidad en Azure Virtual Machines.

## <a name="architecture-overview"></a>Introducción a la arquitectura
En este artículo se describe cómo completar los pasos siguientes:

- Crear un clúster de tres nodos.
- Separar los discos de datos del disco del sistema operativo.
- Crear los discos de datos en la configuración RAID-0/striped para aumentar la tasa de E/S por segundo.
- Usar Azure Load Balancer para equilibrar la carga de los tres nodos.
- Para minimizar el trabajo repetitivo, cree una imagen de máquina virtual que contenga MariaDB+Galera y úsela para crear otras máquinas virtuales de clúster.

![Arquitectura del sistema](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> En este tema se usan las herramientas de la [CLI de Azure](../../../cli-install-nodejs.md); asegúrese de descargarlas y conectarlas a su suscripción de Azure siguiendo las instrucciones. Si necesita una referencia a los comandos disponibles en la CLI de Azure, consulte la [referencia de comandos de la CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). También necesitará [crear una clave SSH para la autenticación] y anotar la ubicación del archivo .pem.
>
>

## <a name="create-the-template"></a>Creación de la plantilla
### <a name="infrastructure"></a>Infraestructura
1. Cree un grupo de afinidad para mantener juntos los recursos.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Cree una red virtual.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Cree una cuenta de almacenamiento para hospedar todos nuestros discos. No debería colocar más de 40 discos de uso intensivo en la misma cuenta de almacenamiento para evitar llegar al límite de la cuenta de almacenamiento de 20,000 E/S por segundo. En este caso, está muy por debajo de ese límite, por lo que almacenará todo en la misma cuenta para simplificar.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Busque el nombre de la imagen de máquina virtual de CentOS 7.

        azure vm image list | findstr CentOS
   El resultado tendrá un aspecto similar a `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Use ese nombre en el paso siguiente.
5. Cree la plantilla de máquina virtual y reemplace /path/to/key.pem por la ruta de acceso donde almacenó la clave SSH .pem generada.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Conecte cuatro discos de datos de 500 GB a la máquina virtual para su uso en la configuración de RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Use SSH para iniciar sesión en la plantilla de máquina virtual que creó en mariadbhatemplate.cloudapp.net:22 y conéctese con su clave privada.

### <a name="software"></a>Software
1. Obtenga la raíz.

        sudo su

2. Instalar compatibilidad con RAID:

    a. Instale mdadm.

              yum install mdadm

    b. Cree la configuración RAID0/stripe con un sistema de archivos EXT4.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Cree el directorio de punto de montaje.

              mkdir /mnt/data
    d. Recupere el UUID del dispositivo RAID recién creado.

              blkid | grep /dev/md0
    e. Edite /etc/fstab.

              vi /etc/fstab
    f. Agregue el dispositivo para habilitar el montaje automático en el reinicio. Para ello, reemplace el UUID por el valor obtenido con el comando **blkid** anterior.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Monte la partición nueva.

              mount /mnt/data

3. Instale MariaDB.

    a. Cree el archivo MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Rellene el archivo de repositorio con el contenido siguiente:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Para evitar conflictos, quite el postfijo existente y mariadb-libs.

           yum remove postfix mariadb-libs-*
    d. Instale MariaDB con Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Mueva el directorio de datos de MySQL al dispositivo de bloques RAID.

    a. Copie el directorio actual de MySQL en su nueva ubicación y quite el directorio antiguo.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Establezca los permisos del nuevo directorio según corresponda.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Cree un vínculo simbólico que apunte al directorio anterior en la nueva ubicación en la partición RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Dado que [SELinux interfiere con las operaciones del clúster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), es necesario deshabilitarlo para la sesión actual. Edite `/etc/selinux/config` para deshabilitar los reinicios posteriores.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Valide las ejecuciones de MySQL.

   a. Inicie MySQL.

           service mysql start
   b. Proteja la instalación de MySQL, establezca la contraseña raíz, quite los usuarios anónimos para deshabilitar el inicio de sesión raíz remoto y quite la base de datos de prueba.

           mysql_secure_installation
   c. Cree un usuario en la base de datos para las operaciones de clúster y, opcionalmente, para las aplicaciones.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Detenga MySQL.

            service mysql stop
7. Cree un marcador de posición de configuración.

   a. Edite la configuración de MySQL para crear un marcador de posición para la configuración del clúster. En este momento, no reemplace las **`<Variables>`** ni quite la marca de comentario. Eso sucederá después de que cree una máquina virtual desde esta plantilla.

            vi /etc/my.cnf.d/server.cnf
   b. Edite la sección **[galera]** y desactívela.

   c. Edite la sección **[mariadb]**.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Abra los puertos necesarios en el firewall mediante FirewallD en CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Vuelva a cargar el firewall: `firewall-cmd --reload`

9. Optimice el sistema para el rendimiento. Para más información, consulte la [estrategia de optimización del rendimiento](optimize-mysql.md).

   a. Edite nuevamente el archivo de configuración de MySQL.

            vi /etc/my.cnf.d/server.cnf
   b. Edite la sección **[mariadb]** y anexe el contenido siguiente:

   > [!NOTE]
   > Se recomienda que innodb\_buffer\_pool_size sea el 70 % de la memoria de su máquina virtual. En este ejemplo, se estableció en 2.45 GB para la máquina virtual de Azure mediana con 3.5 GB de RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Detenga MySQL, deshabilite el servicio MySQL para que no se ejecute al inicio y, de esta forma, evitar toda interrupción del clúster al agregar un nodo y desaprovisionar la máquina.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Capture la máquina virtual a través del portal. (Actualmente, el [problema #1268 en las herramientas de la CLI de Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) describe el hecho de que las imágenes capturadas por las herramientas de la CLI de Azure no capturan los discos de datos adjuntos).

    a. Apague la máquina mediante el portal.

    b. Haga clic en **Capturar** y especifique el nombre de la imagen como **mariadb-galera-image**. Proporcione una descripción y seleccione "He ejecutado waagent".
      
      ![Captura de la máquina virtual](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Creación de clústeres
Cree tres máquinas virtuales con la plantilla que creó y, luego, configure e inicie el clúster.

1. Cree la primera máquina virtual de CentOS 7 desde la imagen mariadb-galera-image que creó y proporcione la información siguiente:

 - Nombre de la red virtual: mariadbvnet
 - Subred: mariadb
 - Tamaño de la máquina: mediana
 - Nombre del servicio en la nube: mariadbha (o el nombre que desee al que se obtenga acceso desde mariadbha.cloudapp.net)
 - Nombre de la máquina: mariadb1
 - Nombre del usuario: azureuser
 - Acceso de SSH: habilitado
 - Pase el archivo .pem del certificado SSH y reemplace /path/to/key.pem por la ruta de acceso donde almacenó la clave SSH .pem generada.

   > [!NOTE]
   > Los comandos siguientes se dividen en varias líneas para mayor claridad, pero debe especificar cada uno de ellos como una sola línea.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Cree dos máquinas virtuales; para ello, conéctelas al servicio en la nube mariadbha. Cambie el nombre de la máquina virtual y el puerto SSH por un puerto único que no tenga conflictos con otras máquinas virtuales en el mismo servicio en la nube.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  Para MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Necesitará obtener la dirección IP interna de cada una de las tres máquinas virtuales para el siguiente paso:

    ![Obtención de la dirección IP](./media/mariadb-mysql-cluster/IP.png)
4. Use SSH para iniciar sesión en las tres máquinas virtuales y edite el archivo de configuración de cada una de ellas.

        sudo vi /etc/my.cnf.d/server.cnf

    Quite la marca de comentario **`wsrep_cluster_name`** y **`wsrep_cluster_address`**; para ello, quite el símbolo **#** que se encuentra al comienzo de la línea.
    Además, reemplace **`<ServerIP>`** en **`wsrep_node_address`** y **`<NodeName>`** en **`wsrep_node_name`** por la dirección IP y el nombre, respectivamente, de las máquinas virtuales, y quite las marcas de comentario de esas líneas también.
5. Inicie el clúster en MariaDB1 y deje que se ejecute en el inicio.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Inicie MySQL en MariaDB2 y MariaDB3 y deje que se ejecute en el inicio.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Equilibro de carga del clúster
Al crear las máquinas virtuales en clúster, las agregó al conjunto de disponibilidad denominado clusteravset para asegurarse de que se colocaron en diferentes dominios de error y de actualización, y que Azure nunca realizará el mantenimiento en todos los equipos a la vez. Esta configuración cumple los requisitos para ser compatible por el Acuerdo de Nivel de Servicio (SLA) de Azure.

Ahora use Azure Load Balancer para equilibrar las solicitudes entre los tres nodos.

Ejecute los comandos siguientes en la máquina con la CLI de Azure.

La estructura de los parámetros de comando es: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

La CLI establece el intervalo de sondeo del equilibrador de carga en 15 segundos, lo que puede resultar demasiado largo. Cámbielo en el portal en **Puntos de conexión** para cualquiera de las máquinas virtuales.

![Edición del extremo](./media/mariadb-mysql-cluster/Endpoint.PNG)

Seleccione **Volver a configurar el conjunto de carga equilibrada**.

![Volver a configurar el conjunto de carga equilibrada](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Cambie **Intervalo de sondeo** a 5 segundos y guarde los cambios.

![Cambio del intervalo de sondeo](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Validación del clúster
El trabajo más duro ya está terminado. Ahora, el clúster debe ser accesible en `mariadbha.cloudapp.net:3306`, que alcanzará al equilibrador de carga y enrutará las solicitudes entre las tres máquinas virtuales de forma fluida y eficaz.

Use el cliente de MySQL de su preferencia para conectarse o conéctese directamente desde una de las máquinas virtuales para comprobar que funciona este clúster.

     mysql -u cluster -h mariadbha.cloudapp.net -p

A continuación, cree una base de datos y rellénela con algunos datos.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

La base de datos que creó devuelve la siguiente tabla:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
En este artículo, creó un clúster MariaDB+Galera de alta disponibilidad de tres nodos en Azure Virtual Machines con CentOS 7. Las máquinas virtuales tienen equilibro de carga con Azure Load Balancer.

Puede que desee echar un vistazo a [otro modo para el clúster MySQL en Linux](mysql-cluster.md) y ver otras formas de [optimizar y probar el rendimiento de MySQL en máquinas virtuales Linux con Azure](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[crear una clave SSH para la autenticación]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
