---
title: Alta disponibilidad para NFS en máquinas virtuales de Azure en SUSE Linux Enterprise Server | Microsoft Docs
description: Alta disponibilidad para NFS en máquinas virtuales de Azure en SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: sedusch
ms.openlocfilehash: 53bc4a6f4ecca8ffe3575a038b86192a8663c35c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261490"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidad para NFS en máquinas virtuales de Azure en SUSE Linux Enterprise Server

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

En este artículo se describe cómo implementar y configurar las máquinas virtuales, instalar el marco de trabajo del clúster e instalar un servidor NFS de alta disponibilidad que se puede usar para almacenar los datos compartidos de un sistema SAP con alta disponibilidad.
En esta guía se describe cómo configurar un servidor NFS de alta disponibilidad que se usa en dos sistemas SAP: NW1 y NW2. En los nombres de los recursos (por ejemplo, máquinas virtuales, redes virtuales) del ejemplo se da por supuesto que ha usado la [plantilla del servidor de archivos de SAP][template-file-server] con el prefijo de recursos **prod**.

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
* [Escenario de rendimiento optimizado para la replicación del sistema de SAP HANA][suse-hana-ha-guide]  
  La guía contiene toda la información necesaria para configurar la replicación local del sistema de SAP HANA. Esta guía sirve como orientación.
* [Highly Available NFS Storage with DRBD and Pacemaker][suse-drbd-guide] (Almacenamiento NFS de alta disponibilidad con DRBD y Pacemaker) La guía contiene toda la información necesaria para configurar un servidor NFS de alta disponibilidad. Esta guía sirve como orientación.


## <a name="overview"></a>Información general

Para lograr alta disponibilidad, SAP NetWeaver requiere un servidor NFS. El servidor NFS está configurado en un clúster distinto y lo pueden usar varios sistemas SAP.

