---
title: "Actualización de Azure Stack 1712 | Microsoft Docs"
description: "Obtenga información sobre el contenido de la actualización 1712 para los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse la actualización."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: brenduns
ms.openlocfilehash: 0456a202990d383370051d99112f829533b1b101
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="azure-stack-1712-update"></a>Actualización de Azure Stack 1712

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de este paquete de actualización, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están asociados a la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Esta actualización solo se aplica a los sistemas integrados de Azure Stack. No la aplique a Azure Stack Development Kit.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización de Azure Stack 1712 es **180106.1**. Si un cliente ya ha implementado **180103.2** anteriormente, no es necesario aplicar **180106.1**.

## <a name="before-you-begin"></a>Antes de empezar

> [!IMPORTANT]
> No intente crear máquinas virtuales durante el proceso de instalación de la actualización 1712. Consulte [Introducción a la administración de actualizaciones en Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) para más información.

### <a name="prerequisites"></a>requisitos previos

En primer lugar, debe instalar la actualización de Azure Stack [1711](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) antes de aplicar esta actualización.

### <a name="post-update-steps"></a>Pasos posteriores a la actualización

Esta actualización también requiere que instale las actualizaciones de firmware del asociado OEM después de completar la instalación de actualización 1712 de Azure Stack.

> [!NOTE]
> Consulte el sitio web de los asociados OEM para descargar las actualizaciones.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

Esta actualización incluye las siguientes correcciones y mejoras para Azure Stack.

#### <a name="new-features"></a>Nuevas características

- El cmdlet Test-AzureStack para validar Azure Stack en la nube, disponible a través de un punto de conexión con privilegios
- Posibilidad de registrar una implementación desconectada de Azure Stack
- Alertas de supervisión sobre la expiración de certificados y de la cuenta del usuario
- Se ha agregado el cmdlet Set-BmcPassword en PEP para la rotación de contraseñas de BMC
- Actualizaciones del registro de red para admitir el registro a petición
- Compatibilidad con la operación de restablecimiento de imagen inicial para los conjuntos de escalado de máquinas virtuales (VMSS)
- Habilitación de la pantalla completa en máquina virtual de ERCS para el inicio de sesión de CloudAdmin
- Los inquilinos pueden activar automáticamente las máquinas virtuales Windows

#### <a name="fixes"></a>Correcciones

- Corrección para mostrar el estado operativo del nodo en mantenimiento durante la ejecución de una reparación
- Solución para corregir la marca de fecha y hora de los registros de uso de direcciones IP públicas
- Se aplicaron otras correcciones de rendimiento, estabilidad y seguridad
- Corrección de los errores del módulo de punto de conexión con privilegios de TimeSource y Defender

#### <a name="windows-server-2016-new-features-and-fixes"></a>Nuevas características y correcciones de Windows Server 2016

- [3 de enero de 2018: KB4056890 (compilación del sistema operativo 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Esta actualización incluye las correcciones de software para el problema de seguridad de todo el sector descrito en [Documento informativo ADV 180002 sobre seguridad de MSRC](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

En esta sección se abordan los problemas conocidos que pueden surgir durante la instalación de la actualización 1712.

1. **Síntoma:** Los operadores de Azure Stack podrían ver el error siguiente durante el proceso de actualización:*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for -ACS01 produced the following errors.\nThere was an error getting VM information from hosts. Exception details:\nGet-VM : The operation on computer 'Node03' failed: The WS-Management service cannot process the request. The WMI \nservice or the WMI provider returned an unknown error: HRESULT 0x8004106c".*
    1. **Causa:** Este problema lo produce un error de Windows Server que se intentará abordar en actualizaciones subsiguientes de Windows Server.
    2. **Resolución:** Póngase en contacto con el Soporte técnico y el servicio al cliente de Microsoft (CSS).
<br><br>
2. **Síntoma:** los operadores de Azure Stack podrían ver el error siguiente durante el proceso de actualización:*"Enabling the seed ring VM failed on node Host-Node03 with an error: [Host-Node03] Connecting to remote server Host-Node03 failed with the following error message : The WinRM client received an HTTP server error status (500), but the remote service did not include any other information about the cause of the failure."*
    1. **Causa:** Este problema lo produce un error de Windows Server que se intentará abordar en actualizaciones subsiguientes de Windows Server. 
    2. **Resolución:** Póngase en contacto con el Soporte técnico y el servicio al cliente de Microsoft (CSS).
<br><br>

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Esta sección contiene problemas conocidos posteriores a la instalación relacionados con la compilación **180106.1**.

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
- Se han eliminado algunos elementos de Marketplace en esta versión por motivos de compatibilidad. Estos se volverán a habilitar después de una validación adicional.
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

    > [!NOTE]
    > No debería experimentar ningún efecto en los usuarios del proveedor SQL o MySQL existentes al actualizar los servicios integrados de Azure Stack a la versión 1712. Puede continuar usando las compilaciones del proveedor de recursos SQL o MySQL actual hasta que esté disponible una nueva actualización de Azure Stack.

#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

#### <a name="identity"></a>Identidad

En entornos implementados de Servicios de federación de Active Directory (AD FS), la cuenta **azurestack\azurestackadmin** ya no es la propietaria de la suscripción de proveedor predeterminada. En lugar de iniciar sesión en el **portal de administración / punto de conexión adminmanagement** con **azurestack\azurestackadmin**, puede usar la cuenta **azurestack\cloudadmin**, de forma que puede administrar y usar la suscripción de proveedor predeterminada.

> [!IMPORTANT]
> Aunque la cuenta **azurestack\cloudadmin** es la propietaria de la suscripción de proveedor predeterminada en entornos implementados de ADFS, no tiene permisos para RDP en el host. Siga usando la cuenta **azurestack\azurestackadmin** o la cuenta de administrador local para iniciar sesión en el host, acceder a él y administrarlo según sea necesario.

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1712 [aquí](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Más información

Microsoft proporcionó una manera de supervisar y reanudar las actualizaciones mediante el punto de conexión con privilegios (PEP) que se instaló con la actualización 1712.

- Consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Otras referencias

- Consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md) para una introducción de la administración de actualizaciones en Azure Stack.
- Consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md) para más información sobre cómo aplicar las actualizaciones con Azure Stack.
