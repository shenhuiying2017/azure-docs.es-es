---
title: Configuración de la replicación del sistema de SAP HANA en Azure Virtual Machines | Microsoft Docs
description: Establezca alta disponibilidad de SAP HANA en Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: e3fb06309dabd7f66d5873e4c5faa48b468854f6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Alta disponibilidad de SAP HANA en Azure Virtual Machines (VM)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Localmente, puede usar la replicación del sistema de HANA o el almacenamiento compartido para establecer la alta disponibilidad para SAP HANA.
En Azure VM, la replicación del sistema de HANA en Azure es que la única función de alta disponibilidad que se admite hasta ahora. La replicación de SAP HANA se realiza con un nodo principal y al menos uno secundario. Los cambios en los datos del nodo principal se replican en los secundarios de forma sincrónica o asincrónica.

En este artículo se describe cómo implementar las máquinas virtuales, configurarlas, instalar la plataforma del clúster, e instalar y configurar la replicación del sistema de SAP HANA.
En las configuraciones de ejemplo, los comandos de instalación, etc., se usa el número de instancia 03 y el HN1 del identificador del sistema de HANA.

Lea primero las notas y los documentos de SAP siguientes:

* Nota de SAP [1928533], que incluye:
  * La lista de tamaños de máquina virtual de Azure que se admiten para la implementación del software de SAP
  * Información importante sobre la capacidad para los tamaños de máquina virtual de Azure
  * Software de SAP admitido y combinaciones de sistema operativo y base de datos
  * Versión del kernel de SAP requerida para Windows y Linux en Microsoft Azure
