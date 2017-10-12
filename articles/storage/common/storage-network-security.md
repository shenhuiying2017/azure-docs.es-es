---
title: "Configuración de Firewalls y redes virtuales de Azure Storage (versión preliminar) | Microsoft Docs"
description: Configure la seguridad de red por niveles para la cuenta de almacenamiento.
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/25/2017
ms.author: cbrooks
ms.openlocfilehash: de52e9cb32e28d2f40a56743ed759b5d5d0a63f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>Configuración de Firewalls y redes virtuales de Azure Storage (versión preliminar)
Azure Storage proporciona un modelo de seguridad por niveles que le permite proteger las cuentas de almacenamiento en un conjunto específico de redes permitidas.  Cuando se configuran las reglas de red, solo las aplicaciones de redes permitidas pueden acceder a una cuenta de almacenamiento.  Al llamar a desde una red permitida, las aplicaciones seguirán requiriendo la autorización adecuada (clave de acceso válida o token de SAS) para acceder a la cuenta de almacenamiento.

## <a name="preview-availability-and-support"></a>Disponibilidad y soporte de la versión preliminar
Firewalls y redes virtuales de Azure Storage se encuentra en versión preliminar.  Esta capacidad se encuentra actualmente disponible para las cuentas de almacenamiento nuevas o existentes en las siguientes regiones:
- Este de EE. UU.
- Oeste de EE. UU.
- Oeste de EE. UU. 2
- Centro occidental de EE.UU.
- Australia Oriental
- Sudeste de Australia

> [!NOTE]
> No se admiten cargas de trabajo de producción durante la versión preliminar.
>

## <a name="scenarios"></a>Escenarios
Las cuentas de almacenamiento se pueden configurar para denegar el acceso al tráfico desde todas las redes (incluido el tráfico de Internet) de forma predeterminada.  Puede conceder acceso al tráfico desde redes virtuales específicas de Azure, lo que permite generar un límite de red seguro para las aplicaciones.  También se puede conceder acceso a intervalos de direcciones IP de Internet públicos, lo que permite habilitar conexiones desde clientes locales o específicos de Internet.

Se aplican reglas de red en todos los protocolos de red para Azure Storage, incluidos REST y SMB.  El acceso a los datos desde herramientas como Azure Portal, Explorador de Storage y AZCopy requiere reglas de red explícitas que otorguen acceso cuando las reglas de red estén en vigor.

Las reglas de red se pueden aplicar a cuentas de almacenamiento existentes o se pueden aplicar durante la creación de nuevas cuentas de almacenamiento.

Una vez que se apliquen las reglas de red, se aplicarán a todas las solicitudes.  Los tokens de SAS que conceden acceso a un servicio de dirección IP específico sirven para **limitar** el acceso del titular del token, pero no conceden un acceso nuevo más allá de las reglas de red configuradas. 

El tráfico de disco de máquina virtual (incluidas las operaciones de montaje y desmontaje y las operaciones de E/S de disco) **no** se ve afectado por las reglas de red.  No se admite la copia de seguridad de discos no administrados para las cuentas de almacenamiento protegido durante la versión preliminar.  El acceso de REST a los blobs en páginas (usados para los discos de máquina virtual) está protegido por las reglas de red.

Las cuentas de almacenamiento clásicas **no** admite Firewalls and Virtual Networks (Firewalls y redes virtuales).

## <a name="change-the-default-network-access-rule"></a>Modificación de la regla de acceso de red predeterminada
De forma predeterminada, las cuentas de almacenamiento aceptan conexiones de clientes en cualquier red.  Para limitar el acceso a redes seleccionadas, primero debe cambiar la acción predeterminada.

> [!WARNING]
> La realización de cambios en reglas de red puede afectar a la capacidad de las aplicaciones de conexión a Azure Storage.  Si se establece la regla de red predeterminada en **denegar**, se bloquea el acceso a los datos a no ser que se apliquen también las reglas de red específicas que *conceden* acceso.  Asegúrese de conceder acceso a las redes permitidas con reglas de red antes de cambiar la regla predeterminada para denegar el acceso.
>

#### <a name="azure-portal"></a>Azure Portal
1. Vaya a la cuenta de almacenamiento que desea proteger.  
> [!NOTE]
> Asegúrese de que la cuenta de almacenamiento se encuentre en una de las regiones admitidas para la versión preliminar pública.
>

2. Haga clic en el menú de configuración denominado **Firewalls y redes virtuales**.
3. Para denegar el acceso de forma predeterminada, elija permitir el acceso desde "Redes seleccionadas".  Para permitir el tráfico desde todas las redes, elija permitir el acceso desde "Todas las redes".
4. Haga clic en *Guardar* para aplicar los cambios.

