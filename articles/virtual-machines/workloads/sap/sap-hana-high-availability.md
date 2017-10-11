---
title: "Alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure | Microsoft Docs"
description: "Establezca alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 951150e621d21037b0adde7287b9f985290d8d11
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure

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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Localmente, puede usar la replicación del sistema de HANA o el almacenamiento compartido para establecer la alta disponibilidad para SAP HANA.
Actualmente solo se admite la configuración de la replicación del sistema de HANA en Azure. La replicación de SAP HANA se realiza con un nodo maestro y al menos uno subordinado. Los cambios en los datos del nodo maestro se replican en los subordinados de forma sincrónica o asincrónica.

En este artículo se describe cómo implementar las máquinas virtuales, configurarlas, instalar la plataforma del clúster, e instalar y configurar la replicación del sistema de SAP HANA.
En las configuraciones de ejemplo, los comandos de instalación, etc., se usa el número de instancia 03 y el HDB del Id. del sistema de HANA.

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

## <a name="deploying-linux"></a>Implementación de Linux

El agente de recursos para SAP HANA se incluye en SUSE Linux Enterprise Server para SAP Applications.
Azure Marketplace contiene una imagen de SUSE Linux Enterprise Server para SAP Applications 12 con BYOS ("traiga su propia suscripción") que puede usar para implementar nuevas máquinas virtuales.

### <a name="manual-deployment"></a>Implementación manual

1. Creación de un grupo de recursos
1. Creación de una red virtual
1. Creación de dos cuentas de almacenamiento
1. Creación de un conjunto de disponibilidad  
   Establecimiento del dominio máximo de actualización
1. Creación de un equilibrador de carga (interno)  
   Selección de la red virtual del paso anterior
1. Creación de la máquina virtual 1  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES para SAP Applications 12 SP1 (BYOS)  
   Selección de la cuenta de almacenamiento 1  
   Selección del conjunto de disponibilidad  
1. Creación de la máquina virtual 2  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES para SAP Applications 12 SP1 (BYOS)  
   Selección de la cuenta de almacenamiento 2   
   Selección del conjunto de disponibilidad  
1. Incorporación de los discos de datos
1. Configuración del equilibrador de carga
    1. Creación de un grupo de direcciones IP de front-end
        1. Abra el equilibrador de carga, seleccione el grupo de direcciones IP de front-end y haga clic en Agregar
        1. Escriba el nombre del nuevo grupo de direcciones IP de front-end (por ejemplo, hana-front-end)
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
    1. Creación de reglas de equilibrio de carga
        1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar
        1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, hana-lb-3**03**15)
        1. Seleccione la dirección IP de front-end, el grupo de back-end y el sondeo de estado que creó anteriormente (por ejemplo, hana-front-end)
        1. Conserve el protocolo TCP y escriba el puerto 3**03**15
        1. Aumente el tiempo de espera de inactividad a 30 minutos
       1. **Asegúrese de habilitar la dirección IP flotante**
        1. Haga clic en Aceptar
        1. Repita los pasos anteriores para el puerto 3**03**17

### <a name="deploy-with-template"></a>Implementación con plantilla
Para implementar todos los recursos necesarios, puede usar una de las plantillas de inicio rápido de github. La plantilla implementa las máquinas virtuales, el equilibrador de carga, el conjunto de disponibilidad, etc. Siga estos pasos para implementar la plantilla:

1. Abra la [plantilla de base de datos][template-multisid-db] o la [plantilla combinada][template-converged] en Azure Portal. La plantilla de la base de datos solo crea las reglas de equilibrio de carga para una base de datos, mientras que la plantilla combinada también crea las reglas de equilibrio de carga para instancias ASCS/SCS y ERS (solamente para Linux). Si tiene previsto instalar un sistema basado en SAP NetWeaver y desea instalar la instancia ASCS/SCS en las mismas máquinas, use la [plantilla combinada][template-converged].
1. Escriba los siguientes parámetros:
    1. Identificador de sistema SAP  
       Escriba el identificador del sistema SAP que se va a instalar. El identificador se utilizará como prefijo para los recursos que se implementen.
    1. Tipo de pila (solo si usa la plantilla combinada)  
       Seleccione el tipo de la pila de SAP NetWeaver
    1. Tipo de sistema operativo  
       Seleccione una de las distribuciones de Linux. En este ejemplo, seleccione SLES 12 BYOS
    1. Tipo de base de datos  
       Seleccione HANA
    1. Tamaño del sistema SAP  
       La cantidad de SAPS que proporcionará el sistema nuevo. Si no está seguro de cuántos SAPS necesitará el sistema, consulte con el integrador de sistemas o el socio tecnológico de SAP.
    1. Disponibilidad del sistema  
       Seleccione alta disponibilidad
    1. Nombre de usuario y contraseña del administrador  
       Se crea un usuario nuevo que se puede usar para iniciar sesión en la máquina.
    1. Subred nueva o existente  
       Determina si es necesario crear una red virtual y subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione la existente.
    1. Identificador de subred  
    El identificador de la subred a la que deben conectarse las máquinas virtuales. Seleccione la subred de la VPN o la red virtual de Express Route para conectar la máquina virtual a la red local. Generalmente, el identificador tiene un aspecto similar al siguiente: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>.