* La nota de SAP [2015553] enumera los requisitos previos para las implementaciones de software de SAP admitidas por SAP en Azure.
* La nota de SAP [2205917] contiene configuraciones recomendadas del sistema operativo para SUSE Linux Enterprise Server para SAP Applications
* La nota de SAP [1944799] contiene guías de SAP HANA para SUSE Linux Enterprise Server para SAP Applications
* La nota de SAP [2178632] contiene información detallada sobre todas las métricas de supervisión notificadas para SAP en Azure.
* La nota de SAP [2191498] incluye la versión de SAP Host Agent necesaria para Linux en Azure.
* La nota de SAP [2243692] incluye información acerca de las licencias de SAP en Linux en Azure.
* La nota de SAP [1984787] incluye información general sobre SUSE Linux Enterprise Server 12.
* La nota de SAP [1999351] contiene más soluciones de problemas de la extensión de supervisión mejorada de Azure para SAP.
* La [WIKI de la comunidad SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene todas las notas de SAP que se necesitan para Linux.
* [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]
* [Implementación de Azure Virtual Machines para SAP en Linux (este artículo)][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
* [Escenario de rendimiento optimizado para la replicación del sistema de SAP HANA][suse-hana-ha-guide] La guía contiene toda la información necesaria para configurar la replicación local del sistema de SAP HANA. Esta guía sirve como orientación.

## <a name="overview"></a>Información general

Para lograr una alta disponibilidad, SAP HANA se instala en dos máquinas virtuales. Los datos se replican mediante Replicación del sistema de HANA.

![Información general sobre la alta disponibilidad de SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

La instalación de la Replicación del sistema de SAP HANA utiliza un nombre de host dedicado y direcciones IP virtuales. En Azure, se requiere un equilibrador de carga para usar una dirección IP virtual. En la lista siguiente se muestra la configuración del equilibrador de carga.

* Configuración de front-end
  * Dirección IP 10.0.0.13 para hn1-db
* Configuración de back-end
  * Se conecta a interfaces de red principales de todas las máquinas virtuales que deben formar parte de la Replicación del sistema de HANA.
* Puerto de sondeo
  * Puerto 62503
* Reglas de equilibrio de carga
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Implementación de Linux

El agente de recursos para SAP HANA se incluye en SUSE Linux Enterprise Server para SAP Applications.
Azure Marketplace contiene una imagen de SUSE Linux Enterprise Server para SAP Applications 12 que puede usar para implementar nuevas máquinas virtuales.

### <a name="deploy-with-template"></a>Implementación con plantilla
Para implementar todos los recursos necesarios, puede usar una de las plantillas de inicio rápido de GitHub. La plantilla implementa las máquinas virtuales, el equilibrador de carga, el conjunto de disponibilidad, etc. Para implementar la plantilla, siga estos pasos:

1. Abra la [plantilla de base de datos][template-multisid-db] o la [plantilla combinada][template-converged] en Azure Portal. 
   La plantilla de la base de datos solo crea las reglas de equilibrio de carga para una base de datos, mientras que la plantilla combinada también crea las reglas de equilibrio de carga para instancias ASCS/SCS y ERS (solamente para Linux). Si tiene previsto instalar un sistema basado en SAP NetWeaver y desea instalar la instancia ASCS/SCS en las mismas máquinas, use la [plantilla combinada][template-converged].
1. Escriba los siguientes parámetros:
    1. Identificador de sistema SAP  
       Escriba el identificador del sistema SAP que se va a instalar. El identificador se va a usar como prefijo de los recursos que se implementan.
    1. Tipo de pila (solo si usa la plantilla combinada)   
       Seleccione el tipo de la pila de SAP NetWeaver
    1. Tipo de sistema operativo  
       Seleccione una de las distribuciones de Linux. En este ejemplo, seleccione SLES 12
    1. Tipo de base de datos  
       Seleccione HANA
    1. Tamaño del sistema SAP  
       La cantidad de SAPS que va a proporcionar el sistema nuevo. Si no está seguro de cuántos SAP necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP
    1. Disponibilidad del sistema  
       Seleccione alta disponibilidad
    1. Nombre de usuario y contraseña del administrador  
       Se crea un usuario nuevo que se puede usar para iniciar sesión en la máquina.
    1. Subred nueva o existente  
       Determina si es necesario crear una red virtual y subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione la existente.
    1. Identificador de subred  
    El identificador de la subred a la que deben conectarse las máquinas virtuales. Para conectar la máquina virtual a la red local, seleccione la subred de la VPN o la red virtual de Express Route. Generalmente, el identificador tiene un aspecto similar al siguiente: /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

### <a name="manual-deployment"></a>Implementación manual

1. Creación de un grupo de recursos
1. Creación de una red virtual
1. Creación de un conjunto de disponibilidad  
   Establecimiento del dominio máximo de actualización
1. Creación de un equilibrador de carga (interno)  
   Seleccione la red virtual creada en el segundo paso.
1. Creación de la máquina virtual 1  
   Utilice al menos SLES4SAP 12 SP1, en este ejemplo se usará la imagen SLES4SAP 12 SP2. https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES para SAP 12 SP2 (Premium)  
   Seleccione el conjunto de disponibilidad creado anteriormente.  
1. Creación de la máquina virtual 2  
   Utilice al menos SLES4SAP 12 SP1, en este ejemplo se usará la imagen SLES4SAP 12 SP1 BYOS. https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES para SAP 12 SP2 (Premium)  
   Seleccione el conjunto de disponibilidad creado anteriormente.  
1. Incorporación de los discos de datos
1. Configuración del equilibrador de carga
    1. Creación de un grupo de direcciones IP de front-end
        1. Abra el equilibrador de carga, seleccione el grupo de direcciones IP de front-end y haga clic en Agregar
        1. Escriba el nombre del nuevo grupo de direcciones IP de front-end (por ejemplo, hana-front-end)
        1. Configure la asignación como estática y escriba la dirección IP (por ejemplo **10.0.0.13**).
        1. Haga clic en Aceptar
        1. Una vez creado el nuevo grupo de direcciones IP de front-end, anote la dirección IP
    1. Creación de un grupo de back-end
        1. Abra el equilibrador de carga, seleccione los grupos de back-end y haga clic en Agregar
        1. Escriba el nombre del nuevo grupo de back-end (por ejemplo, hana-back-end)
        1. Haga clic en Agregar una máquina virtual
        1. Seleccione el conjunto de disponibilidad que creó anteriormente
        1. Seleccione las máquinas virtuales del clúster de SAP HANA
        1. Haga clic en Aceptar
    1. Creación de un sondeo de estado
        1. Abra el equilibrador de carga, seleccione los sondeos de estado y haga clic en Agregar
        1. Escriba el nombre del sondeo de estado nuevo (por ejemplo hana-hp)
        1. Seleccione TCP como protocolo, puerto 625**03**, y mantenga el intervalo de 5 y el umbral incorrecto 2
        1. Haga clic en Aceptar
    1. SAP HANA 1.0: creación de reglas de equilibrio de carga
        1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar
        1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, hana-lb-3**03**15)
        1. Seleccione la dirección IP de front-end, el grupo de back-end y el sondeo de estado que creó anteriormente (por ejemplo, hana-front-end)
        1. Conserve el protocolo TCP y escriba el puerto 3**03**15
        1. Aumente el tiempo de espera de inactividad a 30 minutos
        1. **Asegúrese de habilitar la dirección IP flotante**
        1. Haga clic en Aceptar
        1. Repita los pasos anteriores para el puerto 3**03**17
    1. SAP HANA 2.0: creación de reglas de equilibrio de carga para la base de datos del sistema
        1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar
        1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, hana-lb-3**03**13).
        1. Seleccione la dirección IP de front-end, el grupo de back-end y el sondeo de estado que creó anteriormente (por ejemplo, hana-front-end)
        1. Conserve el protocolo TCP y escriba el puerto 3**03**13.
        1. Aumente el tiempo de espera de inactividad a 30 minutos
        1. **Asegúrese de habilitar la dirección IP flotante**
        1. Haga clic en Aceptar
        1. Repita los pasos anteriores para el puerto 3**03**14.
    1. SAP HANA 2.0: creación de reglas de equilibrio de carga para la primera base de datos de inquilino
        1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar
        1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, hana-lb-3**03**40).
        1. Seleccione la dirección IP de front-end, el grupo de back-end y el sondeo de estado que creó anteriormente (por ejemplo, hana-front-end)
        1. Conserve el protocolo TCP y escriba el puerto 3**03**40.
        1. Aumente el tiempo de espera de inactividad a 30 minutos
        1. **Asegúrese de habilitar la dirección IP flotante**
        1. Haga clic en Aceptar
        1. Repita los pasos anteriores para el puerto 3**03**41 y 3**03**42.