#### <a name="powershell"></a>PowerShell
1. Instale la última versión de [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [inicie sesión](/powershell/azure/authenticate-azureps).

2. Visualice el estado de la regla predeterminada para la cuenta de almacenamiento.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Establezca la regla predeterminada para denegar el acceso de red de forma predeterminada.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Establezca la regla predeterminada para permitir el acceso de red de forma predeterminada.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Instale la CLI de Azure 2.0](/cli/azure/install-azure-cli) e [inicie sesión](/cli/azure/authenticate-azure-cli).
2. Visualice el estado de la regla predeterminada para la cuenta de almacenamiento.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.defaultAction
```

3. Establezca la regla predeterminada para denegar el acceso de red de forma predeterminada.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Establezca la regla predeterminada para permitir el acceso de red de forma predeterminada.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Concesión de acceso desde una red virtual
Las cuentas de almacenamiento se pueden configurar para permitir el acceso solo desde redes virtuales específicas de Azure. 

Al habilitar un [punto de conexión de servicio](/azure/virtual-network/virtual-network-service-endpoints-overview) para Azure Storage en la red virtual, se garantiza al tráfico una ruta óptima para el servicio de Azure Storage. Las identidades de la red virtual y la subred también se transmiten con cada solicitud.  Los administradores pueden configurar posteriormente reglas de red para la cuenta de almacenamiento que permitan que se reciban solicitudes desde subredes específicas en la red virtual.  Los clientes a los que se concedió acceso a través de estas reglas de red deben seguir cumpliendo los requisitos de autorización de la cuenta de almacenamiento para acceder a los datos.

Cada cuenta de almacenamiento puede admitir hasta 100 reglas de red virtual, que se pueden combinar con [reglas de red IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiones de red virtual disponibles
En general, los puntos de conexión de servicio funcionan entre redes virtuales e instancias de servicio en la misma región de Azure.  Cuando se utilizan los puntos de conexión de servicio con Azure Storage, este ámbito se expande para incluir la [región emparejada](/azure/best-practices-availability-paired-regions).  Esto permite la continuidad durante una conmutación por error regional, así como un acceso sin problemas a las instancias de almacenamiento con redundancia geográfica de solo lectura (RA-GRS).  Las reglas de red que conceden acceso de una red virtual a una cuenta de almacenamiento también conceden acceso a cualquier instancia de RA-GRS.

Al planear la recuperación ante desastres durante una interrupción regional, debe aprovisionar por adelantado las redes virtuales en la región emparejada. Se deben habilitar los puntos de conexión de servicio para Azure Storage y deben aplicarse las reglas de red que conceden acceso desde estas redes virtuales alternativas a las cuentas de almacenamiento con redundancia geográfica.

> [!NOTE]
> Los puntos de conexión de servicio no se aplican al tráfico fuera de la región de la red virtual y el par de región designado.  Las reglas de red que conceden acceso de redes virtuales a las cuentas de almacenamiento solo se pueden aplicar para las redes virtuales en la región principal de una cuenta de almacenamiento o en la región emparejada designada.
>

### <a name="required-permissions"></a>Permisos necesarios
Para aplicar una regla de red virtual a una cuenta de almacenamiento, el usuario debe tener permiso para *unir el servicio a una subred* para las subredes que se van a agregar.  Este permiso se incluye en el rol integrado de *colaborador de la cuenta de almacenamiento* y se puede agregar a definiciones de roles personalizados.

El acceso concedido a la cuenta de almacenamiento y a las redes virtuales **puede** darse en distintas suscripciones, pero esas suscripciones deben formar parte del mismo inquilino de Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Administración de reglas de red virtual
Las reglas de red virtual para las cuentas de almacenamiento se pueden administrar a través de Azure Portal, PowerShell o CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Vaya a la cuenta de almacenamiento que desea proteger.  
2. Haga clic en el menú de configuración denominado **Firewalls y redes virtuales**.
3. Asegúrese de que ha elegido permitir el acceso desde "Redes seleccionadas".
4. Para conceder acceso a una red virtual con una nueva regla de red, en "Redes virtuales", haga clic en "Agregar existente" para seleccionar una red virtual existente y subredes y, a continuación, haga clic en *Agregar*.  Para crear una nueva red virtual y concederle acceso, haga clic en *Agregar nueva*, proporcione la información necesaria para crear la nueva red virtual y, a continuación, haga clic en *Crear*.

> [!NOTE]
> Si un punto de conexión de servicio para Azure Storage no se ha configurado previamente para la red virtual y las subredes seleccionadas, se puede configurar como parte de esta operación.
>

5. Para quitar una regla de red virtual o subred, haga clic en "..." para abrir el menú contextual de la red Virtual o la subred, y haga clic en "Quitar".
6. Haga clic en *Guardar* para aplicar los cambios.

#### <a name="powershell"></a>PowerShell
1. Instale la última versión de [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [inicie sesión](/powershell/azure/authenticate-azureps).
2. Enumere las reglas de red virtual.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Habilite el punto de conexión de servicio para Azure Storage en una red virtual y subred existentes.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Agregue una regla de red para una red virtual y subred.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Quite una regla de red para una red virtual y subred.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Asegúrese de [establecer la regla predeterminada](#change-the-default-network-access-rule) para la denegación o las reglas de red no tendrán ningún efecto.
>

#### <a name="cliv2"></a>CLIv2
1. [Instale la CLI de Azure 2.0](/cli/azure/install-azure-cli) e [inicie sesión](/cli/azure/authenticate-azure-cli).
2. Enumere las reglas de red virtual.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Habilite el punto de conexión de servicio para Azure Storage en una red virtual y subred existentes.
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Agregue una regla de red para una red virtual y subred.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule add --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

4. Quite una regla de red para una red virtual y subred. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule remove --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

> [!IMPORTANT]
> Asegúrese de [establecer la regla predeterminada](#change-the-default-network-access-rule) para la denegación o las reglas de red no tendrán ningún efecto.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Concesión de acceso desde un intervalo IP de Internet
Las cuentas de almacenamiento pueden configurarse para permitir el acceso desde intervalos específicos de direcciones IP de Internet público.  Esta configuración permite que se conceda acceso a los servicios específicos basados en Internet y redes locales mientras se bloquea el tráfico de Internet general.

Los intervalos de dirección de Internet pueden proporcionarse mediante la [notación CIDR](https://tools.ietf.org/html/rfc4632) en el formulario *16.17.18.0/24* o como direcciones IP individuales como *16.17.18.19* .

> [!NOTE]
> Los intervalos de dirección pequeños con tamaños de prefijos "/31" o "/32" no son compatibles.  Estos intervalos se deberían configurar utilizando reglas de direcciones IP individuales.
>

Las reglas de red IP solo se permiten para direcciones IP de **Internet público**.  No se permiten intervalos de direcciones IP reservados para redes privadas (tal y como se define en RFC 1918) en las reglas IP.  Las redes privadas incluyen direcciones que comienzan por *10.\** , *172.16.\**  y *192.168.\** .

Solo se admiten direcciones IPV4 en este momento.

Cada cuenta de almacenamiento puede admitir hasta 100 reglas de red IP, que se pueden combinar con [reglas de red virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configuración del acceso desde redes locales
Para conceder acceso desde las redes locales a la cuenta de almacenamiento con una regla de red IP, debe identificar las direcciones IP orientadas a Internet que usa su red.  Para obtener ayuda, póngase en contacto con el administrador de red.

Si la red está conectada a la red de Azure mediante [ExpressRoute](/azure/expressroute/expressroute-introduction), cada circuito se configura con dos direcciones IP públicas en Microsoft Edge que se utilizan para conectarse a Microsoft Services como Azure Storage mediante [emparejamientos públicos de Azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Para permitir la comunicación desde el circuito a Azure Storage, debe crear reglas de red IP para direcciones IP públicas de los circuitos.  Para encontrar las direcciones IP públicas del circuito de ExpressRoute, [abra un vale de soporte con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a través de Azure Portal.


### <a name="managing-ip-network-rules"></a>Administración de reglas de red IP
Las reglas de red IP para las cuentas de almacenamiento se pueden administrar a través de Azure Portal, PowerShell o CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Vaya a la cuenta de almacenamiento que desea proteger.  
2. Haga clic en el menú de configuración denominado **Firewalls y redes virtuales**.
3. Asegúrese de que ha elegido permitir el acceso desde "Redes seleccionadas".
4. Para conceder acceso al intervalo IP de Internet, escriba la dirección IP o el intervalo de direcciones (en formato CIDR) en Firewall de, Intervalos de direcciones.
5. Para quitar una regla de red IP, haga clic en "..." para abrir el menú contextual de la regla y haga clic en "Quitar".
6. Haga clic en *Guardar* para aplicar los cambios.

#### <a name="powershell"></a>PowerShell
1. Instale la última versión de [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [inicie sesión](/powershell/azure/authenticate-azureps).
2. Enumere las reglas de red IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Agregue una regla de red para una dirección IP individual.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Agregue una regla de red para un intervalo de direcciones IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Quite una regla de red para una dirección IP individual. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Quite una regla de red para un intervalo de direcciones IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Asegúrese de [establecer la regla predeterminada](#change-the-default-network-access-rule) para la denegación o las reglas de red no tendrán ningún efecto.
>

#### <a name="cliv2"></a>CLIv2
1. [Instale la CLI de Azure 2.0](/cli/azure/install-azure-cli) e [inicie sesión](/cli/azure/authenticate-azure-cli).
2. Enumere las reglas de red IP.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Agregue una regla de red para una dirección IP individual.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Agregue una regla de red para un intervalo de direcciones IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Quite una regla de red para una dirección IP individual.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Quite una regla de red para un intervalo de direcciones IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Asegúrese de [establecer la regla predeterminada](#change-the-default-network-access-rule) para la denegación o las reglas de red no tendrán ningún efecto.
>

## <a name="exceptions"></a>Excepciones
Aunque las reglas de red pueden habilitar una configuración de red segura para la mayoría de los escenarios, hay algunos casos en los que se deben conceder excepciones para permitir la funcionalidad completa.  Las cuentas de almacenamiento se pueden configurar con excepciones para los servicios de confianza de Microsoft y para el acceso a los datos de análisis de almacenamiento.

### <a name="trusted-microsoft-services"></a>Servicios de Microsoft de confianza
Algunos servicios de Microsoft que interactúan con las cuentas de almacenamiento funcionan desde redes a las que no se puede conceder acceso a través de reglas de red. 

Para permitir que este tipo de servicio funcione según lo previsto, puede permitir que el conjunto de servicios de Microsoft de confianza omita las reglas de red. Estos servicios usarán entonces una autenticación sólida para acceder a la cuenta de almacenamiento.

Cuando la excepción "Servicios de Microsoft de confianza" está habilitada, se les concede acceso a la cuenta de almacenamiento a los siguientes servicios (cuando se han registrado en su suscripción):

|Servicio|Nombre del proveedor de recursos|Propósito|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|Creación de imagen personalizada e instalación de artefactos.  [Más información](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Habilitación de publicación de eventos de Blob Storage.  [Más información](https://docs.microsoft.com/en-us/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Archivo de datos con Event Hubs Capture.  [Más información](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-capture-overview).|
|HDInsight de Azure|Microsoft.HDInsight|Aprovisionamiento e instalación de clústeres.  [Más información](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage).|
|Conexión a Azure|Microsoft.Networking|Almacenamiento y análisis de los registros de tráfico de red.  [Más información](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-packet-capture-overview).|
|Almacenamiento de datos SQL de Azure|Microsoft.Sql|Importación y exportación de datos.  [Más información](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-overview-load#load-from-azure-blob-storage).|
||||

### <a name="storage-analytics-data-access"></a>Acceso a datos de análisis de almacenamiento
En algunos casos, se requiere acceso para leer registros de diagnóstico y métricas desde fuera del límite de red.  Las excepciones a las reglas de red se pueden conceder para permitir acceso de lectura a las tablas de métricas, archivos de registro de cuentas de almacenamiento o ambos. [Obtenga más información sobre cómo trabajar con analíticas de almacenamiento](/azure/storage/storage-analytics).

### <a name="managing-exceptions"></a>Administración de excepciones
Las excepciones de reglas de red pueden administrarse a través de Azure Portal, PowerShell o la CLI de Azure v2.

#### <a name="azure-portal"></a>Azure Portal
1. Vaya a la cuenta de almacenamiento que desea proteger.  
2. Haga clic en el menú de configuración denominado **Firewalls y redes virtuales**.
3. Asegúrese de que ha elegido permitir el acceso desde "Redes seleccionadas".
4. En Excepciones, seleccione las excepciones que se va a conceder.
5. Haga clic en *Guardar* para aplicar los cambios.

#### <a name="powershell"></a>PowerShell
1. Instale la última versión de [Azure PowerShell](/powershell/azure/install-azurerm-ps) e [inicie sesión](/powershell/azure/authenticate-azureps).
2. Vea las excepciones para las reglas de red de la cuenta de almacenamiento.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Configure las excepciones a las reglas de red de la cuenta de almacenamiento.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Quite las excepciones a las reglas de red de la cuenta de almacenamiento.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Asegúrese de [establecer la regla predeterminada](#change-the-default-network-access-rule) para la denegación o a eliminación de las excepciones no tendrá ningún efecto.
>

#### <a name="cliv2"></a>CLIv2
1. [Instale la CLI de Azure 2.0](/cli/azure/install-azure-cli) e [inicie sesión](/cli/azure/authenticate-azure-cli).
2. Vea las excepciones para las reglas de red de la cuenta de almacenamiento.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.bypass
```

3. Configure las excepciones a las reglas de red de la cuenta de almacenamiento.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Quite las excepciones a las reglas de red de la cuenta de almacenamiento.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Asegúrese de [establecer la regla predeterminada](#change-the-default-network-access-rule) para la denegación o a eliminación de las excepciones no tendrá ningún efecto.
>

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información acerca de los puntos de conexión de servicio de red de Azure en [Puntos de conexión de servicio](/azure/virtual-network/virtual-network-service-endpoints-overview).

Profundice en la seguridad de Azure Storage en la [Guía de seguridad de Azure Storage](storage-security-guide.md).