## <a name="setting-up-linux-ha"></a>Configuración de la alta disponibilidad de Linux

Los elementos siguientes tienen el prefijo [A]: aplicable a todos los nodos, [1]: aplicable solo al nodo 1 o [2]: aplicable solo al nodo 2.

1. [A] SLES solo para SAP BYOS: registre SLES para usar los repositorios
1. [A] SLES solo para SAP BYOS: agregue el módulo de nube pública
1. [A] Actualice SLES
    ```bash
    sudo zypper update

    ```

1. [1] Habilite el acceso de SSH
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] Habilite el acceso de SSH
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] Habilite el acceso de SSH
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] Instale la extensión de alta disponibilidad
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] Configure el diseño del disco
    1. LVM  
    Por lo general, se recomienda utilizar LVM para volúmenes de almacén de datos y archivos de registro. En el ejemplo siguiente se supone que las máquinas virtuales tienen cuatro discos de datos asociados que se deben usar para crear dos volúmenes.
        * Cree volúmenes físicos de todos los discos que desee usar.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Cree un grupo de volúmenes para los archivos de datos, un grupo de volúmenes para los archivos de registro y otro para el directorio compartido de SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Cree los volúmenes lógicos
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Cree los directorios de montaje y copie el UUID de todos los volúmenes lógicos
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Cree entradas de fstab para los tres volúmenes lógicos.
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Inserte esta línea en /etc/fstab.
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Monte los volúmenes nuevos.
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Discos sin formato  
       Para sistemas pequeños o de demostración, puede colocar los archivos de datos y de registro HANA en un disco. Con los siguientes comandos se crea una partición en /dev/sdc y con formato xfs.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>Anote el identificador de /dev/sdc1
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] Configure la resolución del nombre de todos los hosts  
    Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos
    ```bash
    sudo vi /etc/hosts
    ```
    Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] Instale el clúster
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Agregue un nodo al clúster
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] Haga que la contraseña de hacluster coincida
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] Configure corosync para usar otro tipo de transporte y agregue nodelist. De lo contrario, el clúster no funcionará.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Agregue el siguiente contenido en negrita al archivo.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Después, reinicie el servicio corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] Instale los paquetes de alta disponibilidad de HANA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Instalación de SAP HANA

Siga el capítulo 4 de la [Guía del escenario de rendimiento optimizado para la replicación del sistema de SAP HANA][suse-hana-ha-guide] para instalar la replicación del sistema de SAP HANA.

1. [A] Ejecute hdblcm desde el DVD de HANA
    * Elija la instalación -> 1
    * Seleccione los componentes adicionales para la instalación -> 1
    * Escriba la ruta de acceso de instalación [/hana/shared]: -> ENTRAR
    * Escriba el nombre del host local [..]: -> ENTRAR
    * ¿Desea agregar hosts adicionales al sistema? (s/n) [n]: -> ENTRAR
    * Escriba el identificador del sistema de SAP HANA: <SID of HANA e.g. HDB>
    * Escriba el número de instancia [00]:   
  Número de instancia de HANA. Use 03 si usó la plantilla de Azure o siguió el ejemplo anterior
    * Seleccione Database Mode (Modo de base de datos) / escriba el índice [1]: -> ENTRAR
    * Seleccione Uso del sistema / escriba el índice [4]:  
  Seleccione el uso del sistema
    * Escriba la ubicación de los volúmenes de datos [/hana/data/HDB]: -> ENTRAR
    * Escriba la ubicación de los volúmenes de registros [/hana/log/HDB]: -> ENTRAR
    * ¿Restringir la asignación de memoria máxima? [n]: -> ENTRAR
    * Escriba el nombre del host del certificado para el host "..." [...]: -> ENTRAR
    * Escriba la contraseña del usuario del agente de host de SAP (sapadm):
    * Confirme la contraseña del usuario del agente de host de SAP (sapadm):
    * Escriba la contraseña del administrador del sistema (hdbadm):
    * Confirme la contraseña del administrador del sistema (hdbadm):
    * Escriba el directorio principal del administrador de sistema [/usr/sap/HDB/home]: -> ENTRAR
    * Escriba el shell de inicio de sesión del administrador de sistema [/bin/sh]: -> ENTRAR
    * Escriba el identificador de usuario del administrador de sistema [1001]: -> ENTRAR
    * Escriba el identificador del grupo de usuarios (sapsys) [79]: -> ENTRAR
    * Escriba la contraseña del usuario (SYSTEM) de la base de datos:
    * Confirme la contraseña del usuario (SYSTEM) de la base de datos:
    * ¿Reiniciar el sistema tras el reinicio de la máquina? [n]: -> ENTRAR
    * ¿Desea continuar? (s/n):  
  Valide el resumen y escriba s para continuar
