---
title: Notas de la versión del Kit de desarrollo de Microsoft Azure Stack | Microsoft Docs
description: Mejoras, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 1a8cbdef8f3d8a5aa4aeab0e51275933160360c2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack
Estas notas de la versión proporcionan información sobre las mejoras, correcciones y problemas conocidos en el Kit de desarrollo de Azure Stack. Si no está seguro de qué versión se está ejecutando, puede usar el [portal de administración](.\.\azure-stack-updates.md#determine-the-current-version).

> Para estar al día de las novedades del ASDK, suscríbase a esta [![fuente](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-201803291"></a>Compilación 20180329.1

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Las nuevas características y revisiones publicadas para la versión 1803 de los sistemas integrados de Azure Stack se aplican al Kit de desarrollo de Azure Stack. Consulte las secciones de [nuevas características](.\.\azure-stack-update-1803.md#new-features) y [nuevas correcciones](.\.\azure-stack-update-1803.md#fixed-issues) de las notas de la versión de la actualización 1803 de Azure Stack para obtener más información.  
> [!IMPORTANT]    
> Algunos de los elementos que aparecen en las secciones de **nuevas características** y **nuevas correcciones** solo son importantes para los sistemas integrados de Azure Stack.

### <a name="changes"></a>Cambios
- La manera de cambiar el estado de una oferta recién creada de *privada* a *pública* o *retirada* ha cambiado. Para obtener más información, consulte [Creación de una oferta](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problemas conocidos
 
#### <a name="portal"></a>Portal
- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) del portal de administración. En su lugar, use el siguiente vínculo:     
    - Para el Kit de desarrollo de Azure Stack, use https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Al ver las propiedades de un recurso o grupo de recursos, el botón **Mover** está deshabilitado. Este comportamiento es normal. Actualmente no se admite mover recursos o grupos de recursos entre grupos de recursos o suscripciones.
 
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

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo error.

-  Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Redes
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.

- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.



- Azure Stack admite una única *puerta de enlace de red* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

- En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.
 
- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.



#### <a name="sql-and-mysql"></a>SQL y MySQL 
- Puede pasar una hora antes de que los usuarios puedan crear bases de datos en una nueva SKU de SQL o MySQL.

- La base de datos que hospeda los servidores debe estar dedicada al uso por el proveedor de recursos y las cargas de trabajo de usuario. No puede usar una instancia de SQL que esté siendo utilizada por otro consumidor, incluido App Services.

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

- Al ver las propiedades de un recurso o grupo de recursos, el botón **Mover** está deshabilitado. Este comportamiento es normal. Actualmente no se admite mover recursos o grupos de recursos entre grupos de recursos o suscripciones.
 
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

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo error.

-  Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Redes
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.

- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.

-   La característica de reenvío IP está visible en el portal, sin embargo, no ocurre nada al habilitar esta característica. El motivo es que aún no se admite.

- Azure Stack admite una única *puerta de enlace de red* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

- En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.
 
- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.



#### <a name="sql-and-mysql"></a>SQL y MySQL 
- Puede pasar una hora antes de que los usuarios puedan crear bases de datos en una nueva SKU de SQL o MySQL.

- La base de datos que hospeda los servidores debe estar dedicada al uso por el proveedor de recursos y las cargas de trabajo de usuario. No puede usar una instancia de SQL que esté siendo utilizada por otro consumidor, incluido App Services.

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




## <a name="build-201801032"></a>Build 20180103.2

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

- Consulte la sección [Nuevas características y correcciones](.\.\azure-stack-update-1712.md#new-features-and-fixes) de las notas de versión de la actualización 1712 de Azure Stack para sistemas integrados de Azure Stack.

    > [!IMPORTANT]
    > Algunos de los elementos que aparecen en la sección **Nuevas características y correcciones** solo son importantes para los sistemas integrados de Azure Stack.

### <a name="known-issues"></a>Problemas conocidos
 
#### <a name="deployment"></a>Implementación
- Debe especificar un servidor de hora mediante una dirección IP durante la implementación.

#### <a name="infrastructure-management"></a>Administración de la infraestructura
- No habilite la copia de seguridad de la infraestructura en la hoja **Infrastructure backup** (Copia de seguridad de la infraestructura).
- La dirección IP del controlador de administración de placa base (BMC) y el modelo no se muestran en la información esencial de un nodo de unidad de escala. Este comportamiento es el esperado en el Kit de desarrollo de Azure Stack.

#### <a name="portal"></a>Portal
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- El botón **Mover** está deshabilitado cuando se visualizan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
-  Para cualquier flujo de trabajo donde seleccione una suscripción, un grupo de recursos o una ubicación en una lista desplegable, puede experimentar uno o varios de los siguientes problemas:

   - Puede que vea una fila en blanco en la parte superior de la lista. Debería poder seleccionar un elemento según lo previsto.
   - Si la lista de elementos de la lista desplegable es breve, es posible que no se vean los nombres de los elementos.
   - Si tiene varias suscripciones de usuario, la lista desplegable de grupos de recursos podría estar vacía. 

   Para evitar los dos últimos problemas, puede escribir el nombre de la suscripción o el grupo de recursos (si lo conoce), o puede usar PowerShell en su lugar.

- Verá una altera de advertencia **Activación necesaria** que le informa de que debe registrar el Kit de desarrollo de Azure Stack. Este comportamiento es normal.
- Si se hace clic al vínculo **Componente** desde cualquier alerta de **rol de infraestructura**, la hoja **Introducción** resultante trata de cargarse y se produce un error. Además, en la hoja **Introducción** no se agota el tiempo de espera.
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, se pueden comprobar los permisos con PowerShell.
- La hoja de **Service Health** no se carga. Cuando se abre la hoja de Service Health en el portal del administrador o de usuario, Azure Stack muestra un error y no carga información. Este es el comportamiento esperado. Si bien es posible seleccionar y abrir Service Health, esta característica no está disponible aún, aunque se implementará en una versión futura de Azure Stack.
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
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.
- Es posible que los operadores de Azure Stack no puedan implementar, eliminar o modificar las redes virtuales o los grupos de seguridad de red. Este problema se ve principalmente en los intentos de actualización posteriores del mismo paquete. Esto se debe a un problema de empaquetado con una actualización que esté investigándose.
- El equilibrio de carga interno (ILB) administra incorrectamente las direcciones MAC de máquinas virtuales de back-end, que coloca paquetes en la red de back-end al utilizar instancias de Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.

#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.
 
#### <a name="usage"></a>Uso  
- El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.

#### <a name="identity"></a>Identidad

En entornos implementados de Servicios de federación de Active Directory (AD FS), la cuenta **azurestack\azurestackadmin** ya no es la propietaria de la suscripción de proveedor predeterminada. En lugar de iniciar sesión en el **portal de administración / punto de conexión adminmanagement** con **azurestack\azurestackadmin**, puede usar la cuenta **azurestack\cloudadmin**, de forma que puede administrar y usar la suscripción de proveedor predeterminada.

> [!IMPORTANT]
> Incluso la cuenta **azurestack\cloudadmin** es la propietaria de la suscripción de proveedor predeterminada en entornos implementados de ADFS, no tiene permisos para RDP en el host. Siga usando la cuenta **azurestack\azurestackadmin** o la cuenta de administrador local para iniciar sesión en el host, acceder a él y administrarlo según sea necesario.




## <a name="build-201711221"></a>Compilación 20171122.1

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

- Consulte la sección [Nuevas características y correcciones](.\.\azure-stack-update-1711.md#new-features-and-fixes) de las notas de versión de la actualización 1711 de Azure Stack para sistemas integrados de Azure Stack.

    > [!IMPORTANT]
    > Algunos de los elementos que aparecen en la sección **Nuevas características y correcciones** solo son importantes para los sistemas integrados de Azure Stack.

### <a name="known-issues"></a>Problemas conocidos

 
#### <a name="deployment"></a>Implementación
- Debe especificar un servidor de hora mediante una dirección IP durante la implementación.
- A partir de la versión 1711, **CloudAdmin** es un nombre de cuenta reservada y no debe especificarse manualmente al implementar el kit de desarrollo. 

#### <a name="infrastructure-management"></a>Administración de la infraestructura
- No habilite la copia de seguridad de la infraestructura en la hoja **Infrastructure backup** (Copia de seguridad de la infraestructura).
- La dirección IP del controlador de administración de placa base (BMC) y el modelo no se muestran en la información esencial de un nodo de unidad de escala. Este comportamiento es el esperado en el Kit de desarrollo de Azure Stack.

#### <a name="portal"></a>Portal
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- El botón **Mover** está deshabilitado cuando se visualizan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
-  Para cualquier flujo de trabajo donde seleccione una suscripción, un grupo de recursos o una ubicación en una lista desplegable, puede experimentar uno o varios de los siguientes problemas:

   - Puede que vea una fila en blanco en la parte superior de la lista. Debería poder seleccionar un elemento según lo previsto.
   - Si la lista de elementos de la lista desplegable es breve, es posible que no se vean los nombres de los elementos.
   - Si tiene varias suscripciones de usuario, la lista desplegable de grupos de recursos podría estar vacía. 

   Para evitar los dos últimos problemas, puede escribir el nombre de la suscripción o el grupo de recursos (si lo conoce), o puede usar PowerShell en su lugar.

- Verá una altera de advertencia **Activación necesaria** que le informa de que debe registrar el Kit de desarrollo de Azure Stack. Este comportamiento es normal.
- Si se hace clic al vínculo **Componente** desde cualquier alerta de **rol de infraestructura**, la hoja **Introducción** resultante trata de cargarse y se produce un error. Además, en la hoja **Introducción** no se agota el tiempo de espera.
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, se pueden comprobar los permisos con PowerShell.
- La hoja de **Service Health** no se carga. Cuando se abre la hoja de Service Health en el portal del administrador o de usuario, Azure Stack muestra un error y no carga información. Este es el comportamiento esperado. Si bien es posible seleccionar y abrir Service Health, esta característica no está disponible aún, aunque se implementará en una versión futura de Azure Stack.

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
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.
- Es posible que los operadores de Azure Stack no puedan implementar, eliminar o modificar las redes virtuales o los grupos de seguridad de red. Este problema se ve principalmente en los intentos de actualización posteriores del mismo paquete. Esto se debe a un problema de empaquetado con una actualización que esté investigándose.
- El equilibrio de carga interno (ILB) administra incorrectamente las direcciones MAC de máquinas virtuales de back-end, que coloca paquetes en la red de back-end al utilizar instancias de Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.

    > [!NOTE]
    > Consulte los artículos de configuración individuales para [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) y [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) para más información sobre la compatibilidad entre versiones.

#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

#### <a name="usage"></a>Uso  
- El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.

#### <a name="identity"></a>Identidad

En entornos implementados de Servicios de federación de Active Directory (AD FS), la cuenta **azurestack\azurestackadmin** ya no es la propietaria de la suscripción de proveedor predeterminada. En lugar de iniciar sesión en el **portal de administración / punto de conexión adminmanagement** con **azurestack\azurestackadmin**, puede usar la cuenta **azurestack\cloudadmin**, de forma que puede administrar y usar la suscripción de proveedor predeterminada.

> [!IMPORTANT]
> Incluso la cuenta **azurestack\cloudadmin** es la propietaria de la suscripción de proveedor predeterminada en entornos implementados de ADFS, no tiene permisos para RDP en el host. Siga usando la cuenta **azurestack\azurestackadmin** o la cuenta de administrador local para iniciar sesión en el host, acceder a él y administrarlo según sea necesario.

