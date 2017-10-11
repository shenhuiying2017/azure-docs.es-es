---
title: "Creación de clústeres de MySQL con conjuntos con equilibrio de carga | Microsoft Docs"
description: "Configuración de un clúster MySQL Linux de carga equilibrada y alta disponibilidad creado con el modelo de implementación clásica en Azure"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: 4eaf86c9ac3e4dc2b51b88383626eda774cab0e9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Uso de conjuntos de carga equilibrada para crear clústeres en MySQL en Linux
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Azure Resource Manager](../../../resource-manager-deployment-model.md) y el modelo clásico. Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Hay disponible una [plantilla de Resource Manager](https://azure.microsoft.com/documentation/templates/mysql-replication/) si necesita implementar un clúster de MySQL.

Este artículo explora e ilustra los diferentes enfoques disponibles para implementar servicios basados en Linux altamente disponibles en Microsoft Azure, explorando la alta disponibilidad de MySQL Server como base. En [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)hay un vídeo disponible que ilustra este enfoque.

Describiremos una solución de alta disponibilidad de MySQL de un solo maestro y dos nodos independientes basada en DRBD, Corosync y Pacemaker. Solamente se ejecuta un nodo MySQL en cada momento. La lectura y escritura desde el recurso DRBD también se limitan a un solo nodo en cada momento.

No se necesita una solución VIP como LVS porque usará conjuntos de carga equilibrada de Microsoft Azure para proporcionar tanto funcionalidad round robin como detección, eliminación y recuperación estable de puntos de conexión de VIP. VIP es una dirección IPv4 globalmente enrutable asignada por Microsoft Azure cuando crea el servicio en la nube.

Hay otras posibles arquitecturas para MySQL, como, por ejemplo, NBD Cluster, Percona, Galera y diversas soluciones middleware que incluyen, al menos, una disponible como máquina virtual en [VM Depot](http://vmdepot.msopentech.com). Mientras que estas soluciones pueden replicarse en unidifusión frente a multidifusión o difusión, y no se basan en almacenamiento compartido o interfaces de varias redes, los escenarios deben ser fáciles de implementar en Microsoft Azure.

Estas arquitecturas de clúster se pueden extender a otros productos como PostgreSQL y OpenLDAP de forma similar. Por ejemplo, este procedimiento de equilibro de carga independiente se probó correctamente con OpenLDAP multimaestro y puede verlo en nuestro blog de Channel 9.

## <a name="get-ready"></a>Prepárese
Necesita los siguientes recursos y capacidades:

  - Una cuenta de Microsoft Azure con una suscripción válida que permita crear al menos dos VM (en este ejemplo se usó XS)
  - Una red y una subred
  - Un grupo de afinidad
  - Un conjunto de disponibilidad
  - La capacidad para crear VHD en la misma región que el servicio en la nube y adjuntarlos a las VM de Linux

### <a name="tested-environment"></a>Entorno probado
* Ubuntu 13.10
  * DRBD
  * MySQL Server
  * Corosync y Pacemaker

### <a name="affinity-group"></a>Grupo de afinidad
Cree un grupo de afinidad para la solución. Para ello, inicie sesión en el portal de Azure clásico, seleccione **Configuración** y cree el grupo de afinidad. Los recursos asignados creados más tarde se asignarán a este grupo de afinidad.

### <a name="networks"></a>Redes
Se crea una nueva red y, a su vez, se crea una subred dentro de la red. En este ejemplo se usa una red 10.10.10.0/24 con una sola subred /24 dentro.

### <a name="virtual-machines"></a>Máquinas virtuales
La primera VM Ubuntu 13.10 se crea usando una imagen de la galería de Ubuntu refrendada llamada `hadb01`. En el proceso, se crea un nuevo servicio en la nube llamado hadb. El nombre refleja la naturaleza compartida y de carga equilibrada que tendrá el servicio cuando agreguemos más recursos. La creación de `hadb01` no presenta ningún problema y se completa mediante el portal. Se crea un punto de conexión para SSH automáticamente y se selecciona la nueva red. Ahora puede crear un conjunto de disponibilidad para las VM.

Tras crear la primera VM (técnicamente, cuando se crea el servicio en la nube), creamos la segunda máquina virtual, `hadb02`. Para la segunda VM, usaremos la VM Ubuntu 13.10 desde la galería a través del Portal, pero usaremos un servicio en la nube existente, `hadb.cloudapp.net`, en lugar de crear uno nuevo. La red y el conjunto de disponibilidad se deben seleccionar automáticamente. También se creará un extremo SSH.

Una vez creadas ambas VM, tomaremos nota del puerto SSH para `hadb01` (TCP 22) y `hadb02` (automáticamente asignado por Azure).

### <a name="attached-storage"></a>Almacenamiento acoplado
Acoplamos un nuevo disco a ambas VM y creamos discos de 5 GB en el proceso. Los discos se hospedan en el contenedor VHD en uso para nuestros discos del sistema operativo principal. Una vez creados y acoplados los discos, no es necesario que reiniciemos Linux, ya que el kernel verá el nuevo dispositivo. Este dispositivo suele ser `/dev/sdc`. Compruebe el resultado en `dmesg`.

En cada VM crearemos una partición mediante `cfdisk` (partición de Linux primaria) y escribiremos la nueva tabla de particiones. No cree un sistema de archivos en esta partición.

## <a name="set-up-the-cluster"></a>Configuración del clúster
Use APT para instalar Corosync, Pacemaker y DRBD en ambas VM de Ubuntu. Para hacerlo con `apt-get`, ejecute el siguiente código:

    sudo apt-get install corosync pacemaker drbd8-utils.

No instale MySQL en este momento. Los scripts de instalación de Debian y Ubuntu inicializarán un directorio de datos de MySQL en `/var/lib/mysql`, pero dado que el directorio se sustituirá por un sistema de archivos DRBD, necesita instalar MySQL más tarde.

Compruebe (con `/sbin/ifconfig`) que ambas VM usan las direcciones de la subred 10.10.10.0/24 y que pueden ejecutar el comando ping entre ellas por nombre. También puede usar `ssh-keygen` y `ssh-copy-id` para asegurarse de que ambas VM pueden comunicarse a través de SSH sin necesidad de contraseña.

### <a name="set-up-drbd"></a>Configure DRBD
Cree un recurso DRBD que usa la partición `/dev/sdc1` subyacente para generar un recurso `/dev/drbd1` que puede asumir el formato ext3 y usarse tanto en nodos principales como secundarios.

1. Abra `/etc/drbd.d/r0.res` y copie la siguiente definición de recurso en ambas VM:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Inicialice el recurso mediante `drbdadm` en ambas VM:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. En la VM principal (`hadb01`), fuerce la propiedad (principal) del recurso DRBD:

        sudo drbdadm primary --force r0

Si examina el contenido de /proc/drbd (`sudo cat /proc/drbd`) en ambas máquinas virtuales, debe ver `Primary/Secondary` en `hadb01` y `Secondary/Primary` en `hadb02`, que concuerda con la solución en este punto. El disco de 5 GB se sincronizara través de la red 10.10.10.0/24 sin coste alguno para los clientes.

Una vez sincronizado el disco, puede crear el sistema de archivos en `hadb01`. Para tareas de prueba usamos ext2, pero el siguiente código creará un sistema de archivos ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Montaje del recurso DRBD
Ahora está preparado para montar los recursos DRBD en `hadb01`. Debian y productos derivados usan `/var/lib/mysql` como directorio de datos de MySQL. Dado que no ha instalado MySQL, cree el directorio y monte el recurso DRBD. Para llevar a cabo esta opción, ejecute el siguiente código en `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Instalación de MySQL
Ahora estamos preparados para instalar MySQL en `hadb01`:

    sudo apt-get install mysql-server

Para `hadb02`, tiene dos opciones. Puede instalar mysql-server, lo que creará /var/lib/mysql, lo rellenará con un nuevo directorio de datos y después quitará el contenido. Para llevar a cabo esta opción, ejecute el siguiente código en `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La segunda opción consiste en un realizar una conmutación por error para `hadb02` y después instalar mysql-server allí. Los scripts de instalación tendrán en cuenta la instalación existente y no la tocarán.

Ejecute el siguiente fragmento de código en `hadb01`:

    sudo drbdadm secondary –force r0

Ejecute el siguiente fragmento de código en `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si no pretende conmutar por error DRBD ahora, la primera opción es más sencilla aunque posiblemente menos elegante. Después de configurar esto, puede comenzar a trabajar en su base de datos MySQL. Ejecute el código siguiente en `hadb02` (o en cualquiera de los servidores activos, conforme a DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> esta última instrucción deshabilita de forma eficaz la autenticación para el usuario raíz en esta tabla. Las instrucciones GRANT de producción deben reemplazar esto y solamente se incluye por motivos ilustrativos.

Si desea realizar consultas desde fuera de las máquinas virtuales, que es la finalidad de esta guía, también necesita habilitar las redes para MySQL. Abra `/etc/mysql/my.cnf` en ambas VM y vaya a `bind-address`. Cambie la dirección de 127.0.0.1 a 0.0.0.0. Después de guardar el archivo, emita `sudo service mysql restart` en su principal actual.

### <a name="create-the-mysql-load-balanced-set"></a>Creación del conjunto de carga equilibrada de MySQL
Vuelva al portal, vaya a `hadb01` y elija **Puntos de conexión**. Para crear un punto de conexión, elija MySQL (TCP 3306) en la lista desplegable y seleccione **Crear nuevo conjunto de carga equilibrada**. Dé un nombre al conjunto de punto de conexión de carga equilibrada `lb-mysql`. Establezca **Tiempo** en 5 segundos como mínimo.

Tras crear el punto de conexión, vaya a `hadb02`, elija **Puntos de conexión** y cree un punto de conexión. Elija `lb-mysql` y seleccione MySQL en la lista desplegable. También puede usar la CLI de Azure para este paso.

Ya tiene todo lo que necesita para una operación manual del clúster.

### <a name="test-the-load-balanced-set"></a>Prueba del conjunto de carga equilibrada
Las pruebas se pueden realizar desde un equipo externo usando cualquier cliente MySQL, o bien mediante ciertas aplicaciones, como phpMyAdmin ejecutada como un sitio web de Azure. En este caso, utiliza una herramienta de línea de comandos de MySQL en otro cuadro de Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Conmutación por error manual
Puede simular conmutaciones por error cerrando MySQL, cambiando el rol principal de DRBD e iniciando MySQL de nuevo.

Para llevar a cabo esta opción, ejecute el siguiente código en hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Después en hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Una vez realizada la conmutación por error manualmente, puede repetir la consulta remota, que debe funcionar perfectamente.

## <a name="set-up-corosync"></a>Configuración de Corosync
Corosync es la infraestructura de clúster subyacente que Pacemaker necesita para trabajar. Para Heartbeat (y otras metodologías como Ultramonkey), Corosync es una división de las funcionalidades CRM, mientras que la funcionalidad de Pacemaker es más similar a Hearbeat.

La restricción principal para Corosync en Azure es que Corosync prefiere comunicaciones multidifusión mejor que difusión y difusión mejor que unidifusión, pero las redes de Microsoft Azure solo admiten unidifusión.

Por suerte, Corosync tiene un modo de unidifusión activo. La única restricción real es que, dado que todos los nodos no se comunican entre sí, necesita definir dichos nodos en los archivos de configuración, incluidas sus direcciones IP. Podemos utilizar los archivos de ejemplo de Corosync para Unidifusión y cambiar la dirección de enlace, las listas de nodos y los directorios de registro (Ubuntu usa `/var/log/corosync`, mientras que los archivos de ejemplo usan `/var/log/cluster`) y habilitar las herramientas de cuórum.

> [!NOTE]
> Use la siguiente directiva `transport: udpu` y las direcciones IP definidas manualmente para ambos nodos.

Ejecute el siguiente fragmento de código en `/etc/corosync/corosync.conf` en ambos nodos:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copie este archivo de configuración en ambas VM e inicie Corosync en los dos nodos:

    sudo service start corosync

Poco después de iniciar el servicio, el clúster se debe establecer en el anillo actual y se debe constituir el cuórum. Podemos comprobar esta funcionalidad revisando registros o ejecutando el código siguiente:

    sudo corosync-quorumtool –l

Verá un resultado similar al de la imagen siguiente:

![corosync-quorumtool -l sample output](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Configuración de Pacemaker
Pacemaker usa el clúster para supervisar recursos, definir el momento en el que los principales dejan de funcionar y cambiar estos recursos a los secundarios. Entre las diferentes posibilidades que existen, los recursos se pueden definir desde un conjunto de scripts disponibles o desde scripts LSB (de tipo init).

Queremos que Pacemaker "posea" el recurso DRBD, el punto de montaje y el servicio MySQL. Si Pacemaker puede activar y desactivar DRBD, montarlo y desmontarlo y después iniciar y detener MySQL en el orden correcto cuando algo vaya mal con la máquina principal, la configuración se habrá completado.

Cuando instalé por primera vez Pacemaker, la configuración debe ser suficientemente sencilla, algo como lo siguiente:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Compruebe la configuración ejecutando `sudo crm configure show`.
2. Después cree un archivo (por ejemplo, `/tmp/cluster.conf`) con los siguiente recursos:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Cargue el archivo en la configuración. Solo tiene que hacer esto en un nodo.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Asegúrese de que Pacemaker se inicia durante el arranque en ambos nodos:

        sudo update-rc.d pacemaker defaults

5. Use `sudo crm_mon –L` para comprobar que uno de los nodos se ha convertido en el maestro para el clúster y se ejecuta en todos los recursos. Puede usar mount y ps para comprobar que los recursos se están ejecutando.

La siguiente captura de pantalla muestra `crm_mon` con un nodo detenido (salga seleccionando Ctrl+C):

![crm_mon node stopped](./media/mysql-cluster/image002.png)

Esta captura de pantalla muestra ambos nodos, uno maestro y otro subordinado:

![crm_mon operational master/slave](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Prueba
Ya puede llevar a cabo una simulación de conmutación por error automática. Existen dos formas de hacerlo: suave y dura.

Para llevar a cabo la simulación suave, usaremos la función de cierre del clúster: ``crm_standby -U `uname -n` -v on``. Si usa esto en el maestro, el subordinado asume el control. No olvide volver a establecer esto en desconectado. Si no lo hace, crm_mon mostrará un nodo en espera.

La forma dura consiste en cerrar la VM principal (hadb01) mediante el portal o cambiando el nivel de ejecución en la VM (es decir, detener, apagar). Esto ayuda a Corosync y Pacemaker indicando que el maestro deja de funcionar. Puede probar esto (útil para ventanas de mantenimiento), pero también puede forzar el escenario congelando la VM.

## <a name="stonith"></a>STONITH
Debe ser posible emitir un cierre de máquina virtual a través de la CLI de Azure en lugar de un script STONITH que controle un dispositivo físico. Puede usar `/usr/lib/stonith/plugins/external/ssh` como base y habilitar STONITH en la configuración del clúster. La CLI de Azure se debe instalar globalmente y la configuración y el perfil de publicación se deben cargar para el usuario del clúster.

Ejemplo de código para el recurso disponible en [GitHub](https://github.com/bureado/aztonith). Cambie la configuración del clúster agregando lo siguiente a `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> El script no realiza comprobaciones ascendentes o descendentes. El recurso SSH original tenía 15 comprobaciones ping, pero el tiempo de recuperación para una VM de Azure podría ser más variable.

## <a name="limitations"></a>Limitaciones
Se aplican las siguientes limitaciones:

* El script de recursos DRBD linbit que administra DRBD como un recurso en Pacemaker usa `drbdadm down` al cerrar un nodo, aunque dicho nodo esté entrando en modo de espera. No se trata de la situación ideal porque el subordinado no sincronizará el recurso DRBD mientras se escribe en el maestro. Si no se produce un error en el maestro, el subordinado podrá asumir el control y el estado del sistema de archivos anterior. Hay dos formas posibles de resolver este problema:
  * Aplicando un comando `drbdadm up r0` en todos los nodos del clúster mediante un guardián local (sin clústeres)
  * Editando el script DRBD linbit, asegurándose de que no se llama a `down`, en `/usr/lib/ocf/resource.d/linbit/drbd`
* El equilibrador de carga necesita al menos cinco segundos para responder, por lo que las aplicaciones deben ser compatibles con clústeres y ser más tolerantes a errores de tiempo de espera. También pueden ayudar otras arquitecturas, como las colas de la aplicación y middlewares de consulta.
* El ajuste de MySQL es necesario para garantizar que la escritura se realiza a un ritmo apropiado y las memorias caché se vacían en disco con la máxima frecuencia posible para minimizar la pérdida de memoria.
* El rendimiento de escritura depende de la interconexión de las VM en la conmutación virtual ya que este es el mecanismo que usa DRBD para replicar el dispositivo.