Para más información sobre los puertos necesarios para SAP HANA, lea el capítulo [Connections to Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) (Conexiones a las bases de datos de inquilino) de la guía [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) (Bases de datos de inquilino de SAP HANA) o la [nota de SAP 2388694][2388694].


## <a name="create-pacemaker-cluster"></a>Creación del clúster de Pacemaker

Siga los pasos de [Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure](high-availability-guide-suse-pacemaker.md) para crear un clúster de Pacemaker básico para este servidor HANA. También puede utilizar el mismo clúster de Pacemaker para SAP HANA y SAP NetWeaver (A)SCS.

## <a name="installing-sap-hana"></a>Instalación de SAP HANA

Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2 del clúster de Pacemaker.

1. **[A]** Configuración del diseño del disco
    1. LVM  
       
       Por lo general, se recomienda usar LVM para volúmenes de almacén de datos y archivos de registro. En el ejemplo siguiente se supone que las máquinas virtuales tienen cuatro discos de datos asociados que se deben usar para crear dos volúmenes.

       Enumere todos los discos disponibles.
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Salida de ejemplo
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Cree volúmenes físicos de todos los discos que desee usar.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Cree un grupo de volúmenes para los archivos de datos, un grupo de volúmenes para los archivos de registro y otro para el directorio compartido de SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       Cree los volúmenes lógicos

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Cree los directorios de montaje y copie el UUID de todos los volúmenes lógicos
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Cree entradas de fstab para los tres volúmenes lógicos.
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Inserte esta línea en /etc/fstab.
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Monte los volúmenes nuevos.
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Discos sin formato  
       Para sistemas demostración, puede colocar los archivos de datos y de registro HANA en un disco. Con los siguientes comandos se crea una partición en /dev/disk/azure/scsi1/lun0 y con formato xfs.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Inserte esta línea en /etc/fstab.
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Cree el directorio de destino y monte el disco.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** Configuración de la resolución del nombre de todos los hosts  
    Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos
    ```bash
    sudo vi /etc/hosts
    ```
    Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Instalación de los paquetes de alta disponibilidad de HANA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Para instalar la replicación del sistema de SAP HANA, siga el capítulo 4 de la guía del escenario optimizado para rendimiento para la replicación del sistema de SAP HANA en https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/.

