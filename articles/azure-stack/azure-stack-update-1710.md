---
title: "Actualización de Azure Stack 1710 (compilación 20171020.1) | Microsoft Docs"
description: "Obtenga información sobre el contenido de la actualización 1710 para los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse la actualización."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: twooley
ms.openlocfilehash: a66c7dddab1a2246dd0a9fa3c7533063140d2f01
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Actualización de Azure Stack 1710 (compilación 20171020.1)

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de este paquete de actualización, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están asociados a la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No aplique este paquete de actualización a Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Mejoras y correcciones

Esta actualización incluye las siguientes correcciones y mejoras de calidad para Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Correcciones y mejoras de Windows Server 2016

- Actualizaciones para Windows Server 2016: 10 de octubre de 2017, KB4041691 (compilación del sistema operativo 14393.1770). Consulte [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691) para obtener más información.

### <a name="additional-quality-improvements-and-fixes"></a>Correcciones y mejoras de calidad adicionales

- Se agregaron cmdlets de PowerShell de punto de conexión con privilegios para ayudar a solucionar problemas y actualizar el servidor de protocolo de tiempo de red (NTP).
- Se agregó compatibilidad para la actualización de la lista blanca de cmdlets y los módulos de punto de conexión Just Enough Administration (JEA) del punto de conexión con privilegios. 
- Se corrigieron errores de idioma local en el punto de conexión con privilegios.
- Se agregó la posibilidad de girar credenciales de puerta de enlace.
- Se quitó la cuenta de administrador local CBLocalAdmin. 
- Se corrigió el contenido de la plantilla de alerta de latido para garantizar que las alertas de latido funcionan correctamente después de una actualización.
- Se corrigió el proveedor de recursos de Fabric para controlar los tiempos de espera durante las operaciones de FRU. 
- Se agregó la posibilidad de que los desarrolladores de nube usen perfiles de API de Azure Resource Manager en Azure Stack.
- Se deshabilitó el servicio de Windows Update en la máquina virtual de implementación (DVM). 
- Se quitaron las acciones de encendido/apagado del nodo de la interfaz de usuario.
- Se aplicaron otras correcciones de rendimiento y estabilidad. 
 
## <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

En esta sección se abordan problemas conocidos que pueden surgir durante la instalación de la actualización 1710.

> [!IMPORTANT]
> Si se produce un error en la actualización, al intentar reanudarla posteriormente debe usar el cmdlet `Resume-AzureStackUpdate` en el punto de conexión con privilegios. No reanude la actualización mediante el portal de administrador (es un problema conocido de esta versión). Para obtener más información, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).

| Síntoma  | Causa  | Resolución |
|---------|---------|---------|
|Cuando se realiza una actualización, puede producirse un error similar al siguiente durante el paso de "Storage Hosts Restart Storage Node" (Los hosts de almacenamiento reinician el nodo de almacenamiento) del plan de acción de actualización.<br><br>**{"name":"Restart Storage Hosts","description":"Restart Storage Hosts.","errorMessage":"Type 'Restart' of Role 'BareMetal' raised an exception:\n\nThe computer HostName-05 is skipped. Fail to retrieve its LastBootUpTime via the WMI service with the following error message: The RPC server is unavailable. (Exception from HRESULT: 0x800706BA).\nat Restart-Host** | Este problema está causado por la presencia de un posible controlador defectuoso en algunas configuraciones. | 1. Inicie sesión en la interfaz web del controlador de administración de placa base (BMC) y reinicie el host que se identifica en el mensaje de error.<br><br>2. Reanude la actualización mediante el uso del punto de conexión con privilegios. |
| Cuando se realiza una actualización, el proceso de actualización parece detenerse y no progresar después del paso "Step: Running step 2.4 - Install update"(Ejecución del paso 2.4: instalación de la actualización) del plan de acción de actualización.<br><br>A este paso le sigue una serie de procesos de copia de archivos .nupkg en los recursos compartidos de archivos de infraestructura interna. Por ejemplo:<br><br>**Copying 1 files from content\PerfCollector\VirtualMachines to \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\PerfCounterConfiguration**  | El problema se debe a archivos de registro que llenan los discos en una máquina virtual de infraestructura. Se entregará una edición del Servidor de archivos de escalabilidad horizontal (SOFS) de Windows Server en una actualización posterior. | Póngase en contacto con el Soporte técnico y el servicio al cliente de Microsoft (CSS). | 
| Cuando se realiza una actualización, puede producirse un error similar al siguiente durante el paso "Step: Running step 2.13.2 - Update *VM_Name*" (ejecución del paso 2.13.2: actualización de VM_Name) del plan de acción de actualización. (El nombre de la máquina virtual puede variar).<br><br>**ActionPlanInstanceWarning ece/MachineName: WarningMessage:Task: Invocation of interface 'LiveUpdate' of role 'Cloud\Fabric\WAS' failed:<br>Type 'LiveUpdate' of Role 'WAS' raised an exception:<br>ERROR during storage initialization: An error occurred while trying to make an API call to Microsoft Storage service: {"Message":"A timeout occurred while communicating with Service Fabric. Exception Type: TimeoutException. Exception message: Operation timed out."}**  | El problema se debe a un tiempo de espera de E/S en Windows Server que se corregirá en una actualización posterior. | Para recibir asistencia, póngase en contacto con Microsoft CSS.
| Cuando se realiza una actualización, un error similar al siguiente puede producirse durante el paso "Step 21 Restart SQL server VMs" (Paso 21 Reinicio de las VM de SQL Server).<br><br>**Type 'LiveUpdateRestart' of Role 'VirtualMachines' raised an exception:<br>VerboseMessage:[VirtualMachines:LiveUpdateRestart] Querying for VM MachineName-Sql01. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart] VM is marked as HighlyAvailable. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart] at MS.Internal.ServerClusters.ExceptionHelp.Build at MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline(Boolean force) at Microsoft.FailoverClusters.PowerShell.StopClusterResourceCommand.BeginTimedOperation() at Microsoft.FailoverClusters.PowerShell.TimedCmdlet.WrappedProcessRecord() at Microsoft.FailoverClusters.PowerShell.FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM WarningMessage:Task: Invocation of interface 'LiveUpdateRestart' of role 'Cloud\Fabric\VirtualMachines' failed:** | Este problema puede producirse si la máquina virtual no se puede reiniciar. | Para recibir asistencia, póngase en contacto con Microsoft CSS.
| Cuando se realiza una actualización, puede producirse un error similar al siguiente:<br><br>**2017-10-22T01:37:37.5369944Z Type 'Shutdown' of Role 'SQL' raised an exception:An error occurred pausing node 's45r1004-Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Roles\SQL\SQL.psm1: line 542at Shutdown, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Classes\SQL\SQL.psm1: line 50at <ScriptBlock>, <No file>: line 18at <ScriptBlock>, <No file>: line 16** | Este problema puede producirse si la máquina virtual no se puede ponerse en estado suspendido para purgar los roles. | Para recibir asistencia, póngase en contacto con Microsoft CSS.
| Cuando se realiza una actualización, puede producirse cualquiera de los siguientes errores:<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation for ADFS/Graph role failed with error: Error checking ADFS probe endpoint *endpoint_URI*: Exception calling "GetResponse" with "0" argument(s): "The remote server returned an error: (503) Server Unavailable." at Invoke-ADFSGraphValidation**<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation for ADFS/Graph role failed with error: Error fetching ADFS properties: Could not connect to net.tcp://localhost:1500/policy. The connection attempt lasted for a time span of 00:00:02.0498923. TCP error code 10061: No connection could be made because the target machine actively refused it 127.0.0.1:1500. at Invoke-ADFSGraphValidation** | El plan de acción de actualización no puede validar el estado de los Servicios de federación de Active Directory (AD FS). | Para recibir asistencia, póngase en contacto con Microsoft CSS.

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Esta sección contiene problemas conocidos posteriores a la instalación relacionados con la compilación 20171020.1.

