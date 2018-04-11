---
title: 'Solución del error de Azure Backup: no está disponible el estado del agente invitado | Documentos de Microsoft'
description: Síntomas, causas y soluciones de errores de Azure Backup relacionados con el agente, la extensión y los discos.
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
keywords: Azure Backup; Agente de máquina virtual; Conectividad de red;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: 81678f6a8659ffb763ebfe418098e510c73f6ae0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solución de errores de Azure Backup: problemas con el agente o la extensión

En este artículo se proporcionan los pasos de solución de problemas que pueden ayudar a resolver los errores de Azure Backup relacionados con la comunicación con el agente y la extensión de máquina virtual.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>El agente de máquina virtual no se puede comunicar con Azure Backup

Mensaje de error: "El agente de máquina virtual no se puede comunicar con Azure Backup"

Después de registrar y programar una máquina virtual para el servicio Backup, dicho servicio se comunica con el agente de la máquina virtual para sacar una instantánea de un momento dado e inicia el trabajo. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene. Cuando una instantánea no se desencadena, se puede producir un error en la copia de seguridad. Realice los pasos de solución de problemas siguientes en el orden indicado y, a continuación, vuelva a intentar la operación:

**Causa 1: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 3: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Causa 5: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Se produce un error en la operación de instantánea porque la máquina virtual no está conectada a la red

Mensaje de error: "Error de la operación de instantánea debido a que no hay conectividad de red en la máquina virtual"

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene. Si la instantánea no se desencadena, se podría producir un error en la copia de seguridad. Realice los pasos de solución de problemas siguientes en el orden indicado y, a continuación, vuelva a intentar la operación:    
**Causa 1: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 3: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>Se produce un error en la operación de la extensión VMSnapshot

Mensaje de error: "Error en la operación de extensión VMSnapshot".

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene. Si la instantánea no se desencadena, se podría producir un error en la copia de seguridad. Realice los pasos de solución de problemas siguientes en el orden indicado y, a continuación, vuelva a intentar la operación:  
**Causa 1: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Se produce un error en la copia de seguridad porque el agente de máquina virtual no responde

Mensaje de error: "No se puede realizar la operación: el agente de máquina virtual no responde"

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene. Si la instantánea no se desencadena, se podría producir un error en la copia de seguridad. Realice los pasos de solución de problemas siguientes en el orden indicado y, a continuación, vuelva a intentar la operación:  
**Causa 1: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Se produce un error interno en la copia de seguridad

