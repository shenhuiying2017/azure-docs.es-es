---
title: Notas de la versión del Kit de desarrollo de Microsoft Azure Stack | Microsoft Docs
description: Mejoras, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 9a396c51e8eb6262726b7590498500bfb4528924
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258411"
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack
Estas notas de la versión proporcionan información sobre las mejoras, correcciones y problemas conocidos en el Kit de desarrollo de Azure Stack. Si no está seguro de qué versión se está ejecutando, puede usar el [portal de administración](.\.\azure-stack-updates.md#determine-the-current-version).

> Para estar al día de las novedades del ASDK, suscríbase a esta [![fuente](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).


## <a name="build-201805131"></a>Compilación 20180513.1

### <a name="new-features"></a>Nuevas características 
Esta compilación incluye las siguientes correcciones y mejoras para Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available:
  - *Suscripción del proveedor predeterminado*. Use esta suscripción solo para la infraestructura básica. No implemente recursos ni proveedores de recursos en esta suscripción.
  - *Suscripción de medición*. Use esta suscripción para la implementación de proveedores de recursos. No se le cobrarán los recursos implementados en esta suscripción.
  - *Suscripción de consumo*. Use esta suscripción para cualquier otra carga de trabajo que desee implementar. Los recursos implementados aquí se cobran al precio de uso normal.


### <a name="fixed-issues"></a>Problemas corregidos
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Versiones adicionales en el momento de esta actualización  
Las siguientes están disponibles ahora, pero no requieren la actualización 1804 de Azure Stack.
- **Actualización del pack de supervisión de System Center Operations Manager para Microsoft Azure Stack**. Una nueva versión (1.0.3.0) del paquete de supervisión de System Center Operations Manager para Microsoft Azure Stack está disponible para [descargar](https://www.microsoft.com/download/details.aspx?id=55184). Con esta versión, puede usar a las entidades de servicio al agregar una implementación de Azure Stack conectada. Esta versión también incluye una experiencia de Update Management que le permite realizar una acción de corrección directamente desde Operations Manager. También hay nuevos paneles que muestran los proveedores de recursos, las unidades de escalado y los nodos de las unidades de escalado.

- **Nueva versión 1.3.0 de PowerShell de administración para Azure Stack**.  Azure Stack PowerShell 1.3.0 ahora está disponible para instalar. Esta versión proporciona comandos para que todos los proveedores de recursos de administración administren Azure Stack.  Con esta versión, parte del contenido del [repositorio](https://github.com/Azure/AzureStack-Tools) de GitHub para herramientas de Azure Stack entrará en desuso. 

   Para obtener información de instalación, siga las [instrucciones](.\.\azure-stack-powershell-install.md) o el contenido de [ayuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) para el módulo 1.3.0 de Azure Stack. 

- **Versión inicial de la referencia de la API de REST para Azure Stack**. Se ha publicado la [referencia de la API para todos los proveedores de recursos de administración de Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/). 

### <a name="known-issues"></a>Problemas conocidos
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Para el Kit de desarrollo de Azure Stack, use https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Ruta de navegación](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERROR: falta la plantilla de FaultType ResourceProviderTimeout)

    Esta alerta puede omitirse sin problemas. 

#### <a name="compute"></a>Proceso
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo un error.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Redes
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Para solucionar este problema, use un plan complementario para aumentar una cuota de red cuando el plan ya esté asociado a una suscripción. Para obtener más información, consulte cómo [disponer de un plan complementario](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL y MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->






## <a name="build-201803291"></a>Compilación 20180329.1

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Las nuevas características y revisiones publicadas para la versión 1803 de los sistemas integrados de Azure Stack se aplican al Kit de desarrollo de Azure Stack. Consulte las secciones de [nuevas características](.\.\azure-stack-update-1803.md#new-features) y [nuevas correcciones](.\.\azure-stack-update-1803.md#fixed-issues) de las notas de la versión de la actualización 1803 de Azure Stack para obtener más información.  
> [!IMPORTANT]    
> Algunos de los elementos que aparecen en las secciones de **nuevas características** y **nuevas correcciones** solo son importantes para los sistemas integrados de Azure Stack.

### <a name="changes"></a>Cambios
- La manera de cambiar el estado de una oferta recién creada de *privada* a *pública* o *retirada* ha cambiado. Para más información, consulte [Creación de una oferta](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problemas conocidos
 
#### <a name="portal"></a>Portal
- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) del portal de administración. En su lugar, use el siguiente vínculo:     
    - Para el Kit de desarrollo de Azure Stack, use https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Verá una alerta de advertencia **Activación necesaria** que le informa de que debe registrar el Kit de desarrollo de Azure Stack. Este comportamiento es normal.

- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.

- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use PowerShell para comprobar los permisos.

- En el panel del portal de administración, el icono de actualización no muestra información sobre las actualizaciones. Para resolver este problema, haga clic en el icono para actualizarlo.

-   En el portal de administración, puede que vea una alerta crítica del componente Microsoft.Update.Admin. El nombre de la alerta, la descripción y la solución se muestran como:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERROR: falta la plantilla de FaultType ResourceProviderTimeout)

    Esta alerta puede omitirse sin problemas. 



#### <a name="marketplace"></a>Marketplace
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.
 
#### <a name="compute"></a>Proceso
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie DS. Las máquinas virtuales de la serie DS pueden albergar tantos discos de datos como la configuración de Azure.

- Cuando no es posible crear una imagen de máquina virtual, los elementos con error que no se pueden eliminar se podrían agregar a la hoja de proceso de imágenes de máquina virtual.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo un error.

-  Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Redes
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.

- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.



- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

- En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.
 
- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.



#### <a name="sql-and-mysql"></a>SQL y MySQL 
- Puede pasar una hora antes de que los usuarios puedan crear bases de datos en una nueva SKU de SQL o MySQL.

- La base de datos que hospeda los servidores debe estar dedicada al uso por el proveedor de recursos y las cargas de trabajo de usuario. No puede usar una instancia de SQL que esté siendo utilizada por otro consumidor, incluido App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>App Service
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.
 
#### <a name="usage"></a>Uso  
- El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Descarga de las herramientas de Azure Stack desde GitHub
- Al usar el cmdlet *invoke-webrequest* de PowerShell para descargar las herramientas de Azure Stack de Github, recibirá un error:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest: anulada la solicitud, no se pudo crear un canal seguro SSL/TLS)     

  Este error se produce debido a una degradación reciente de la compatibilidad con GitHub de los estándares criptográficos Tlsv1 y Tlsv1.1 (los predeterminados de PowerShell). Para más información, consulte [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Aviso de eliminación de estándares criptográficos débiles).

  Para resolver este problema, agregue `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a la parte superior del script para obligar a la consola de PowerShell a usar TLSv1.2 cuando se descargue de los repositorios de GitHub.






## <a name="build-201803021"></a>Compilación 20180302.1

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Consulte la sección de [nuevas características y correcciones](.\.\azure-stack-update-1802.md#new-features-and-fixes) de las notas de versión de la actualización 1802 de Azure Stack para sistemas integrados de Azure Stack.

> [!IMPORTANT]    
> Algunos de los elementos que aparecen en la sección **Nuevas características y correcciones** solo son importantes para los sistemas integrados de Azure Stack.


### <a name="known-issues"></a>Problemas conocidos
 
#### <a name="portal"></a>Portal
- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) del portal de administración. En su lugar, use el siguiente vínculo:     
    - Para el Kit de desarrollo de Azure Stack, use https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Verá una alerta de advertencia **Activación necesaria** que le informa de que debe registrar el Kit de desarrollo de Azure Stack. Este comportamiento es normal.

- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.

- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use PowerShell para comprobar los permisos.

- En el panel del portal de administración, el icono de actualización no muestra información sobre las actualizaciones. Para resolver este problema, haga clic en el icono para actualizarlo.

-   En el portal de administración, puede que vea una alerta crítica del componente Microsoft.Update.Admin. El nombre de la alerta, la descripción y la solución se muestran como:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERROR: falta la plantilla de FaultType ResourceProviderTimeout)

    Esta alerta puede omitirse sin problemas. 

- En el portal de administración y el portal de usuarios, la hoja de información general no puede cargarse al seleccionarla para las cuentas de almacenamiento que se crearon con una versión anterior de la API (ejemplo: 15-06-2015). 

  Como alternativa, use PowerShell para ejecutar el script **Start-ResourceSynchronization.ps1** para restaurar el acceso a los detalles de la cuenta de almacenamiento. [El script está disponible en GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) y debe ejecutarse con las credenciales de administrador del servicio en el host del kit de desarrollo si utiliza el ASDK.  

- La hoja de **Service Health** no se carga. Cuando se abre la hoja de Service Health en el portal del administrador o de usuario, Azure Stack muestra un error y no carga información. Este es el comportamiento esperado. Si bien es posible seleccionar y abrir Service Health, esta característica no está disponible aún, aunque se implementará en una versión futura de Azure Stack.

#### <a name="health-and-monitoring"></a>Estado y supervisión
En el portal de administración de Azure Stack, puede que vea una alerta crítica con el nombre **Pending external certificate expiration**(Pendiente de expiración del certificado externo).  Esta alerta puede omitirse sin problemas y afecta a las operaciones del Kit de desarrollo de Azure Stack. 


#### <a name="marketplace"></a>Marketplace
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.
 
#### <a name="compute"></a>Proceso
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

- Azure Stack solo admite el uso de discos duros virtuales de tipo fijo. Algunas imágenes ofrecidas mediante Marketplace en Azure Stack usan discos duros virtuales dinámicos, pero estas se han quitado. El hecho de cambiar el tamaño de una máquina virtual (VM) que lleva asociado un disco dinámico deja a la máquina en estado de error.

  Para mitigar este problema, elimine la máquina virtual sin eliminar el disco de la máquina virtual, un blob de disco duro virtual de una cuenta de almacenamiento. A continuación, convierta el disco duro virtual dinámico a fijo y vuelva a crear la máquina virtual.

- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie DS. Las máquinas virtuales de la serie DS pueden albergar tantos discos de datos como la configuración de Azure.

- Cuando no es posible crear una imagen de máquina virtual, los elementos con error que no se pueden eliminar se podrían agregar a la hoja de proceso de imágenes de máquina virtual.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo un error.

-  Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Redes
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.

- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.

-   La característica de reenvío IP está visible en el portal; sin embargo, no ocurre nada al habilitar esta característica. El motivo es que aún no se admite.

- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

- En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.
 
- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.



#### <a name="sql-and-mysql"></a>SQL y MySQL 
- Puede pasar una hora antes de que los usuarios puedan crear bases de datos en una nueva SKU de SQL o MySQL.

- La base de datos que hospeda los servidores debe estar dedicada al uso por el proveedor de recursos y las cargas de trabajo de usuario. No puede usar una instancia de SQL que esté siendo utilizada por otro consumidor, incluido App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>App Service
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.
 
#### <a name="usage"></a>Uso  
- El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Descarga de las herramientas de Azure Stack desde GitHub
- Al usar el cmdlet *invoke-webrequest* de PowerShell para descargar las herramientas de Azure Stack de Github, recibirá un error:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest: anulada la solicitud, no se pudo crear un canal seguro SSL/TLS)     

  Este error se produce debido a una degradación reciente de la compatibilidad con GitHub de los estándares criptográficos Tlsv1 y Tlsv1.1 (los predeterminados de PowerShell). Para más información, consulte [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Aviso de eliminación de estándares criptográficos débiles).

  Para resolver este problema, agregue `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a la parte superior del script para obligar a la consola de PowerShell a usar TLSv1.2 al realizar la descarga desde repositorios de GitHub.

