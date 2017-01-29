---
title: "Se ha producido un error en la copia de seguridad de la máquina virtual de Azure: no se pudo comunicar con el agente de máquina virtual para el estado de la instantánea: la subtarea de la máquina virtual de la instantánea ha agotado el tiempo de espera | Microsoft Docs"
description: "Causas de los síntomas y soluciones para los errores de copia de seguridad de máquina virtual de Azure relacionados con no poder comunicarse con el agente de VM para el estado de la instantánea. Error de tiempo de espera de la subtarea de máquina virtual de instantánea"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 5cd91d74ea09e4fc77437a310f39b769983bd123
ms.openlocfilehash: 005d6b220135302a85495d0840e545c13feb0705


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Se ha producido un error en la copia de seguridad de la máquina virtual de Azure: no se pudo comunicar con el agente de máquina virtual para el estado de la instantánea: la subtarea de la máquina virtual de la instantánea agotó el tiempo de espera
## <a name="summary"></a>Resumen
Después de registrar y programar una máquina virtual (VM) de Copia de seguridad de Azure, este servicio inicia el trabajo de copia de seguridad a la hora programada comunicándose con la extensión de copia de seguridad de la máquina virtual para tomar una instantánea de un momento específico. Hay condiciones que pueden impedir que la instantánea se desencadene lo cual provocará un error de copia de seguridad. Este artículo proporciona pasos para solucionar problemas relacionados con errores de copia de seguridad de máquina virtual de Azure relacionados a su vez con el error de tiempo de espera de instantánea agotado.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Síntoma
La máquina virtual del servicio Microsoft Azure Backup para una infraestructura como servicio (IaaS) da error y devuelve el siguiente mensaje en la ventana de detalles de error de trabajo de [Azure Portal](https://portal.azure.com/):

**No se pudo comunicar con el agente de máquina virtual para el estado de la instantánea: la subtarea de la máquina virtual de la instantánea agotó el tiempo de espera.**

## <a name="cause"></a>Causa
Hay cuatro causas comunes de este error:

* La máquina virtual no tiene acceso a Internet.
* El agente de máquina virtual de Microsoft Azure instalado en la máquina virtual está anticuado (en el caso de máquinas virtuales de Linux).
* No se puede actualizar ni cargar la extensión de copia de seguridad.
* No se puede recuperar el estado de las instantáneas o no se pueden tomar las mismas.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: La máquina virtual no tiene acceso a Internet
Según los requisitos de implementación, la máquina virtual no tiene ningún acceso a Internet o tiene restricciones vigentes que impiden el acceso a la infraestructura de Azure.

La extensión de copia de seguridad requiere conectividad a las direcciones IP públicas de Azure para poder funcionar correctamente. Esto se debe a que envía comandos a un punto de conexión de Almacenamiento de Azure (dirección URL de HTTP) para administrar las instantáneas de la máquina virtual. Si la extensión no tiene acceso a Internet, se produce un error en la copia de seguridad.

### <a name="solution"></a>Solución
En este escenario, utilice uno de los métodos siguientes para resolver el problema:

* Preparar una lista blanca con los intervalos IP del centro de datos de Azure.
* Crear una ruta de acceso para el flujo del tráfico HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Para preparar una lista blanca con los intervalos IP del centro de datos de Azure
1. Obtenga la lista de [IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) que van a formar parte de la lista de direcciones IP aprobadas.
2. Desbloquee las direcciones IP mediante el cmdlet New-NetRoute. Ejecute este cmdlet en la máquina virtual de Azure, en una ventana de PowerShell con privilegios elevados (realice la ejecución como administrador).
3. Agregue reglas al grupo de seguridad de red (NSG), si dispone de uno, para permitir el acceso a las direcciones IP.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Para crear una ruta de acceso para el flujo del tráfico HTTP
1. Si tiene alguna restricción de red vigente (por ejemplo, un grupo de seguridad de red), implemente un servidor proxy HTTP para enrutar el tráfico.
2. Agregue reglas al grupo de seguridad de red para permitir el acceso a Internet desde el proxy HTTP.

Aprenda a configurar [un proxy HTTP para las copias de seguridad de máquinas virtuales](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: El agente de máquina virtual de Microsoft Azure instalado en la máquina virtual está anticuado (en el caso de máquinas virtuales de Linux)
### <a name="solution"></a>Solución
La mayoría de los errores relacionados con el agente o la extensión de máquinas virtuales de Linux están provocados por problemas que afectan a un agente VM antiguo. Como norma general, los primeros pasos para solucionar este problema son los siguientes:

1. Siga las instrucciones proporcionadas en [Actualización del agente de máquina virtual Linux](../virtual-machines/virtual-machines-linux-update-agent.md).
Se **recomienda fehacientemente** actualizar el agente solo a través del repositorio de distribución. No recomendamos descargar el código de agente desde github directamente y actualizarlo. Si el último agente no está disponible para su distribución, vaya al soporte técnico de distribución para obtener instrucciones sobre cómo instalar el agente más reciente. Puede comprobar la información del [agente Linux de Windows Azure](https://github.com/Azure/WALinuxAgent/releases) más reciente en el repositorio de github. 
2. Asegúrese de que el agente de Azure se ejecuta en la máquina virtual. Para ello, ejecute el siguiente comando: ```ps -e```
   
    Si no se está ejecutando este proceso, utilice los siguientes comandos para reiniciarlo.
   
    Para Ubuntu: ```service walinuxagent start```
   
    Para otras distribuciones: '''service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Jan17_HO1-->


