---
title: "Solución de errores de Azure Backup: Se agotó el tiempo de espera de la subtarea de instantánea de máquina virtual | Microsoft Docs"
description: "Síntomas, causas y soluciones para los errores de Azure Backup relacionados con no poder comunicarse con el agente de máquina virtual para el estado de la instantánea: Se agotó el tiempo de espera de la subtarea de instantánea de máquina virtual"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 26ea5c6f867165a25dd5aecb01d0a0ce3b213a51
ms.openlocfilehash: 707d666eb6c23fb926c31711daddfb22979513bc

---

# <a name="troubleshoot-azure-backup-failure-snapshot-vm-sub-task-timed-out"></a>Solución de errores de Azure Backup: Se agotó el tiempo de espera de la subtarea de instantánea de máquina virtual
## <a name="summary"></a>Resumen
Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las cuatro condiciones puede impedir que la instantánea se desencadene, lo que a su vez puede dar lugar a errores de Backup. Este artículo proporciona pasos para solucionar problemas que le ayudan a resolver errores de Backup relacionados con los errores de tiempo de espera de instantánea agotado.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Síntoma
Se produce un error en Azure Backup para la máquina virtual de infraestructura como servicio (IaaS), devuelve el siguiente mensaje de error en los detalles del error de trabajo en [Azure Portal](https://portal.azure.com/): "No se pudo comunicar con el agente de máquina virtual para obtener el estado de instantánea. Se agotó el tiempo de espera de la subtarea de instantánea de máquina virtual".

## <a name="cause-1-the-vm-has-no-internet-access"></a>Causa 1: La máquina virtual no tiene acceso a Internet
Según los requisitos de implementación, la máquina virtual no tiene ningún acceso a Internet o tiene restricciones vigentes que impiden el acceso a la infraestructura de Azure.

Para poder funcionar correctamente, la extensión de copia de seguridad requiere conectividad a las direcciones IP públicas de Azure. La extensión envía comandos a un punto de conexión de Azure Storage (dirección URL de HTTP) para administrar las instantáneas de la máquina virtual. Si la extensión no tiene acceso a Internet, se produce un error en Backup.

### <a name="solution"></a>Solución
Para solucionar este problema, pruebe uno de los métodos siguientes:
#### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Permitir acceso a los intervalos IP del centro de datos de Azure

1. Obtenga la lista de [IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) a la que se va a permitir el acceso.
2. Desbloquee las direcciones IP mediante la ejecución del cmdlet **New-NetRoute** en la máquina virtual de Azure en una ventana de PowerShell con privilegios elevados. Ejecute el cmdlet como administrador.
3. Para permitir el acceso a las direcciones IP, agregue reglas al grupo de seguridad de red, si dispone de uno.

#### <a name="create-a-path-for-http-traffic-to-flow"></a>Crear una ruta de acceso para el flujo del tráfico HTTP

1. Si tiene alguna restricción de red implementada (por ejemplo, un grupo de seguridad de red), implemente un servidor proxy HTTP para enrutar el tráfico.
2. Para permitir el acceso a Internet desde el servidor proxy HTTP, agregue las reglas al grupo de seguridad de red, si dispone de uno.

Para aprender a cómo configurar un proxy HTTP para las copias de seguridad de la máquina virtual, consulte [Preparación del entorno de copia de seguridad de Azure Virtual Machines](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)

### <a name="solution"></a>Solución
La mayoría de los errores relacionados con el agente o la extensión de máquinas virtuales de Linux están provocados por problemas que afectan a un agente VM obsoleto. Para solucionar este problema, siga estas directrices generales:

1. Siga las instrucciones para [actualizar el agente de máquina virtual Linux ](../virtual-machines/virtual-machines-linux-update-agent.md).

 >[!NOTE]
 >Se *recomienda encarecidamente* actualizar el agente solo a través de un repositorio de distribución. No recomendamos descargar el código de agente desde GitHub directamente y actualizarlo. Si el último agente no está disponible para su distribución, póngase en contacto con el soporte técnico de distribución para obtener instrucciones sobre cómo instalarlo. Para buscar el agente más reciente, vaya a la página del [agente Linux de Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) en el repositorio de GitHub.

2. Asegúrese de que el agente de Azure se ejecuta en la máquina virtual mediante la ejecución del comando siguiente: `ps -e`.

 Si el proceso no se está ejecutando, reinícielo mediante los siguientes comandos:

 * Para Ubuntu: `service walinuxagent start`
 * Para otras distribuciones: `service waagent start`

3. [Configure el agente de reinicio automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Ejecute una nueva copia de seguridad de prueba. Si el error persiste, recopile los registros siguientes de la máquina virtual del cliente:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Si se requiere el registro detallado para waagent, siga estos pasos:

1. En el archivo /etc/waagent.conf, localice la línea siguiente: **Enable verbose logging (y|n)** (Habilitar registro detallado [s/n]).
2. Cambie el valor de **Logs.Verbose** de *n* a *y*.
3. Guarde los cambios y reinicie waagent siguiendo los pasos anteriores de esta sección.

## <a name="cause-3-the-backup-extension-fails-to-update-or-load"></a>Causa 3: No se puede actualizar ni cargar la extensión de copia de seguridad.
Si no se pueden cargar las extensiones, Backup producirá un error porque no se puede tomar una instantánea.

### <a name="solution"></a>Solución

Para invitados de Windows:

Compruebe que el servicio iaasvmprovider está habilitado y tiene un tipo de inicio *automático*. Si el servicio no está configurado de esa forma, habilítelo para determinar si se realiza correctamente la siguiente copia de seguridad.

Para invitados de Linux:

La versión más reciente de VMSnapshot para Linux (extensión utilizada por Backup) es 1.0.91.0.

Si todavía no se puede actualizar ni cargar la extensión de copia de seguridad, puede forzar a la extensión VMSnapshot para que se vuelva a cargar mediante la desinstalación de la extensión. El siguiente intento de copia de seguridad volverá a cargar la extensión.

Para desinstalar la extensión, haga lo siguiente:

1. Vaya al [Portal de Azure](https://portal.azure.com/).
2. Busque la máquina virtual que tiene problemas de copia de seguridad.
3. Haga clic en **Configuración**.
4. Haga clic en **Extensiones**.
5. Haga clic en **Extensión de Vmsnapshot**.
6. Hacer clic en **Desinstalar**.  

Este procedimiento hace que la extensión se vuelva a instalar durante la siguiente copia de seguridad.

## <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 4: No se puede recuperar el estado de las instantáneas o no se pueden sacar las mismas.
La copia de seguridad de máquina virtual se basa en la emisión de comandos de instantánea para la cuenta del almacenamiento subyacente. Backup puede producir un error porque no tiene ningún acceso a la cuenta de almacenamiento o porque se retrasa la ejecución de la tarea de instantáneas.

### <a name="solution"></a>Solución
Las siguientes condiciones pueden producir un error en la tarea de instantáneas.

| Causa | Solución |
| --- | --- |
| La máquina virtual tiene una copia de seguridad de SQL Server configurada. | De forma predeterminada, la copia de seguridad de la máquina virtual ejecuta una copia de seguridad completa de VSS en máquinas virtuales Windows. En las máquinas virtuales que se ejecutan en servidores basados en SQL Server y en las que se configura la copia de seguridad de SQL Server, se pueden producir retrasos en la ejecución de instantáneas.<br><br>Si experimenta un error de Backup debido a problemas de instantáneas, establezca la siguiente clave del Registro:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| El estado de la máquina virtual se notifica incorrectamente porque la máquina virtual está apagada en RDP. | Si ha apagado la máquina virtual en Protocolo de escritorio remoto (RDP), compruebe el portal para determinar si ese estado de la máquina virtual es correcto. Si no es así, apague la máquina virtual en el portal mediante la opción **Apagar** en el panel de la máquina virtual. |
| Muchas máquinas virtuales del mismo servicio en la nube están configuradas para efectuar la copia de seguridad al mismo tiempo. | Se recomienda propagar las programaciones de copia de seguridad para las máquinas virtuales del mismo servicio en la nube. |
| La máquina virtual se está ejecutando con un uso elevado de la CPU o de la memoria. | Si la máquina virtual se está ejecutando con un uso elevado de CPU (más del 90 %) o un uso elevado de memoria, la tarea de instantáneas se pone en cola y se retrasa y, en ocasiones, se agota el tiempo de espera. Pruebe la copia de seguridad a petición en estas situaciones. |
| La máquina virtual no puede obtener la dirección de host o del tejido desde DHCP. | DHCP debe estar habilitado dentro del invitado para que la copia de seguridad de la máquina virtual de IaaS funcione.  Si la máquina virtual no puede obtener la dirección de host o del tejido de la respuesta 245 de DHCP, no podrá descargar ni ejecutar ninguna extensión. Si necesita una dirección IP privada estática, debe configurarla a través de la plataforma. La opción DHCP dentro de la máquina virtual debe continuar habilitada. Para más información, consulte el artículo sobre el [establecimiento de una dirección IP privada interna estática](../virtual-network/virtual-networks-reserved-private-ip.md). |



<!--HONumber=Jan17_HO4-->


