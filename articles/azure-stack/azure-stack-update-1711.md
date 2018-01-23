---
title: "Actualización de Azure Stack 1711 | Microsoft Docs"
description: "Obtenga información sobre el contenido de la actualización 1711 para los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse la actualización."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: andredm
ms.openlocfilehash: 578d17bcfbb7e12c9855132772c2068a5cdf1f62
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-stack-1711-update"></a>Actualización de Azure Stack 1711

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de este paquete de actualización, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están asociados a la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Esta actualización solo se aplica a los sistemas integrados de Azure Stack. No la aplique a Azure Stack Development Kit.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización de Azure Stack 1711 es **171201.3**.

## <a name="before-you-begin"></a>Antes de empezar

### <a name="prerequisites"></a>requisitos previos

En primer lugar, debe instalar la actualización de Azure Stack [1710](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) antes de aplicar esta actualización.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

Esta actualización incluye las siguientes correcciones y mejoras para Azure Stack.

#### <a name="new-features"></a>Nuevas características

- Compatibilidad con la distribución de las plantillas de solución
- Actualizaciones en el control de errores y el registro de Azure Stack Graph
- Capacidad de activar y desactivar los hosts
- Los usuarios ahora pueden activar automáticamente las máquinas virtuales Windows
- Se agregó un cmdlet de PowerShell de punto de conexión con privilegios para recuperar las claves de recuperación de BitLocker para la retención
- Compatibilidad para actualizar las imágenes sin conexión cuando se actualiza la infraestructura
- Habilitar la copia de seguridad de infraestructura con el servicio Habilitar copia de seguridad

#### <a name="fixes"></a>Correcciones

- Se corrigió la condición de carrera en DNS durante la unidad reemplazable en campo (FRU) y también se actualizó el registro del clúster
- Corrección de la capacidad de reinicio de deshabilitar host en la unidad reemplazable en campo (FRU)
- Se aplicaron otras correcciones de rendimiento, seguridad y estabilidad

#### <a name="windows-server-2016-new-features-and-fixes"></a>Nuevas características y correcciones de Windows Server 2016

