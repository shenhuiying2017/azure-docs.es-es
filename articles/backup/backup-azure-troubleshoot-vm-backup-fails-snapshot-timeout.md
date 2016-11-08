---
title: 'Se ha producido un error en la copia de seguridad de la máquina virtual de Azure: no se pudo comunicar con el agente de máquina virtual para el estado de la instantánea: la subtarea de la máquina virtual de la instantánea agotó el tiempo de espera | Microsoft Docs'
description: Causas de los síntomas y soluciones para los errores de copia de seguridad de máquina virtual de Azure relacionados con no poder comunicarse con el agente de VM para el estado de la instantánea. Error de tiempo de espera de la subtarea de máquina virtual de instantánea
services: backup
documentationcenter: ''
author: genlin
manager: jwhit
editor: ''

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2016
ms.author: jimpark; markgal;genli

---
# Se ha producido un error en la copia de seguridad de la máquina virtual de Azure: no se pudo comunicar con el agente de máquina virtual para el estado de la instantánea: la subtarea de la máquina virtual de la instantánea agotó el tiempo de espera
## Resumen
Después de registrar y programar una máquina virtual (VM) de Copia de seguridad de Azure, este servicio inicia el trabajo de copia de seguridad a la hora programada comunicándose con la extensión de copia de seguridad de la máquina virtual para tomar una instantánea de un momento específico. Hay condiciones que pueden impedir que la instantánea se desencadene lo cual provocará un error de copia de seguridad. Este artículo proporciona pasos para solucionar problemas relacionados con errores de copia de seguridad de máquina virtual de Azure relacionados con el error de tiempo de espera de instantánea agotado.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Síntoma
La máquina virtual del servicio Copia de seguridad de Microsoft Azure para una infraestructura como un servicio (IaaS) da error y devuelve el siguiente mensaje en la ventana de detalles de error de trabajo del Portal de Azure.

**No se pudo comunicar con el agente de máquina virtual para el estado de la instantánea: la subtarea de la máquina virtual de la instantánea agotó el tiempo de espera.**

## Causa
Hay cuatro causas comunes de este error:

* La máquina virtual no tiene acceso a Internet.
* El agente de máquina virtual de Microsoft Azure instalado en la máquina virtual está anticuado (en el caso de máquinas virtuales de Linux).
* No se puede actualizar ni cargar la extensión de copia de seguridad.
* No se puede recuperar el estado de las instantáneas o no se pueden tomar las mismas.

## Causa 1: La máquina virtual no tiene acceso a Internet
Según los requisitos de implementación, la máquina virtual no tiene ningún acceso a Internet o tiene restricciones vigentes que impiden el acceso a la infraestructura de Azure.

La extensión de copia de seguridad requiere conectividad a las direcciones IP públicas de Azure para poder funcionar correctamente. Esto se debe a que envía comandos a un punto de conexión de Almacenamiento de Azure (dirección URL de HTTP) para administrar las instantáneas de la máquina virtual. Si la extensión no tiene acceso a Internet, se produce un error en la copia de seguridad.

### Solución
En este escenario, utilice uno de los métodos siguientes para resolver el problema:

* Preparar una lista blanca con los intervalos IP del centro de datos de Azure.
* Crear una ruta de acceso para el flujo del tráfico HTTP

### Para preparar una lista blanca con los intervalos IP del centro de datos de Azure
1. Obtenga la lista de [IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) que van a formar parte de la lista de direcciones IP aprobadas.
2. Desbloquee las direcciones IP mediante el cmdlet New-NetRoute. Ejecute este cmdlet en la máquina virtual de Azure, en una ventana de PowerShell con privilegios elevados (realice la ejecución como administrador).
3. Agregue reglas al grupo de seguridad de red (NSG), si dispone de uno, para permitir el acceso a las direcciones IP.

### Para crear una ruta de acceso para el flujo del tráfico HTTP
1. Si tiene alguna restricción de red vigente (por ejemplo, un grupo de seguridad de red), implemente un servidor proxy HTTP para enrutar el tráfico.
2. Agregue reglas al grupo de seguridad de red para permitir el acceso a Internet desde el proxy HTTP.

