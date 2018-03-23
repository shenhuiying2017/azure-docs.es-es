---
title: Actualización 1802 de Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 1802 de los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse la actualización.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 247f13717971d3660b3ec0ee94821bd593c5fed0
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-1802-update"></a>Actualización 1802 de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de este paquete de actualización 1802, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]        
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación    
El número de compilación de la actualización 1802 de Azure Stack es **20180302.1**.  


## <a name="before-you-begin"></a>Antes de empezar    
> [!IMPORTANT]    
> No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](/azure-stack-updates#plan-for-updates).


### <a name="prerequisites"></a>requisitos previos
- Antes de aplicar la actualización 1802 de Azure Stack, instale la [actualización 1712](azure-stack-update-1712.md).    

- Antes de aplicar la actualización 1812 de Azure Stack, instale **AzS Hotfix – 1.0.180312.1- Build 20180222.2**. Esta revisión actualiza Windows Defender, y está disponible al descargar actualizaciones para Azure Stack.

  Para instalar la revisión, siga los procedimientos normales para [instalar actualizaciones de Azure Stack](azure-stack-apply-updates.md). El nombre de la actualización aparece como **AzS Hotfix – 1.0.180312.1** e incluye los siguientes archivos: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2 1.bin
    - Metadata.xml

  Después de cargar estos archivos en una cuenta de almacenamiento y un contenedor, ejecute la instalación desde el icono de actualización en el portal de administración. 
  
  A diferencia de las actualizaciones de Azure Stack, al instalar esta actualización no cambia la versión de Azure Stack.  Para confirmar que esta actualización está instalada, vea la lista de **actualizaciones instaladas**.
 


### <a name="post-update-steps"></a>Pasos posteriores a la actualización
*No hay ningún paso posterior a la actualización para la actualización 1802.*


### <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Esta actualización incluye las siguientes correcciones y mejoras para Azure Stack.

- **Se ha agregado compatibilidad con las siguientes versiones de API de servicio de Azure Storage**:
    - 17-04-2017 
    - 31-05-2016 
    - 11-12-2015 
    - 08-07-2015 
    
    Para más información, consulte [Azure Stack Storage: Diferencias y consideraciones](/azure/azure-stack/user/azure-stack-acs-differences).

- **Compatibilidad con [blobs en bloques](azure-stack-acs-differences.md)** más grandes:
    - El tamaño máximo de bloque permitido ha aumentado de 4 MB a 100 MB.
    - El tamaño máximo de blob ha aumentado de 195 GB a 4,75 TB.  

- La **copia de seguridad de infraestructura** aparece ahora en el icono de proveedores de recursos y están habilitadas alertas de copia de seguridad. Para más información sobre el servicio Copia de seguridad de infraestructura, consulte [Copia de seguridad y recuperación de datos para Azure Stack con el servicio Copia de seguridad de infraestructura](azure-stack-backup-infrastructure-backup.md).

- **Actualice al cmdlet *Test-AzureStack*** para mejorar el diagnóstico de almacenamiento. Para más información sobre este cmdlet, consulte [Validación de Azure Stack](azure-stack-diagnostic-test.md).

- **Mejoras de control de acceso (RBAC) basado en roles**: ahora puede usar RBAC para delegar permisos en grupos de usuarios universales cuando se implementa Azure Stack con AD FS. Para más información sobre RBAC, consulte [Administración de RBAC](azure-stack-manage-permissions.md).

- **Se agregó compatibilidad con varios dominios de error**.  Para más información, consulte [High availability for Azure Stack](azure-stack-key-features.md#high-availability-for-azure-stack) (Alta disponibilidad para Azure Stack).

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización    
*No hay ningún problema conocido con la instalación de la actualización 1802.*


### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)
Los siguientes son problemas conocidos posteriores a la instalación de la compilación **20180302.1**

#### <a name="portal"></a>Portal
- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](azure-stack-manage-portals.md#quick-access-to-help-and-support) del portal de administración. En su lugar, use el siguiente vínculo:     
    - Para los sistemas integrados de Azure Stack, use https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- No es posible ver los recursos de proceso o almacenamiento en el portal de administración. La causa de este problema es un error durante la instalación de la actualización que hace que se notifique como correcta erróneamente. Si se produce este problema, póngase en contacto con los servicios de soporte técnico de Microsoft para obtener ayuda.

- Puede que vea un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.

- Al ver las propiedades de un recurso o grupo de recursos, el botón **Mover** está deshabilitado. Este comportamiento es normal. Actualmente no se admite mover recursos o grupos de recursos entre grupos de recursos o suscripciones.

- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.

- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use PowerShell para comprobar los permisos.

- En el panel del portal de administración, el icono de actualización no muestra información sobre las actualizaciones. Para resolver este problema, haga clic en el icono para actualizarlo.

-   En el portal de administración, puede que vea una alerta crítica del componente Microsoft.Update.Admin. El nombre de la alerta, la descripción y la solución se muestran como:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERROR: falta la plantilla de FaultType ResourceProviderTimeout)

    Esta alerta puede omitirse sin problemas. 

#### <a name="health-and-monitoring"></a>Estado y supervisión
No hay ningún problema conocido después de actualizar a 1802.

#### <a name="marketplace"></a>Marketplace
- Los usuarios pueden examinar toda la plataforma Marketplace sin una suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.

#### <a name="compute"></a>Proceso
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

- Azure Stack solo admite el uso de discos duros virtuales de tipo fijo. Algunas imágenes ofrecidas mediante Marketplace en Azure Stack usan discos duros virtuales dinámicos, pero estas se han quitado. El hecho de cambiar el tamaño de una máquina virtual (VM) que lleva asociado un disco dinámico deja a la máquina en estado de error.

  Para mitigar este problema, elimine la máquina virtual sin eliminar el disco de la máquina virtual, un blob de disco duro virtual de una cuenta de almacenamiento. A continuación, convierta el disco duro virtual dinámico a fijo y vuelva a crear la máquina virtual.

- Cuando crea un conjunto de disponibilidad en el portal en **Nuevo** > **Compute** > **Conjunto de disponibilidad**, solo puede crear uno con un dominio de error y un dominio de actualización de 1. Como alternativa, al crear una nueva máquina virtual, cree el conjunto de disponibilidad mediante PowerShell, la CLI o el portal.

- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie DS. Las máquinas virtuales de la serie DS pueden albergar tantos discos de datos como la configuración de Azure.

- Cuando no es posible crear una imagen de máquina virtual, los elementos con error que no se pueden eliminar se podrían agregar a la hoja de proceso de imágenes de máquina virtual.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo error.

-  Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>Redes
- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.

- El equilibrio de carga interno (ILB) administra incorrectamente las direcciones MAC de máquinas virtuales de back-end, lo que hace que se interrumpa cuando se usan instancias de Linux en la red de back-end.  ILB funciona bien con instancias de Windows en la red de back-end.

-   La característica de reenvío IP está visible en el portal, sin embargo, no ocurre nada al habilitar esta característica. El motivo es que aún no se admite.

- Azure Stack admite una única *puerta de enlace de red* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

- En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.

- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Solución alternativa para App Service: si tiene que establecer una conexión de Escritorio remoto a las instancias del controlador, modifique las reglas de seguridad de los grupos de seguridad de red con PowerShell.  Estos son algunos ejemplos de cómo *permitir* y, luego, restaurar la configuración a *denegar*:  
    
    - *Permitir:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Denegar:*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL y MySQL
 - Antes de continuar, revise la nota importante en [Antes de empezar](#before-you-begin) cerca del comienzo de estas notas de la versión.
- Puede pasar una hora antes de que los usuarios puedan crear bases de datos en una nueva implementación de SQL o MySQL.

- Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.  


> [!NOTE]  
> Después de actualizar a Azure Stack 1802, puede seguir usando los proveedores de recursos SQL y MySQL que implementó anteriormente.  Le recomendamos que actualice SQL y MySQL cuando haya disponible una nueva versión. Al igual que Azure Stack, aplique las actualizaciones a los proveedores de recursos SQL y MySQL de manera secuencial.  Por ejemplo, si usa la versión 1710, aplique primero la versión 1711, luego la versión 1712 y luego actualice a la 1802.      
>   
> La instalación de la actualización 1802 no afecta el uso actual de los proveedores de recursos SQL o MySQL por parte de los usuarios.
> Con independencia de la versión que use de los proveedores de recursos, los datos de los usuarios de sus bases de datos no se tocan, y permanecen accesibles.    


#### <a name="app-service"></a>App Service
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Descarga de las herramientas de Azure Stack desde GitHub
- Al usar el cmdlet *invoke-webrequest* de PowerShell para descargar las herramientas de Azure Stack de Github, recibirá un error:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest: anulada la solicitud, no se pudo crear un canal seguro SSL/TLS)     

  Este error se produce debido a una degradación reciente de la compatibilidad con GitHub de los estándares criptográficos Tlsv1 y Tlsv1.1 (los predeterminados de PowerShell). Para más información, consulte [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Aviso de eliminación de estándares criptográficos débiles).

  Para resolver este problema, agregue `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a la parte superior del script para obligar a la consola de PowerShell a usar TLSv1.2 cuando se descargue de los repositorios de GitHub.


## <a name="download-the-update"></a>Descarga de la actualización
Puede descargar la actualización 1802 de Azure Stack [aquí](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Más información
Microsoft proporcionó una manera de supervisar y reanudar las actualizaciones mediante el punto de conexión con privilegios (PEP) que se instaló con la actualización 1710.

- Consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Otras referencias

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