- [14 de noviembre de 2017: KB4048953 (compilación del sistema operativo 14393.1884) ](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

En esta sección se abordan los problemas conocidos que pueden surgir durante la instalación de la actualización 1711.


1. **Síntoma:** Los operadores de Azure Stack podrían ver el error siguiente durante el proceso de actualización: *"name:Install Update.", "description": "Install Update on Hosts and Infra VMs.", "errorMessage": "Type 'LiveUpdate' of Role 'VirtualMachines' raised an exception:\n\nThere is not enough space on the disk.\n\nat <ScriptBlock>, <No file>: line22", "status": "Error", "startTimeUtc": "2017-11-10T16:46:59.123Z", "endTimeUtc": "2017-11-10T19:20:29.669Z", "steps": [ ]"*
    2. **Causa:** Este problema lo provoca la falta de espacio en disco disponible en una o varias máquinas virtuales que forman parte de la infraestructura de Azure Stack.
    3. **Resolución:** Póngase en contacto con el Soporte técnico y el servicio al cliente de Microsoft (CSS).
<br><br>
2. **Síntoma:** Los operadores de Azure Stack podrían ver el error siguiente durante el proceso de actualización:*Exception calling "ExtractToFile" with "3" argument(s):"The process cannot access the file '<\\<machineName>-ERCS01\C$\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll>'*
    1. **Causa:** Este problema se produce cuando se reanuda una actualización desde el portal que anteriormente se reanudó con un punto de conexión con privilegios (PEP).
    2. **Resolución:** Póngase en contacto con el Soporte técnico y el servicio al cliente de Microsoft (CSS).
<br><br>
3. **Síntoma:** Los operadores de Azure Stack podrían ver el error siguiente durante el proceso de actualización:*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for <machineName>-ACS01 produced the following errors.\nThere was an error getting VM information from hosts. Exception details:\nGet-VM : The operation on computer 'Node03' failed: The WS-Management service cannot process the request. The WMI \nservice or the WMI provider returned an unknown error: HRESULT 0x8004106c".*
    1. **Causa:** Este problema lo produce un error de Windows Server que se intentará abordar en actualizaciones subsiguientes de Windows Server.
    2. **Resolución:** Póngase en contacto con el Soporte técnico y el servicio al cliente de Microsoft (CSS).
<br><br>
4. **Síntoma:** Los operadores de Azure Stack podrían ver el error siguiente durante el proceso de actualización:*"Type 'DefenderUpdate' of Role 'URP' raised an exception: Failed getting version from \\SU1FileServer\SU1_Public\DefenderUpdates\x64\{file name}.exe after 60 attempts at Copy-AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: line 262"*
    1. **Causa:** Este problema surge por una descarga en segundo plano incompleta o con errores de las actualizaciones de definiciones de Windows Defender.
    2. **Resolución:** Intente reanudar la actualización después de hasta ocho horas desde el primer intento de actualización.

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Esta sección contiene problemas conocidos posteriores a la instalación relacionados con la compilación **20171201.3**.

#### <a name="portal"></a>Portal

- No es posible ver los recursos de proceso o almacenamiento en el portal de administración. Esto indica que se produjo un error durante la instalación de la actualización y que la actualización se notificó como correcta erróneamente. Si el problema persiste, póngase en contacto con Microsoft CSS para obtener asistencia.
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- El botón **Mover** está deshabilitado cuando se visualizan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
- Para cualquier flujo de trabajo donde seleccione una suscripción, un grupo de recursos o una ubicación en una lista desplegable, puede experimentar uno o varios de los siguientes problemas:

   - Puede que vea una fila en blanco en la parte superior de la lista. Debería poder seleccionar un elemento según lo previsto.
   - Si la lista de elementos de la lista desplegable es breve, es posible que no se vean los nombres de los elementos.
   - Si tiene varias suscripciones de usuario, la lista desplegable de grupos de recursos podría estar vacía. 

        > [!NOTE]
        > Para evitar los dos últimos problemas, puede escribir el nombre de la suscripción o el grupo de recursos (si lo conoce), o puede usar PowerShell en su lugar.

- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, se pueden comprobar los permisos con PowerShell.

#### <a name="health-and-monitoring"></a>Estado y supervisión

- Si reinicia una instancia de rol de infraestructura, es posible que reciba un mensaje que indica que se produjo un error en el reinicio. Sin embargo, el reinicio se realizó correctamente.

#### <a name="marketplace"></a>Marketplace
- Al intentar agregar elementos al Marketplace de Azure Stack mediante la opción **Add from Azure** (Agregar desde Azure), es posible que no todos los elementos estén visibles para su descarga.
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.

#### <a name="compute"></a>Proceso
- Los usuarios tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica. Esta configuración hace que no se puedan crear máquinas virtuales.
- Puede configurar un conjunto de disponibilidad de la máquina virtual con un dominio de error de uno y un dominio de actualización de uno.
- No hay ninguna experiencia del Marketplace para crear conjuntos de escalado de máquinas virtuales. Puede crear un conjunto de escalado mediante una plantilla.
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.
 
#### <a name="networking"></a>Redes
- No puede crear un equilibrador de carga con una dirección IP pública mediante el portal. Como alternativa, puede usar PowerShell para crear el equilibrador de carga.
- Debe crear una regla de traducción de direcciones de red (NAT) cuando cree un equilibrador de carga de red. Si no lo hace, recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.
- Es posible que los operadores de Azure Stack no puedan implementar, eliminar o modificar las redes virtuales o los grupos de seguridad de red. Este problema se ve principalmente en los intentos de actualización posteriores del mismo paquete. Esto se debe a un problema de empaquetado con una actualización que esté investigándose.
- El equilibrio de carga interno (ILB) maneja incorrectamente las direcciones MAC de máquinas virtuales de back-end, lo que interrumpe las instancias de Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.
 
#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

#### <a name="identity"></a>Identidad

En entornos implementados de Servicios de federación de Active Directory (AD FS), la cuenta **azurestack\azurestackadmin** ya no es la propietaria de la suscripción de proveedor predeterminada. En lugar de iniciar sesión en el **portal de administración / punto de conexión adminmanagement** con **azurestack\azurestackadmin**, puede usar la cuenta **azurestack\cloudadmin**, de forma que puede administrar y usar la suscripción de proveedor predeterminada.

> [!IMPORTANT]
> Aunque la cuenta **azurestack\cloudadmin** es la propietaria de la suscripción de proveedor predeterminada en entornos implementados de ADFS, no tiene permisos para RDP en el host. Siga usando la cuenta **azurestack\azurestackadmin** o la cuenta de administrador local para iniciar sesión en el host, acceder a él y administrarlo según sea necesario.

#### <a name="infrastructure-backup-sevice"></a>Servicio de copia de seguridad de infraestructura
<!-- 1974890-->

- **No se admiten copias de seguridad de versiones anteriores a 1711 para la recuperación en la nube.**  
  Las copias de seguridad de versiones anteriores a 1711 no son compatibles con la recuperación en la nube. Debe actualizar primero a la versión 1711 y habilitar las copias de seguridad. Si ya tiene habilitadas las copias de seguridad, asegúrese de realizar una copia de seguridad después de actualizar a la versión 1711. Las copias de seguridad de versiones anteriores a 1711 deben eliminarse.

- **La habilitación de la copia de seguridad de infraestructura en ASDK se realiza solo con fines de prueba.**  
  Las copias de seguridad de infraestructura pueden utilizarse para restaurar soluciones de varios nodos. Puede habilitar la copia de seguridad de infraestructura en ASDK, pero no existe ninguna manera de probar la recuperación.

Para obtener más información, consulte [Backup and data recovery for Azure Stack with the Infrastructure Backup Service](C:\Git\MS\azure-docs-pr\articles\azure-stack\azure-stack-backup-infrastructure-backup.md) (Copia de seguridad y recuperación de datos de Azure Stack con el servicio de copia de seguridad de infraestructura).

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1711 [aquí](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Más información

Microsoft proporcionó una manera de supervisar y reanudar las actualizaciones mediante el punto de conexión con privilegios (PEP) que se instaló con la actualización 1711.

- Consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Otras referencias

- Consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md) para una introducción de la administración de actualizaciones en Azure Stack.
- Consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md) para más información sobre cómo aplicar las actualizaciones con Azure Stack.
