---
title: Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure | Microsoft Docs
description: Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure
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
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: 75615de523f1fba808f44fb1a1015138fb190edc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Existen dos opciones para configurar un clúster de Pacemaker en Azure. Se puede usar un agente de vallado, que se encarga de reiniciar un nodo con error a través de las API de Azure, o un dispositivo SBD.

El dispositivo SBD requiere una máquina virtual adicional que actúe como servidor de destino iSCSI y proporcione un dispositivo SBD. Este servidor de destino iSCSI, sin embargo, puede compartirse con otros clústeres de Pacemaker. La ventaja de usar un dispositivo SBD es que el tiempo de conmutación por error es menor y, si utiliza dispositivos SBD en un entorno local, no se necesitan cambios en el modo de operar con el clúster de Pacemaker. El vallado de SBD podrá seguir utilizando el agente de barrera de Azure como mecanismo de vallado de copia de seguridad en caso de que el servidor de destino iSCSI no esté disponible.

Si no desea invertir en otra máquina virtual, también puede utilizar el agente de barrera de Azure. El inconveniente es que una conmutación por error puede tardar entre 10 y 15 minutos si se produce un error en una detención de recursos o se interrumpe la comunicación entre los nodos del clúster.

![Información general de Pacemaker en SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>Vallado de SBD

Siga estos pasos si desea usar un dispositivo SBD como vallado.

### <a name="set-up-an-iscsi-target-server"></a>Configuración de un servidor de destino iSCSI

En primer lugar debe crear una máquina virtual de destino iSCSI, si aún no tiene una. Los servidores de destino iSCSI pueden compartirse con varios clústeres de Pacemaker.

1. Implemente una nueva máquina virtual SLES 12 SP1 o superior y conéctela a la máquina mediante SSH. No es necesario que la máquina sea grande. Un tamaño de máquina virtual como Standard_E2s_v3 o Standard_D2s_v3 es suficiente.

1. Actualice SLES.

   <pre><code>
   sudo zypper update
   </code></pre>

1. Instale paquetes de destino iSCSI.

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Habilite el servicio de destino iSCSI.

   <pre><code>   
   sudo systemctl enable target
   sudo systemctl enable targetcli
   sudo systemctl start target
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Creación de dispositivo iSCSI en el servidor de destino iSCSI.

Asocie un nuevo disco de datos a la máquina virtual de destino iSCSI que se puede usar para este clúster. Basta con que el disco de datos sea de 1 GB, y debe colocarse en una cuenta de Premium Storage o un disco administrado premium para beneficiarse de un [Acuerdo de Nivel de Servicio de una sola VM](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Ejecute el siguiente comando en la **VM de destino iSCSI** para crear un disco iSCSI para el nuevo clúster. En el ejemplo siguiente, **cl1** se usa para identificar el nuevo clúster y **prod-cl1-0** y **prod-cl1-1** son los nombres de host de los nodos del clúster. Sustitúyalos por los nombres de host de los nodos del clúster.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
sudo systemctl restart target
</code></pre>

### <a name="set-up-sbd-device"></a>Configuración de un dispositivo SBD

Conéctese al dispositivo iSCSI que se creó en el último paso desde el clúster.
Ejecute los siguientes comandos en los nodos del clúster nuevo que desea crear.
Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2.

1. **[A]**  Conexión a los dispositivos iSCSI

   En primer lugar, habilite los servicios iSCSI y SBD.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Cambio del nombre del iniciador en el primer nodo

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Cambie el contenido del archivo para que coincida con las ACL que utilizó al crear el dispositivo iSCSI en el servidor de destino iSCSI.

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Cambio del nombre del iniciador en el segundo nodo

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Cambie el contenido del archivo para que coincida con las ACL que utilizó al crear el dispositivo iSCSI en el servidor de destino iSCSI.

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** Reinicio del servicio iSCSI

   Reinicie ahora el servicio iSCSI para aplicar el cambio.
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Conecte los dispositivos iSCSI. En el ejemplo siguiente, 10.0.0.17 es la dirección IP del servidor de destino iSCSI y 3260 es el puerto predeterminado. <b>iqn.2006-04.cl1.local:cl1</b> es el nombre de destino que aparece al ejecutar el primer comando.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Asegúrese de que el dispositivo iSCSI está disponible y anote el nombre del dispositivo (en el ejemplo siguiente/dev/sde).

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Ahora, recupere el identificador del dispositivo iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   El comando presenta tres identificadores de dispositivo. Se recomienda utilizar el identificador que empieza por scsi-3, en el ejemplo anterior, esto es
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** Creación del dispositivo SBD

   Use el identificador de dispositivo del dispositivo iSCSI para crear un nuevo dispositivo SBD en el primer nodo del clúster.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** Adaptación de la configuración de SBD

   Abra el archivo de configuración de SBD.

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Cambie la propiedad del dispositivo SBD, habilite la integración de Pacemaker y cambie el modo de inicio del SBD.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Cree el archivo de configuración softdog.

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Ahora cargue el módulo.

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalación del clúster

Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2.

1. **[A]** Actualice SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Habilite el acceso de SSH

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** Habilite el acceso de SSH

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Habilite el acceso de SSH

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Instale la extensión de alta disponibilidad
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]** Configure la resolución nombres de host   

   Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instale el clúster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Agregue un nodo al clúster
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** Haga que la contraseña de hacluster coincida

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** Configure corosync para usar otro tipo de transporte y agregue nodelist. De lo contrario, el clúster no funciona.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

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
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Después, reinicie el servicio corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Cambie la configuración predeterminada de Pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Creación de un dispositivo STONITH

El dispositivo STONITH usa una entidad de servicio para la autorización de Microsoft Azure. Siga estos pasos para crear una entidad de servicio.

1. Vaya a <https://portal.azure.com>.
1. Abra la hoja Azure Active Directory  
   Vaya a Propiedades y anote el identificador del directorio. Se trata del **id. de inquilino**.
1. Haga clic en Registros de aplicaciones
1. Haga clic en Agregar
1. Escriba un nombre, seleccione el tipo de aplicación "Aplicación web o API", escriba una dirección URL de inicio de sesión (por ejemplo, http://localhost)) y haga clic en Crear.
1. La dirección URL de inicio de sesión no se usa y puede ser cualquier dirección URL válida
1. Seleccione la nueva aplicación y haga clic en las llaves de la pestaña Configuración
1. Escriba una descripción para la nueva clave, seleccione "Nunca expira" y haga clic en Guardar
1. Anote el valor. Se utiliza como **contraseña** para la entidad de servicio
1. Anote el identificador de la aplicación. Se utiliza como nombre de usuario (**Id. de inicio de sesión** en los pasos siguientes) de la entidad de servicio

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Creación de un rol personalizado para el agente de barrera

La entidad de servicio no tiene permiso para tener acceso a los recursos de Azure de forma predeterminada. Debe concedérselos para iniciar y detener (desasignar) todas las máquinas virtuales del clúster. Si no ha creado aún el rol personalizado, puede crearlo mediante [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell#create-a-custom-role) o la [CLI de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli#create-a-custom-role).

Utilice el siguiente contenido para el archivo de entrada. Debe adaptar el contenido a sus suscripciones; esto es, reemplace c276fc76-9cd4-44c9-99a7-4fd71546436e y e91d47c4-76f3-4271-a796-21b4ecfe3624 por los identificadores de su suscripción. Si solo tiene una suscripción, quite la segunda entrada en AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Asignación del rol personalizado a la entidad de servicio

Asigne el rol personalizado "Rol del agente de barrera de Linux" que se creó en el último capítulo a la entidad de servicio. Deje de utilizar el rol de propietario.

1. Vaya a https://portal.azure.com.
1. Abra la hoja Todos los recursos
1. Seleccione la máquina virtual del primer nodo de clúster.
1. Haga clic en Control de acceso (IAM)
1. Haga clic en Agregar
1. Seleccione el rol "Rol de agente de barrera de Linux".
1. Escriba el nombre de la aplicación que creó anteriormente
1. Haga clic en Aceptar

Repita los pasos anteriores para el segundo nodo de clúster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Cree los dispositivos STONITH

Después de editar los permisos para las máquinas virtuales, puede configurar los dispositivos STONITH en el clúster.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Creación de topología de barrera para el vallado de SBD

Si desea usar un dispositivo SBD, sigue siendo recomendable utilizar un agente de barrera de Azure como copia de seguridad en caso de que el servidor de destino iSCSI no esté disponible.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** Habilite el uso de un dispositivo STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Pasos siguientes
* [Planeación e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* Para información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure Virtual Machines, consulte [Alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure][sap-hana-ha]