1. **[A]** Ejecute hdblcm desde el DVD de HANA
    * Elija la instalación -> 1
    * Seleccione los componentes adicionales para la instalación -> 1
    * Escriba la ruta de acceso de instalación [/hana/shared]: -> ENTRAR
    * Escriba el nombre del host local [..]: -> ENTRAR
    * ¿Desea agregar hosts adicionales al sistema? (s/n) [n]: -> ENTRAR
    * Escriba el identificador del sistema de SAP HANA: <SID of HANA e.g. HN1>
    * Escriba el número de instancia [00]:   
  Número de instancia de HANA. Use 03 si usó la plantilla de Azure o siguió la implementación manual
    * Seleccione Database Mode (Modo de base de datos) / escriba el índice [1]: -> ENTRAR
    * Seleccione Uso del sistema / escriba el índice [4]:  
  Seleccione el uso del sistema
    * Escriba la ubicación de los volúmenes de datos [/hana/data/HN1]: -> ENTRAR.
    * Escriba la ubicación de los volúmenes de registros [/hana/log/HN1]: -> ENTRAR.
    * ¿Restringir la asignación de memoria máxima? [n]: -> ENTRAR
    * Escriba el nombre del host del certificado para el host "..." -> ENTRAR
    * Escriba la contraseña del usuario del agente de host de SAP (sapadm):
    * Confirme la contraseña del usuario del agente de host de SAP (sapadm):
    * Escriba la contraseña del administrador del sistema (hdbadm):
    * Confirme la contraseña del administrador del sistema (hdbadm):
    * Escriba el directorio principal del administrador de sistema [/usr/sap/HN1/home]: -> ENTRAR.
    * Escriba el shell de inicio de sesión del administrador de sistema [/bin/sh]: -> ENTRAR
    * Escriba el identificador de usuario del administrador de sistema [1001]: -> ENTRAR
    * Escriba el identificador del grupo de usuarios (sapsys) [79]: -> ENTRAR
    * Escriba la contraseña del usuario (SYSTEM) de la base de datos:
    * Confirme la contraseña del usuario (SYSTEM) de la base de datos:
    * ¿Reiniciar el sistema tras el reinicio de la máquina? [n]: -> ENTRAR
    * ¿Desea continuar? (s/n):   
  Valide el resumen y escriba s para continuar

1. **[A]** Actualice el agente de host de SAP  
  Descargue el archivo más reciente del agente de host de SAP desde [SAP Softwarecenter][sap-swcenter] y ejecute el siguiente comando para actualizar el agente. Reemplace la ruta de acceso al archivo para que apunte al archivo que descargó.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Configuración de la Replicación del sistema de SAP HANA 2.0

Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2 del clúster de Pacemaker.