Aprenda a configurar [un proxy HTTP para las copias de seguridad de máquinas virtuales](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## Causa 2: El agente de máquina virtual de Microsoft Azure instalado en la máquina virtual está anticuado (en el caso de máquinas virtuales de Linux)
### Solución
La mayoría de los errores relacionados con el agente o la extensión de máquinas virtuales de Linux están provocados por problemas que afectan a un agente VM antiguo. Como norma general, los primeros pasos para solucionar este problema son los siguientes:

1. [Instale la versión más reciente del agente de máquina virtual de Azure](https://github.com/Azure/WALinuxAgent).
2. Asegúrese de que el agente de Azure se ejecuta en la máquina virtual. Para ello, ejecute el siguiente comando: ```ps -e```
   
    Si no se está ejecutando este proceso, utilice los siguientes comandos para reiniciarlo.
   
    Para Ubuntu: ```service walinuxagent start```
   
    Para otras distribuciones: ```service waagent start
   ```
3. [Configure el agente de reinicio automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Ejecute una nueva copia de seguridad de prueba. Si el error persiste, recopile los registros de las siguientes carpetas para su depuración.
   
    Se requieren los siguientes registros de la máquina virtual del cliente:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Si se requiere el registro detallado para waagent, siga estos pasos para habilitar esta opción:

1. En el archivo /etc/waagent.conf, busque la línea siguiente:
   
    Enable verbose logging (y|n) (Habilitar registro detallado (s/n)
2. Cambie el valor de **Logs.Verbose** de n (no) a y (sí).
3. Guarde los cambios y reinicie waagent siguiendo los pasos anteriores de esta sección.

## Causa 3: No se puede actualizar ni cargar la extensión de copia de seguridad.
Si no se pueden cargar las extensiones, la copia de seguridad producirá un error porque no se puede tomar una instantánea.

### Solución
Para invitados de Windows:

1. Compruebe que el servicio iaasvmprovider está habilitado y tiene un tipo de inicio automático.
2. Si esta no es la configuración, habilite el servicio para determinar si se realiza correctamente la siguiente copia de seguridad.

Para invitados de Linux:

La versión más reciente de VMSnapshot Linux (extensión utilizada por la copia de seguridad) es 1.0.91.0

Si todavía no se puede actualizar ni cargar la extensión de copia de seguridad, puede forzar a la extensión VMSnapshot para que se vuelva a cargar mediante la desinstalación de la extensión. El siguiente intento de copia de seguridad volverá a cargar la extensión.

### Para desinstalar la extensión
1. Vaya al [Portal de Azure](https://portal.azure.com/).
2. Busque la máquina virtual concreta que tiene problemas de copia de seguridad.
3. Haga clic en **Configuración**.
4. Haga clic en **Extensiones**.
5. Haga clic en **Extensión de Vmsnapshot**.
6. Hacer clic en **Desinstalar**.

Esto hará que la extensión se vuelva a instalar durante la siguiente copia de seguridad.

## Causa 4: No se puede recuperar el estado de las instantáneas o no se pueden tomar las mismas.
La copia de seguridad de máquina virtual se basa en la emisión de comandos de instantánea para el almacenamiento subyacente. La copia de seguridad puede producir un error porque no tiene ningún acceso al almacenamiento o debido a un retraso en la ejecución de la tarea de instantáneas.

### Solución
Las siguientes condiciones pueden producir un error en la tarea de instantáneas.

| Causa | Solución |
| --- | --- |
| Máquinas virtuales con copia de seguridad de Microsoft SQL Server configurada. De forma predeterminada, la copia de seguridad de máquinas virtuales ejecuta una copia de seguridad completa de VSS en máquinas virtuales de Windows. En máquinas virtuales que se ejecutan en servidores basados en SQL Server y en las que se configura la copia de seguridad de SQL Server, se pueden producir retrasos en la ejecución de instantáneas. |Establezca la siguiente clave del Registro si experimenta errores de copia de seguridad debido a problemas con las instantáneas.<br><br>[HKEY\_LOCAL\_MACHINE\\SOFTWARE\\MICROSOFT\\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| El estado de la máquina virtual se notifica incorrectamente porque la máquina virtual está apagada en RDP. Si ha apagado la máquina virtual en RDP, compruebe el portal para determinar si ese estado de la máquina virtual se ha reflejado correctamente. |Si no es así, apague la máquina virtual en el portal mediante la opción "Apagar" en el panel de la máquina virtual. |
| Muchas máquinas virtuales del mismo servicio en la nube están configuradas para efectuar la copia de seguridad al mismo tiempo. |Se recomienda propagar las máquinas virtuales del mismo servicio en la nube para tener diferentes programaciones de copia de seguridad. |
| La máquina virtual se está ejecutando con un uso elevado de la CPU o de la memoria. |Si la máquina virtual se está ejecutando con un uso elevado de CPU (más del 90%) o un uso elevado de memoria, la tarea de instantáneas se pone en cola y se retrasa y, en ocasiones, se agota el tiempo de espera. Pruebe la copia de seguridad a petición en estas situaciones. |
| La máquina virtual no puede obtener la dirección de host o del tejido desde DHCP. |DHCP debe estar habilitado dentro del invitado para que Copia de seguridad de VM de IaaS funcione. Si la máquina virtual no puede obtener la dirección de host o del tejido de la respuesta 245 de DHCP, no podrá descargar ni ejecutar ninguna extensión. Si necesita una dirección IP privada estática, debe configurarla a través de la plataforma. La opción DHCP dentro de la máquina virtual debe continuar habilitada. Vea más información sobre el [Establecimiento de una dirección IP privada interna estática](../virtual-network/virtual-networks-reserved-private-ip.md). |

<!---HONumber=AcomDC_0921_2016-->