### <a name="portal"></a>Portal

- No es posible ver los recursos de proceso o almacenamiento en el portal de administración. Esto indica que se produjo un error durante la instalación de la actualización y que la actualización se notificó como correcta erróneamente. Si el problema persiste, póngase en contacto con Microsoft CSS para obtener asistencia.
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.
- El botón **Mover** está deshabilitado cuando se consultan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, se pueden comprobar los permisos con PowerShell.
-  Para cualquier flujo de trabajo donde seleccione una suscripción, un grupo de recursos o una ubicación en una lista desplegable, puede experimentar uno o varios de los siguientes problemas:

   - Puede que vea una fila en blanco en la parte superior de la lista. Debería poder seleccionar un elemento según lo previsto.
   - Si la lista de elementos de la lista desplegable es breve, es posible que no se vean los nombres de los elementos.
   - Si tiene varias suscripciones de usuario, la lista desplegable de grupos de recursos podría estar vacía. 

   Para evitar los dos últimos problemas, puede escribir el nombre de la suscripción o el grupo de recursos (si lo conoce), o puede usar PowerShell en su lugar.
  
### <a name="backup"></a>Backup

- No habilite la copia de seguridad de la infraestructura en la hoja **Infrastructure backup** (Copia de seguridad de la infraestructura).

### <a name="health-and-monitoring"></a>Estado y supervisión

- Si reinicia una instancia de rol de infraestructura, es posible que reciba un mensaje que indica que se produjo un error en el reinicio. Sin embargo, el reinicio se realizó correctamente.

### <a name="services"></a>Services

**Marketplace**
- Al intentar agregar elementos al Marketplace de Azure Stack mediante la opción **Add from Azure** (Agregar desde Azure), es posible que no todos los elementos estén visibles para su descarga.
- No hay ninguna experiencia del Marketplace para crear conjuntos de escalado de máquinas virtuales. Puede crear un conjunto de escalado mediante una plantilla.
- Un inquilino debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario. 

**SQL/MySQL**
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.

**Proceso**
- Los usuarios tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica. Esta configuración hace que no se puedan crear máquinas virtuales.
- Puede configurar un conjunto de disponibilidad de la máquina virtual con un dominio de error de uno y un dominio de actualización de uno.
 
**Red**
- No puede crear un equilibrador de carga con una dirección IP pública mediante el portal. Como alternativa, puede usar PowerShell para crear el equilibrador de carga.
- Debe crear una regla de traducción de direcciones de red (NAT) cuando cree un equilibrador de carga de red. Si no lo hace, recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procedimientos de unidad reemplazable en campo (FRU)

- Durante la ejecución de actualización, las imágenes sin conexión no se revisan. Si es necesario reemplazar un nodo de unidad de escala, trabaje con su proveedor de hardware de OEM para asegurarse de que el nodo reemplazado tiene el nivel de revisión más reciente.

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar el paquete de actualización 1710 [aquí](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una introducción a la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).
- Para obtener información acerca de cómo aplicar las actualizaciones, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).