![Información general sobre la alta disponibilidad de SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

El servidor NFS usa un nombre de host virtual dedicado y direcciones IP virtuales para todos los sistemas SAP que usan este servidor NFS. En Azure, se requiere un equilibrador de carga para usar una dirección IP virtual. En la lista siguiente se muestra la configuración del equilibrador de carga.        

* Configuración de front-end
  * Dirección IP 10.0.0.4 de NW1
  * Dirección IP 10.0.0.5 de NW2
* Configuración de back-end
  * Se conecta a interfaces de red principales de todas las máquinas que deben ser parte del clúster NFS
* Puerto de sondeo
  * Puerto 61000 de NW1
  * Puerto 61001 de NW2
* Reglas de equilibrio de carga
  * TCP 2049 de NW1
  * UDP 2049 de NW1
  * TCP 2049 de NW2
  * UDP 2049 de NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configuración de un servidor NFS de alta disponibilidad

Puede usar una plantilla de Azure de GitHub para implementar todos los recursos de Azure necesarios, como máquinas virtuales, conjunto de disponibilidad y equilibrador de carga, o puede implementar los recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implementación de Linux mediante una plantilla de Azure

Azure Marketplace contiene una imagen de SUSE Linux Enterprise Server para SAP Applications 12 que puede usar para implementar nuevas máquinas virtuales.
Para implementar todos los recursos necesarios, puede usar una de las plantillas de inicio rápido de GitHub. La plantilla implementa las máquinas virtuales, el equilibrador de carga, el conjunto de disponibilidad, etc. Siga estos pasos para implementar la plantilla:

1. Abra la [plantilla del servidor de archivos SAP][template-file-server] en Azure Portal   
1. Escriba los siguientes parámetros:
   1. Prefijo de recurso  
      Escriba el prefijo que desea usar. El valor se usa como prefijo de los recursos que se implementan.
   2. Número de sistemas SAP: escriba el número de sistemas SAP que usarán este servidor de archivos. Esta acción implementará la cantidad necesaria de configuraciones de front-end, reglas de equilibrio de carga, puertos de sondeo, discos, etc.
   3. Tipo de sistema operativo  
      Seleccione una de las distribuciones de Linux. En este ejemplo, seleccione SLES 12
   4. Nombre de usuario y contraseña del administrador  
      Se crea un usuario nuevo que se puede usar para iniciar sesión en la máquina.
   5. Identificador de subred  
      El identificador de la subred a la que deben conectarse las máquinas virtuales. Deje el valor en blanco si quiere crear una nueva red virtual o seleccione la subred de la VPN o la red virtual de Express Route para conectar la máquina virtual a la red local. El identificador suele tener este aspecto: /subscriptions/**&lt;id. de suscripción&gt;**/resourceGroups/**&lt;nombre del grupo de recursos&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nombre de red virtual&gt;**/subnets/**&lt;nombre de subred&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementación manual de Linux mediante Azure Portal

En primer lugar, debe crear las máquinas virtuales de este clúster NFS. Después, creará un equilibrador de carga y usará las máquinas virtuales de los grupos de servidores back-end.

1. Creación de un grupo de recursos
1. Creación de una red virtual
1. Creación de un conjunto de disponibilidad  
   Establecimiento del dominio máximo de actualización
1. Creación de la máquina virtual 1   
   Utilice al menos SLES4SAP 12 SP3, en este ejemplo se usa la imagen SLES4SAP 12 SP3 BYOS de SLES For SAP Applications 12 SP3 (BYOS)  
   Seleccione el conjunto de disponibilidad creado anteriormente.  
1. Creación de la máquina virtual 2   
   Use al menos SLES4SAP 12 SP3, en este ejemplo se usa la imagen SLES4SAP 12 SP3 BYOS  
   Se usa SLES para SAP Applications 12 SP3 (BYOS)  
   Seleccione el conjunto de disponibilidad creado anteriormente.  
1. Agregue un disco de datos por cada sistema SAP a ambas máquinas virtuales.
1. Creación de un equilibrador de carga (interno)  
   1. Creación de las direcciones IP de front-end
      1. Dirección IP 10.0.0.4 de NW1
         1. Abra el equilibrador de carga, seleccione el grupo de direcciones IP de front-end y haga clic en Agregar
         1. Escriba el nombre del nuevo grupo IP de direcciones front-end (por ejemplo, **nw1-frontend**).
         1. Configure la asignación como estática y escriba la dirección IP (por ejemplo **10.0.0.4**).
         1. Haga clic en Aceptar         
      1. Dirección IP 10.0.0.5 de NW2
         * Repita los pasos anteriores con NW2.
   1. Creación de los grupos de servidores back-end
      1. Conectado a las interfaces de red principales de todas las máquinas que deben ser parte del clúster NFS para NW1:
         1. Abra el equilibrador de carga, seleccione los grupos de back-end y haga clic en Agregar
         1. Escriba el nombre del nuevo grupo de servidores back-end (por ejemplo, **nw1-backend**).
         1. Haga clic en Agregar una máquina virtual
         1. Seleccione el conjunto de disponibilidad que creó anteriormente
         1. Seleccione las máquinas virtuales del clúster NFS.
         1. Haga clic en Aceptar
      1. Conectado a las interfaces de red principales de todas las máquinas que deben ser parte del clúster NFS para NW2:
         * Repita los pasos anteriores para crear un grupo de servidores back-end para NW2.
   1. Creación de los sondeos de estado
      1. Puerto 61000 de NW1
         1. Abra el equilibrador de carga, seleccione los sondeos de estado y haga clic en Agregar
         1. Escriba el nombre del nuevo sondeo de estado (por ejemplo, **nw1-hp**).
         1. Seleccione TCP como protocolo, puerto 610**00**, y mantenga el intervalo de 5 y el umbral incorrecto 2.
         1. Haga clic en Aceptar
      1. Puerto 61001 de NW2
         * Repita los pasos anteriores para crear un sondeo de estado para NW2.
   1. Reglas de equilibrio de carga
      1. TCP 2049 de NW1
         1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar
         1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, **nw1-lb-2049**).
         1. Seleccione la dirección IP de front-end, el grupo de servidores back-end y el sondeo de estado que creó anteriormente (por ejemplo, **nw1-front-end**).
         1. Conserve el protocolo **TCP** y escriba el puerto **2049**.
         1. Aumente el tiempo de espera de inactividad a 30 minutos
         1. **Asegúrese de habilitar la dirección IP flotante**
         1. Haga clic en Aceptar    
      1. UDP 2049 de NW1
         * Repita los pasos anteriores con el puerto 2049 y UDP de NW1.
      1. TCP 2049 de NW2
         * Repita los pasos anteriores con el puerto 2049 y TCP de NW2.
      1. UDP 2049 de NW2
         * Repita los pasos anteriores con el puerto 2049 y UDP de NW2.

### <a name="create-pacemaker-cluster"></a>Creación del clúster de Pacemaker

Siga los pasos de [Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure](high-availability-guide-suse-pacemaker.md) para crear un clúster de Pacemaker básico para este servidor NFS.

### <a name="configure-nfs-server"></a>Configuración del servidor NFS

Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2.

1. **[A]** Configure la resolución nombres de host   

   Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Habilite el servidor NFS.

   Cree la entrada de exportación de raíz, inicie el servidor NFS y habilite el inicio automático.

   <pre><code>
   sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/

   sudo systemctl enable nfsserver
   sudo service nfsserver restart
   </code></pre>

1. **[A]** Instale los componentes de drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Cree una partición para los dispositivos drbd.

   Enumere todos los discos de datos disponibles.

   <pre><code>
   sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Salida de ejemplo
   
   ```
   lun0  lun1
   ```

   Cree particiones para cada disco de datos.

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Cree configuraciones de LVM

   Enumere todas las particiones disponibles.

   <pre><code>
   ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Salida de ejemplo
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Cree volúmenes LVM para cada partición.

   <pre><code>
   sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Configure drbd.

   <pre><code>
   sudo vi /etc/drbd.conf
   </code></pre>

   Asegúrese de que el archivo drbd.conf contenga las siguientes dos líneas:

   <pre><code>
   include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Cambie la configuración de drbd global.

   <pre><code>
   sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Agregue las siguientes entradas a la sección de red y controlador.

   <pre><code>
   global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             resync-rate 50M;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
        }
   }
   </code></pre>

1. **[A]** Cree los dispositivos drbd de NFS.

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Inserte la configuración del nuevo dispositivo drbd y salga

   <pre><code>
   resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Inserte la configuración del nuevo dispositivo drbd y salga

   <pre><code>
   resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Cree el dispositivo drbd e inícielo

   <pre><code>
   sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Omita la sincronización inicial

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Establezca el nodo principal

   <pre><code>
   sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Espere hasta que se sincronicen los nuevos dispositivos drbd

   <pre><code>
   sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Cree sistemas de archivos en los dispositivos drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Configure la detección de cerebro dividido de drbd.

   Al usar drbd para sincronizar los datos de un host a otro, puede producirse lo que se conoce como cerebro dividido. Un escenario de cerebro dividido es aquel en el que ambos nodos del clúster promovieron el drbd a principal y perdieron la sincronización. Aunque es una situación muy poco frecuente, debe tratar y resolver un cerebro dividido lo antes posible. Por lo tanto, es importante recibir una notificación cuando suceda una situación así.

   Lea la [documentación oficial de drbd](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) para saber cómo configurar una notificación de cerebro dividido.

   También es posible recuperarse automáticamente de un escenario de cerebro dividido. Para más información, lea las [directivas de recuperación automática de cerebro dividido](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration).
   
### <a name="configure-cluster-framework"></a>Configuración de la plataforma del clúster

1. **[1]**  Agregue los dispositivos drbd de NFS del sistema SAP NW1 a la configuración del clúster.

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=3 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=4 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=5 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=6 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=7 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=8 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> exportfs_<b>NW1</b>_sidsys \
     exportfs_<b>NW1</b>_sapmntsid exportfs_<b>NW1</b>_trans exportfs_<b>NW1</b>_ASCS \
     exportfs_<b>NW1</b>_ASCSERS exportfs_<b>NW1</b>_SCS exportfs_<b>NW1</b>_SCSERS \
     nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Agregue los dispositivos drbd de NFS del sistema SAP NW2 a la configuración del clúster.

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true   
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=9 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=10 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=11 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=12 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=13 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=14 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=15 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=16 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> exportfs_<b>NW2</b>_sidsys \
     exportfs_<b>NW2</b>_sapmntsid exportfs_<b>NW2</b>_trans exportfs_<b>NW2</b>_ASCS \
     exportfs_<b>NW2</b>_ASCSERS exportfs_<b>NW2</b>_SCS exportfs_<b>NW2</b>_SCSERS \
     nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Deshabilite el modo de mantenimiento.
   
   <pre><code>
   sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Pasos siguientes
* [Instalación de SAP ASCS y la base de datos](high-availability-guide-suse.md)
* [Planeación e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* Para obtener información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure (instancias grandes), vea [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md).
* Para información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure Virtual Machines, consulte [Alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure][sap-hana-ha]
