---
title: "Solución del error de Azure Backup: no está disponible el estado del agente invitado | Documentos de Microsoft"
description: "Síntomas, causas y soluciones de otros errores de Azure Backup relacionados con el agente, la extensión y los discos"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Azure Backup; Agente de máquina virtual; Conectividad de red;"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli;markgal;
ms.openlocfilehash: f3195fa83479986a3e605abce618c78bcdb64dac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Solución de errores de Azure Backup: problemas con el agente o la extensión

En este artículo se proporcionan los pasos necesarios para ayudarle a resolver errores de Backup relacionados con los errores de comunicación con el agente de la máquina virtual y la extensión.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>El agente de máquina virtual no se puede comunicar con Azure Backup
Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con el agente de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene, lo que a su vez puede dar lugar a errores de Backup. Siga los pasos para la solución de problemas que aparecen a continuación en el orden dado y reintente la operación.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 1: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 2: [El agente está instalado en la máquina virtual, pero no responde (para máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 3: [El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 4: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 5: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Error de la operación de instantánea debido a que no hay conectividad de red en la máquina virtual
Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene, lo que a su vez puede dar lugar a errores de Backup. Siga los pasos para la solución de problemas que aparecen a continuación en el orden dado y reintente la operación.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 1: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 2: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 3: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>Error en la operación de extensión de VMSnapshot

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene, lo que a su vez puede dar lugar a errores de Backup. Siga los pasos para la solución de problemas que aparecen a continuación en el orden dado y reintente la operación.
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 1: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 2: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 3: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 4: [El agente está instalado en la máquina virtual, pero no responde (para máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 5: [El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>No se puede realizar la operación porque el agente de la máquina virtual no está respondiendo

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene, lo que a su vez puede dar lugar a errores de Backup. Siga los pasos para la solución de problemas que aparecen a continuación en el orden dado y reintente la operación.
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 1: [El agente está instalado en la máquina virtual, pero no responde (para máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: [El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 3: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>Error interno en la copia de seguridad. Reintente la operación en unos minutos

Después de registrar y programar una máquina virtual para el servicio de Azure Backup, Backup inicia el trabajo al comunicarse con la extensión de copia de seguridad de la máquina virtual para sacar una instantánea de un momento dado. Cualquiera de las condiciones siguientes puede impedir que la instantánea se desencadene, lo que a su vez puede dar lugar a errores de Backup. Siga los pasos para la solución de problemas que aparecen a continuación en el orden dado y reintente la operación.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 1: [La máquina virtual no tiene acceso a Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 2: [El agente está instalado en la máquina virtual, pero no responde (para máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 3: [El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 4: [No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 5: [La extensión de la copia de seguridad no se puede actualizar ni cargar](#the-backup-extension-fails-to-update-or-load)

## <a name="the-specified-disk-configuration-is-not-supported"></a>No se admite la configuración de disco especificada

Azure Backup no admite actualmente tamaños de disco [mayores que 1023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Si tiene discos de más de 1 TB, [conecte discos nuevos](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) de menos de 1 TB. <br>
- A continuación, copie los datos del disco de más de 1 TB en los discos recién creados de tamaño inferior a 1 TB. <br>
- Asegúrese de que se han copiado todos los datos y quite los discos de más de 1 TB.
- Inicie la copia de seguridad.

## <a name="causes-and-solutions"></a>Causas y soluciones

### <a name="the-vm-has-no-internet-access"></a>La máquina virtual no tiene acceso a Internet
Según los requisitos de implementación, la máquina virtual no tiene ningún acceso a Internet o tiene restricciones vigentes que impiden el acceso a la infraestructura de Azure.

Para poder funcionar correctamente, la extensión de copia de seguridad requiere conectividad a las direcciones IP públicas de Azure. La extensión envía comandos a un punto de conexión de Azure Storage (dirección URL de HTTP) para administrar las instantáneas de la máquina virtual. Si la extensión no tiene acceso a Internet, se produce un error en Backup.

####  <a name="solution"></a>Solución
Para solucionar este problema, pruebe uno de los métodos siguientes:
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Permitir acceso a los intervalos IP del centro de datos de Azure

1. Obtenga la lista de [IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) a la que se va a permitir el acceso.
2. Desbloquee las direcciones IP mediante la ejecución del cmdlet **New-NetRoute** en la máquina virtual de Azure en una ventana de PowerShell con privilegios elevados. Ejecute el cmdlet como administrador.
3. Para permitir el acceso a las direcciones IP, agregue reglas al grupo de seguridad de red, si dispone de uno.

##### <a name="create-a-path-for-http-traffic-to-flow"></a>Crear una ruta de acceso para el flujo del tráfico HTTP

1. Si tiene alguna restricción de red implementada (por ejemplo, un grupo de seguridad de red), implemente un servidor proxy HTTP para enrutar el tráfico.
2. Para permitir el acceso a Internet desde el servidor proxy HTTP, agregue las reglas al grupo de seguridad de red, si dispone de uno.

Para aprender a cómo configurar un proxy HTTP para las copias de seguridad de la máquina virtual, consulte [Preparación del entorno de copia de seguridad de Azure Virtual Machines](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

En caso de que use Managed Disks, puede que necesite un puerto adicional (8443) que se abra en los firewalls.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>El agente está instalado en la máquina virtual, pero no responde (para máquinas virtuales Windows)

#### <a name="solution"></a>Solución
Es posible que el agente de máquina virtual se haya dañado o que el servicio se haya detenido. Si vuelve a instalar al agente tendrá la versión más reciente y se reiniciará la comunicación.

1. Compruebe si el servicio Windows Guest Agent se está ejecutando en los servicios (services.msc) de la máquina virtual. Intente reiniciar el servicio Windows Guest Agent e iniciar la copia de seguridad<br>
2. Si no lo ve en los servicios, compruebe en Programas y características si el servicio Windows Guest Agent está instalado.
4. Si lo ve en Programas y características, desinstale el servicio.
5. Descargue e instale la [versión más reciente del agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Para completar la instalación, necesita privilegios de administrador.
6. A partir de ese momento debería poder ver Windows Guest Agent en los servicios
7. Pruebe a ejecutar una copia de seguridad ad hoc o a petición, para lo que debe hacer clic en "Realizar copia de seguridad ahora" en el portal.

Compruebe también que la máquina virtual tiene **[instalado .NET 4.5 en el sistema](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)**. ya que se requiere para que el agente de máquina virtual se comunique con el servicio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)

#### <a name="solution"></a>Solución
La mayoría de los errores relacionados con el agente o la extensión de máquinas virtuales de Linux están provocados por problemas que afectan a un agente VM obsoleto. Para solucionar este problema, siga estas directrices generales:

1. Siga las instrucciones para [actualizar el agente de máquina virtual Linux ](../virtual-machines/linux/update-agent.md).

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
2. Cambie el valor de **Logs.Verbose** de *n* a  *y* .
3. Guarde los cambios y reinicie waagent siguiendo los pasos anteriores de esta sección.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>No se puede recuperar el estado de las instantáneas o no se pueden tomar instantáneas
La copia de seguridad de máquina virtual se basa en la emisión de comandos de instantánea para la cuenta del almacenamiento subyacente. Backup puede producir un error porque no tiene ningún acceso a la cuenta de almacenamiento o porque se retrasa la ejecución de la tarea de instantáneas.

#### <a name="solution"></a>Solución
Las siguientes condiciones pueden producir un error en la tarea de instantáneas.

| Causa | Solución |
| --- | --- |
| La máquina virtual tiene una copia de seguridad de SQL Server configurada. | De forma predeterminada, la copia de seguridad de la máquina virtual ejecuta una copia de seguridad completa de VSS en máquinas virtuales Windows. En las máquinas virtuales que se ejecutan en servidores basados en SQL Server y en las que se configura la copia de seguridad de SQL Server, se pueden producir retrasos en la ejecución de instantáneas.<br><br>Si experimenta un error de Backup debido a problemas de instantáneas, establezca la siguiente clave del Registro:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| El estado de la máquina virtual se notifica incorrectamente porque la máquina virtual está apagada en RDP. | Si ha apagado la máquina virtual en Protocolo de escritorio remoto (RDP), compruebe el portal para determinar si ese estado de la máquina virtual es correcto. Si no es así, apague la máquina virtual en el portal mediante la opción **Apagar** en el panel de la máquina virtual. |
| Muchas máquinas virtuales del mismo servicio en la nube están configuradas para efectuar la copia de seguridad al mismo tiempo. | Se recomienda propagar las programaciones de copia de seguridad para las máquinas virtuales del mismo servicio en la nube. |
| La máquina virtual se está ejecutando con un uso elevado de la CPU o de la memoria. | Si la máquina virtual se está ejecutando con un uso elevado de CPU (más del 90 %) o un uso elevado de memoria, la tarea de instantáneas se pone en cola y se retrasa y, en ocasiones, se agota el tiempo de espera. Pruebe la copia de seguridad a petición en estas situaciones. |
| La máquina virtual no puede obtener la dirección de host o del tejido desde DHCP. | DHCP debe estar habilitado dentro del invitado para que la copia de seguridad de la máquina virtual de IaaS funcione.  Si la máquina virtual no puede obtener la dirección de host o del tejido de la respuesta 245 de DHCP, no podrá descargar ni ejecutar ninguna extensión. Si necesita una dirección IP privada estática, debe configurarla a través de la plataforma. La opción DHCP dentro de la máquina virtual debe continuar habilitada. Para más información, consulte el artículo sobre el [establecimiento de una dirección IP privada interna estática](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>No se puede actualizar ni cargar la extensión de copia de seguridad
Si no se pueden cargar las extensiones, Backup producirá un error porque no se puede tomar una instantánea.

#### <a name="solution"></a>Solución

**Para invitados de Windows:** compruebe que el servicio iaasvmprovider está habilitado y tiene un tipo de inicio *automático*. Si el servicio no está configurado de esa forma, habilítelo para determinar si se realiza correctamente la siguiente copia de seguridad.

**Para agentes de Linux:** compruebe que la versión más reciente de VMSnapshot para Linux (extensión utilizada por Backup) es 1.0.91.0.<br>


Si todavía no se puede actualizar ni cargar la extensión de copia de seguridad, puede forzar a la extensión VMSnapshot para que se vuelva a cargar mediante la desinstalación de la extensión. El siguiente intento de copia de seguridad volverá a cargar la extensión.

Para desinstalar la extensión, haga lo siguiente:

1. Vaya al [Portal de Azure](https://portal.azure.com/).
2. Busque la máquina virtual que tiene problemas de copia de seguridad.
3. Haga clic en **Configuración**.
4. Haga clic en **Extensiones**.
5. Haga clic en **Extensión de Vmsnapshot**.
6. Hacer clic en **Desinstalar**.

Este procedimiento hace que la extensión se vuelva a instalar durante la siguiente copia de seguridad.