Mensaje de error: "Error interno al realizar la copia de seguridad. Intente volver a realizar la operación en unos minutos"

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene. Si la instantánea no se desencadena, se podría producir un error en la copia de seguridad. Realice los pasos de solución de problemas siguientes en el orden indicado y, a continuación, vuelva a intentar la operación:  
**Causa 1: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 5: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)**  
**Causa 6: [El servicio de copia de seguridad no tiene permiso para eliminar los puntos de restauración antiguos debido a un bloqueo del grupo de recursos](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>Causas y soluciones

### <a name="the-vm-has-no-internet-access"></a>La máquina virtual no tiene acceso a Internet.
Según el requisito de implementación, la máquina virtual no tiene acceso a Internet. O bien, podría tener restricciones que impiden el acceso a la infraestructura de Azure.

Para poder funcionar correctamente, la extensión de Backup requiere conectividad a las direcciones IP públicas de Azure. La extensión envía comandos a un punto de conexión de Azure Storage (dirección URL de HTTP) para administrar las instantáneas de la máquina virtual. Si la extensión no tiene acceso a la red Internet pública, se produce un error en la copia de seguridad.

####  <a name="solution"></a>Solución
Para solucionar este problema, pruebe uno de los métodos siguientes:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Permitir el acceso al almacenamiento de Azure que corresponda a la región.

Puede usar [etiquetas de servicio](../virtual-network/security-overview.md#service-tags) para permitir conexiones al almacenamiento en la región específica. Asegúrese de que la regla que permite el acceso a la cuenta de almacenamiento tenga mayor prioridad que la que bloquea el acceso a Internet. 

![Grupo de seguridad de red con etiquetas de almacenamiento para una región](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Las etiquetas de servicio de almacenamiento se encuentran en versión preliminar. Solo están disponibles en determinadas regiones. Para ver la lista de regiones, consulte el apartado [Etiquetas de servicio para almacenamiento](../virtual-network/security-overview.md#service-tags).

##### <a name="create-a-path-for-http-traffic"></a>Crear una ruta de acceso para el tráfico HTTP

1. Si tiene alguna restricción de red implementada (por ejemplo, un grupo de seguridad de red), implemente un servidor proxy HTTP para enrutar el tráfico.
2. Para permitir el acceso a Internet desde el servidor proxy HTTP, agregue las reglas al grupo de seguridad de red, si dispone de uno.

Para aprender a cómo configurar un proxy HTTP para las copias de seguridad de la máquina virtual, consulte [Preparación del entorno de copia de seguridad de Azure Virtual Machines](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Si usa discos administrados de Azure, necesitará abrir otro puerto (8443) en los firewalls.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)

#### <a name="solution"></a>Solución
Es posible que el agente de máquina virtual se haya dañado o que el servicio se haya detenido. Puede intentar volver a instalar al agente de máquina virtual para obtener la versión más reciente. O bien, intente restablecer la comunicación con el servicio.

1. Determine si el servicio Windows Guest Agent se ejecuta en los servicios de máquina virtual (services.msc). Intente reiniciar el servicio Windows Guest Agent e inicie la copia de seguridad.    
2. Si el servicio Windows Guest Agent no se muestra en los servicios, vaya a Panel de control y seleccione **Programas y características** para determinar si dicho servicio está instalado.
4. Si aparece en **Programas y características**, desinstálelo.
5. Descargue e instale la [versión más reciente del MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Debe tener derechos de administrador para completar la instalación.
6. Compruebe que el servicio Windows Guest Agent aparece en los servicios.
7. Ejecute un trabajo de copia de seguridad a petición: 
    * En el portal, seleccione **Realizar copia de seguridad ahora**.

Además, compruebe que [Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) está instalado en la máquina virtual, ya que se requiere para que el agente de máquina virtual se comunique con el servicio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)

#### <a name="solution"></a>Solución
La mayoría de los errores relacionados con el agente o la extensión de máquinas virtuales de Linux están provocados por problemas que afectan a un agente VM obsoleto. Para solucionar este problema, siga estas directrices generales:

1. Siga las instrucciones para [actualizar el agente de máquina virtual Linux ](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Se *recomienda encarecidamente* actualizar el agente solo a través de un repositorio de distribución. No recomendamos descargar el código de agente desde GitHub directamente y actualizarlo. Si el último agente no está disponible para su distribución, póngase en contacto con el soporte técnico de distribución para obtener instrucciones sobre cómo instalarlo. Para buscar el agente más reciente, vaya a la página del [agente Linux de Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) en el repositorio de GitHub.

2. Asegúrese de que el agente de Azure se ejecuta en la máquina virtual mediante la ejecución del comando siguiente: `ps -e`.

 Si el proceso no se está ejecutando, reinícielo mediante los siguientes comandos:

 * Para Ubuntu: `service walinuxagent start`
 * Para otras distribuciones: `service waagent start`

3. [Configure el agente de reinicio automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Ejecute una nueva copia de seguridad de prueba. Si el error persiste, recopile los registros siguientes de la máquina virtual:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Si se requiere el registro detallado para waagent, siga estos pasos:

1. En el archivo /etc/waagent.conf, localice la línea siguiente: **Enable verbose logging (y|n)** (Habilitar registro detallado [s/n]).
2. Cambie el valor de **Logs.Verbose** de *n* a *y*.
3. Guarde el cambio y, a continuación, reinicie waagent siguiendo los pasos descritos anteriormente en esta sección.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas
La copia de seguridad de máquina virtual se basa en la emisión de comandos de instantánea para la cuenta del almacenamiento subyacente. La copia de seguridad puede producir un error porque no tiene ningún acceso a la cuenta de almacenamiento o porque se retrasa la ejecución de la tarea de instantáneas.

#### <a name="solution"></a>Solución
Las siguientes condiciones podrían hacer que la tarea de instantáneas no se realizara:

| Causa | Solución |
| --- | --- |
| El estado de la máquina virtual no se notifica correctamente porque la máquina virtual está apagada en el Protocolo de escritorio remoto (RDP). | Si ha apagado la máquina virtual en RDP, compruebe el portal para determinar si ese estado de la máquina virtual es correcto. Si no es así, apague la máquina virtual en el portal mediante la opción **Apagar** en el panel de la máquina virtual. |
| La máquina virtual no puede obtener la dirección de host o de tejido de DHCP. | DHCP debe estar habilitado dentro del invitado para que la copia de seguridad de la máquina virtual de IaaS funcione. Si la máquina virtual no puede obtener la dirección de host o de tejido de la respuesta 245 de DHCP, no podrá descargar ni ejecutar ninguna extensión. Si necesita una dirección IP privada estática, configúrela mediante la plataforma. La opción DHCP dentro de la máquina virtual debe continuar habilitada. Para más información, consulte [Establecimiento de una dirección IP privada interna estática](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>No se puede actualizar ni cargar la extensión de copia de seguridad
Si no se pueden cargar las extensiones, no se puede realizar la copia de seguridad porque no se puede realizar una instantánea.

#### <a name="solution"></a>Solución

Desinstale la extensión para obligar a la extensión VMSnapshot a cargarse de nuevo. El siguiente intento de copia de seguridad vuelve a cargar la extensión.

Para desinstalar la extensión, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), vaya a la máquina virtual que experimenta los errores de copia de seguridad.
2. Seleccione **Configuración**.
3. Seleccione **Extensiones**.
4. Seleccione **Extensión Vmsnapshot**.
5. Seleccione **Desinstalar**.

La realización de estos pasos hace que se vuelva a instalar la extensión durante la siguiente copia de seguridad.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>El servicio Backup no tiene permiso para eliminar los puntos de restauración antiguos debido a un bloqueo del grupo de recursos.
Este problema es específico de máquinas virtuales administradas en las que el usuario bloquea el grupo de recursos. En este caso, el servicio de copia de seguridad no puede eliminar los puntos de restauración más antiguos. Como hay un límite de 18 puntos de restauración, las nuevas copias de seguridad comienzan a generar error.

#### <a name="solution"></a>Solución

Para resolver el problema, realice los pasos siguientes para quitar la colección de puntos de restauración: <br>
 
1. Quite el bloqueo en el grupo de recursos en el que se encuentra la máquina virtual. 
2. Instale ARMClient mediante Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Inicie sesión en ARMClient. <br>
    `.\armclient.exe login`
4. Obtenga la colección de puntos de restauración que corresponde a la máquina virtual: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Ejemplo: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Elimine la colección de puntos de restauración: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. La próxima copia de seguridad programada crea automáticamente una colección de puntos de restauración y nuevos puntos de restauración.

 
Si bloquea de nuevo el grupo de recursos, el problema se vuelve a producir. 