1. [A] Actualice el agente de host de SAP  
  Descargue el archivo más reciente del agente de host de SAP desde [SAP Softwarecenter][sap-swcenter] y ejecute el siguiente comando para actualizar el agente. Reemplace la ruta de acceso al archivo para que apunte al archivo que descargó.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] Cree la replicación de HANA (como raíz)  
    Ejecute el siguiente comando. Asegúrese de reemplazar las cadenas en negrita (HDB de Id. de sistema de HANA y número de instancia 03) con los valores de la instalación de SAP HANA.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Cree la entrada de almacén de claves (como raíz).
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] Realice la copia de seguridad de la base de datos (como raíz).
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] Cambie al usuario sapsid (por ejemplo, hdbadm) y cree el sitio principal.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] Cambie al usuario sapsid (por ejemplo, hdbadm) y cree el sitio secundario.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Configuración de la plataforma del clúster

Cambie la configuración predeterminada

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>ahora cargaremos el archivo en el clúster
sudo crm configura la actualización de carga de crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Creación de un dispositivo STONITH

El dispositivo STONITH usa una entidad de servicio para la autorización de Microsoft Azure. Para crear una entidad de servicio, siga estos pasos:

1. Vaya a <https://portal.azure.com>
1. Abra la hoja Azure Active Directory  
   Vaya a Propiedades y anote el identificador del directorio. Se trata del **id. de inquilino**.
1. Haga clic en Registros de aplicaciones
1. Haga clic en Agregar
1. Escriba un nombre, seleccione el tipo de aplicación "Aplicación web o API", escriba una dirección URL de inicio de sesión (por ejemplo, http://localhost) y haga clic en Crear
1. La dirección URL de inicio de sesión no se usa y puede ser cualquier dirección URL válida
1. Seleccione la nueva aplicación y haga clic en las llaves de la pestaña Configuración
1. Escriba una descripción para la nueva clave, seleccione "Nunca expira" y haga clic en Guardar
1. Anote el valor. Se utiliza como **contraseña** para la entidad de servicio
1. Anote el identificador de la aplicación. Se utiliza como nombre de usuario (**Id. de inicio de sesión** en los pasos siguientes) de la entidad de servicio

La entidad de servicio no tiene permiso para tener acceso a los recursos de Azure de forma predeterminada. Debe concedérselos para iniciar y detener (desasignar) todas las máquinas virtuales del clúster.

1. Vaya a https://portal.azure.com
1. Abra la hoja Todos los recursos
1. Seleccione la máquina virtual
1. Haga clic en Control de acceso (IAM)
1. Haga clic en Agregar
1. Seleccione el rol de propietario
1. Escriba el nombre de la aplicación que creó anteriormente
1. Haga clic en Aceptar

Después de editar los permisos para las máquinas virtuales, puede configurar los dispositivos STONITH en el clúster.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>ahora cargaremos el archivo en el clúster
sudo crm configura la actualización de carga de crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Creación de recursos de SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>ahora cargaremos el archivo en el clúster
sudo crm configura la actualización de carga de crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>ahora cargaremos el archivo en el clúster
sudo crm configura la actualización de carga de crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Prueba de configuración del clúster
En el capítulo siguiente se describe cómo se puede probar la configuración. En todas las pruebas se supone que es la raíz y el sistema SAP HANA maestro lo que se ejecuta en la máquina virtual saphanavm1.

#### <a name="fencing-test"></a>Prueba de vallado

Puede probar la configuración del agente de vallado si deshabilita la interfaz de red en el nodo saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

Ahora, la máquina virtual debería reiniciarse o detenerse, en función de la configuración del clúster.
Si estableció la acción stonith en off (desactivar), la máquina virtual se detendrá y los recursos se migrarán a la máquina virtual en ejecución.

Cuando reinicie la máquina virtual, se producirá un error al iniciarse el recurso de SAP HANA como secundario si estableció AUTOMATED_REGISTER = "false". En este caso, debe configurar la instancia de HANA como secundaria mediante la ejecución del comando siguiente:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Prueba de una conmutación por error manual

Para probar una conmutación por error manual, detenga el servicio de marcapasos en el nodo saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

Después de la conmutación por error, puede reiniciar el servicio. Si estableció AUTOMATED_REGISTER = "false", se producirá un error al iniciarse el recurso de SAP HANA en saphanavm1 como secundario. En este caso, debe configurar la instancia de HANA como secundaria mediante la ejecución del comando siguiente:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Prueba de una migración

Puede migrar el nodo principal de SAP HANA al ejecutar el siguiente comando
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Esto debe migrar el nodo principal de SAP HANA y el grupo que contiene la dirección IP virtual a saphanavm2.
Si estableció AUTOMATED_REGISTER = "false", se producirá un error al iniciarse el recurso de SAP HANA en saphanavm1 como secundario. En este caso, debe configurar la instancia de HANA como secundaria mediante la ejecución del comando siguiente:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

La migración crea restricciones de ubicación que deben eliminarse de nuevo.

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

También debe limpiar el estado del recurso de nodo secundario

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Pasos siguientes
* [Planeación e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* Para obtener información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure (instancias grandes), vea [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md). 