1. **[1]** Creación de una base de datos de inquilino

   Si usa SAP HANA 2.0 o MDC, cree una base de datos de inquilino para el sistema SAP NetWeaver. Reemplace NW1 por el SID del sistema SAP.

   Inicie sesión como `<hanasid`>adm y ejecute el comando siguiente.

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Configuración de la replicación del sistema en el primer nodo
   
   Inicie sesión como `<hanasid`> adm y haga una copia de seguridad de las bases de datos.

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie los archivos PKI de sistema en la base de datos secundaria.

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Cree el sitio primario.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Configuración de la Replicación del sistema en el segundo nodo
    
    Registre el segundo nodo para iniciar la replicación del sistema. Inicie sesión como `<hanasid`>adm y ejecute el comando siguiente:

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configuración de la Replicación del sistema de SAP HANA 1.0

1. **[1]** Creación de los usuarios necesarios

    Inicie sesión como raíz y ejecute el comando siguiente. Asegúrese de reemplazar las cadenas en negrita (HN1 del identificador de sistema de HANA y número de instancia 03) por los valores de la instalación de SAP HANA.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** Creación de la entrada de almacén de claves
   
    Inicie sesión como raíz y ejecute el siguiente comando para crear una nueva entrada del almacén de claves.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]** Copia de seguridad de la base de datos

   Inicie sesión como raíz y haga una copia de seguridad de las bases de datos.

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Si usa una instalación de varios inquilinos, haga también una copia de seguridad de la base de datos de inquilino.

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Configuración de la replicación del sistema en el primer nodo
    
    Inicie sesión como `<hanasid`>adm y cree el sitio primario.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Configuración de la Replicación del sistema en el nodo secundario

    Inicie sesión como `<hanasid`> adm y registre el sitio secundario.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Creación de recursos de clúster de SAP HANA

   Primero, cree la topología de HANA. Ejecute los comandos siguientes en uno de los nodos del clúster de Pacemaker.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   A continuación, cree los recursos de HANA.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Asegúrese de que el estado del clúster sea el correcto y que se iniciaron todos los recursos. No es importante en qué nodo se ejecutan los recursos.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Prueba de configuración del clúster
En este capítulo se describe cómo se puede probar la configuración. En todas las pruebas se supone que es la raíz y el sistema SAP HANA maestro se ejecuta en la máquina virtual hn1-db-0.

#### <a name="fencing-test"></a>Prueba de vallado

Puede probar la configuración del agente de vallado si deshabilita la interfaz de red en el nodo hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

Ahora, la máquina virtual debería reiniciarse o detenerse, en función de la configuración del clúster.
Si estableció la acción stonith en off (desactivar), la máquina virtual se detendrá y los recursos se migrarán a la máquina virtual en ejecución.

Cuando reinicie la máquina virtual, se producirá un error al iniciarse el recurso de SAP HANA como secundario si estableció AUTOMATED_REGISTER = "false". En este caso, configure la instancia de HANA como secundaria mediante la ejecución de este comando:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Prueba de una conmutación por error manual

Para probar una conmutación por error manual, detenga el servicio de Pacemaker en el nodo hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Después de la conmutación por error, puede reiniciar el servicio. Si estableció AUTOMATED_REGISTER = "false", se producirá un error al iniciarse el recurso de SAP HANA en hn1-db-0 como secundario. En este caso, configure la instancia de HANA como secundaria mediante la ejecución de este comando:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Prueba de una migración

Puede migrar el nodo principal de SAP HANA al ejecutar el siguiente comando
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Si estableció AUTOMATED_REGISTER = "false", esta secuencia de comandos debe migrar el nodo maestro de SAP HANA y el grupo que contiene la dirección IP virtual a hn1-db-1.
Se producirá un error al iniciarse el recurso de SAP HANA en hn1-db-0 como secundario. En este caso, configure la instancia de HANA como secundaria mediante la ejecución de este comando:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

La migración crea restricciones de ubicación que deben eliminarse de nuevo.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

También debe limpiar el estado del recurso de nodo secundario

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Pasos siguientes
* [Planeación e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* Para obtener información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure (instancias grandes), vea [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md). 
