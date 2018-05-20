---
title: Protección del acceso a una cuenta de Azure Cosmos DB con el uso de un punto de conexión de servicio de Azure Virtual Network | Microsoft Docs
description: En este documento se describen los pasos necesarios para configurar el punto de conexión de servicio de Virtual Network en Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: b07a159e69a11656555a8550b807cce0b2c9ef6c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Protección del acceso a una cuenta de Azure Cosmos DB con el uso de un punto de conexión de servicio de Azure Virtual Network

Las cuentas de Azure CosmosDB se pueden configurar para permitir el acceso solo a una subred específica de Azure Virtual Network. Al habilitar un [punto de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md) para Azure CosmosDB desde una instancia de Virtual Network y su subred, se garantiza un enrutamiento óptimo y seguro del tráfico a Azure Cosmos DB.  

Azure Cosmos DB es un servicio de base de datos de varios modelos distribuido globalmente. Puede replicar los datos presentes en la cuenta de Azure Cosmos DB en varias regiones. Cuando se configura Azure Cosmos DB con un punto de conexión de servicio de Virtual Network, cada red virtual permite acceder desde las direcciones IP que pertenecen a una subred específica. En la siguiente imagen se muestra una ilustración de una instancia de Azure Cosmos DB que tiene habilitado un punto de conexión de servicio de Virtual Network:

![arquitectura del punto de conexión de servicio de Virtual Network](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Una vez configurada la cuenta de Azure Cosmos DB con un punto de conexión de servicio de Virtual Network, solo se puede acceder a ella desde la subred especificada; de hecho, se elimina todo el acceso público y desde Internet. Para obtener información detallada sobre los puntos de conexión de servicio, vaya al artículo de [información general sobre los puntos de servicio de Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md).

> [!NOTE]
> Actualmente, los puntos de conexión de servicio de Virtual Network se pueden configurar para la API de SQL de Azure Cosmos DB o las cuentas de la API de Mongo. Pronto se ofrecerá la posibilidad de configurar los puntos de conexión de servicio para otras API y nubes soberanas, como Azure Alemania o Azure Government. Si tiene un firewall para direcciones IP existente configurado para la cuenta de Azure Cosmos DB, tenga en cuenta la configuración del firewall, quite el firewall para direcciones IP y luego configure la ACL del punto de conexión de servicio. Una vez configurado el punto de conexión de servicio, puede volver a habilitar el firewall para las direcciones IP si es necesario.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Configuración del punto de conexión de servicio en Azure Portal
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configuración del punto de conexión de servicio para una subred y red virtual de Azure existentes

1. En la hoja **Todos los recursos**, busque la red virtual en la que desea configurar el punto de conexión de servicio para Azure Cosmos DB. Vaya a la hoja **Puntos de conexión de servicio** y asegúrese de que se haya habilitado la subred de la red virtual para el punto de conexión de servicio "Azure.CosmosDB".  

   ![Confirmación de que se ha habilitado el punto de conexión de servicio](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. En la hoja **Todos los recursos**, busque la cuenta de Azure Cosmos DB que desea proteger.  

3. Antes de habilitar el punto de conexión de servicio de Virtual Network, copie la información del firewall para las direcciones IP asociada con la cuenta de Azure Cosmos DB para su futuro uso. Puede volver a habilitar el firewall para las direcciones IP después de configurar el punto de conexión de servicio.  

4. Seleccione **Firewalls y redes virtuales** en el menú de configuración y elija Permitir el acceso en **Redes seleccionadas**.  

3. Para conceder acceso a la subred de una red virtual existente, en Redes virtuales, seleccione **Add existing Azure virtual network** (Agregar red virtual de Azure existente).  

4. Seleccione la **suscripción** en la que quiere agregar la red virtual de Azure. Seleccione las **redes virtuales** y las **subredes** de Azure a las que desea conceder acceso a la cuenta de Azure Cosmos DB. A continuación, seleccione **Habilitar** para habilitar las redes seleccionadas con los puntos de conexión de servicio para "Microsoft.AzureCosmosDB". Cuando haya finalizado, seleccione **Agregar**.  

   ![Selección de la red virtual y la subred](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Si un punto de conexión de servicio para Azure Cosmos DB no se ha configurado previamente para las redes virtuales y las subredes de Azure seleccionadas, se puede configurar como parte de esta operación. El proceso para habilitar el acceso puede tardar hasta 15 minutos en completarse. 

   ![red virtual y subred configuradas correctamente](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Ahora, la cuenta de Azure Cosmos DB solo admitirá el tráfico desde esta subred seleccionada. Si ya habilitó el firewall para las direcciones IP, vuelva a habilitarlo con la información anterior.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configuración del punto de conexión de servicio para una subred y red virtual de Azure nuevas

1. En la hoja **Todos los recursos**, busque la cuenta de Azure Cosmos DB que desea proteger.  

2. Antes de habilitar el punto de conexión de servicio de Virtual Network, copie la información del firewall para las direcciones IP asociada con la cuenta de Azure Cosmos DB para su futuro uso. Puede volver a habilitar el firewall para las direcciones IP después de configurar el punto de conexión de servicio.  

3. Seleccione **Firewalls y redes virtuales de Azure** en el menú de configuración y elija Permitir el acceso en **Redes seleccionadas**.  

4. Para conceder acceso a la subred de una red virtual de Azure nueva, en Redes virtuales, seleccione **Agregar nueva red virtual**.  

5. Proporcione los detalles necesarios para crear una red virtual y, a continuación, seleccione Crear. La subred se creará con un punto de conexión de servicio para "Microsoft.AzureCosmosDB" habilitado.

   ![Selección de la red virtual y la subred para la red virtual nueva](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Permitir el acceso desde Azure Portal

Una vez que los puntos de conexión de servicio de Azure Virtual Network están habilitados para la cuenta de base de datos de Azure Cosmos DB, el acceso desde Azure Portal o desde otros servicios de Azure está deshabilitado de forma predeterminada. El acceso a la cuenta de base de datos de Azure Cosmos DB desde máquinas que están fuera de la subred configurada está bloqueado, incluido el acceso desde Azure Portal.

![Permitir el acceso desde Azure Portal](./media/vnet-service-endpoint/allow-access-from-portal.png)

Si otros servicios de Azure, como Azure Search, usan la cuenta de Azure Cosmos DB, o bien si se accede a ella desde Stream Analytics o Power BI, podrá permitir el acceso si marca la opción **Permitir acceso al servicio de Azure**.

Para garantizar el acceso a las métricas de Azure Cosmos DB desde Azure Portal, debe habilitar las opciones **Allow access to Azure portal** (Permitir acceso a Azure Portal). Para más información sobre estas opciones, vea [Conexiones desde Azure Portal](firewall-support.md#connections-from-the-azure-portal) y [Conexiones desde otros servicios PaaS de Azure](firewall-support.md#connections-from-other-azure-paas-services). Después de seleccionar el acceso, seleccione **Guardar** para guardar la configuración.

## <a name="remove-a-virtual-network-or-subnet"></a>Eliminación de una red virtual o subred 

1. En la hoja **Todos los recursos**, busque la cuenta de Azure Cosmos DB a la que ha asignado los puntos de conexión de servicio.  

2. Seleccione **Firewalls y redes virtuales** en el menú de configuración.  

3. Para eliminar una regla de subred o red virtual, seleccione "..." junto a la red virtual o subred y luego **Eliminar**.

   ![Eliminación de una red virtual](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Haga clic en **Guardar** para aplicar los cambios.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Configuración del punto de conexión de servicio mediante Azure PowerShell 

Use los pasos siguientes para configurar el punto de conexión de servicio de una cuenta de Azure Cosmos DB mediante Azure PowerShell:  

1. Instale la última versión de [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) e [inicie sesión](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Asegúrese de anotar la configuración del firewall para las direcciones IP y elimine el firewall por completo antes de habilitar el punto de conexión de servicio para la cuenta.

2. Antes de habilitar el punto de conexión de servicio de Virtual Network, copie la información del firewall para las direcciones IP asociada con la cuenta de Azure Cosmos DB para su futuro uso. Volverá a habilitar el firewall para las direcciones IP después de configurar el punto de conexión de servicio.  

3. Habilite el punto de conexión de servicio de una subred existente de una red virtual.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. Prepárese para habilitar la ACL en la cuenta de Cosmos DB; para ello, asegúrese de que la red virtual y la subred tienen el punto de conexión de servicio habilitado para Azure Cosmos DB.

   ```powershell
   $subnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName  | Get-AzureRmVirtualNetworkSubnetConfig -Name $sname
   $vnProp = Get-AzureRmVirtualNetwork `-Name $vnName  -ResourceGroupName $rgName
   ```

5. Obtenga las propiedades de la cuenta de Azure Cosmos DB mediante la ejecución del siguiente cmdlet:  

   ```powershell
   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inicialice las variables para su uso posterior. Configure todas las variables de la definición de la cuenta existente; si tiene varias ubicaciones, debe agregarlas como parte de la matriz. En este paso, también configurará el punto de conexión de servicio de Virtual Network al definir la variable "accountVNETFilterEnabled" en "True". Este valor se asigna más adelante al parámetro "isVirtualNetworkFilterEnabled".  

   ```powershell
   $locations = @(@{})
   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $subnetName  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Actualice las propiedades de la cuenta de Azure Cosmos DB con la nueva configuración mediante la ejecución de los siguientes cmdlets: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource ``
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Ejecute el siguiente comando para verificar que la cuenta de Azure Cosmos DB se ha actualizado con el punto de conexión de servicio de Virtual Network que configuró en el paso anterior:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Adición de un punto de conexión de servicio de Virtual Network a una cuenta de Azure Cosmos DB que tiene el firewall para las direcciones IP habilitado

1. Primero deshabilite el acceso del firewall para las direcciones IP a la cuenta de Azure Cosmos DB.  

2. Habilite el punto de conexión de Virtual Network a la cuenta de Azure Cosmos DB con el uso de uno de los métodos descritos en las secciones anteriores.  

3. Vuelva a habilitar el acceso al firewall para las direcciones IP. 

## <a name="test-the-access"></a>Prueba del acceso

Para comprobar si los puntos de conexión de servicio para Azure Cosmos DB están configurados según lo previsto, aplique los pasos siguientes:

* Configure las dos subredes de una red virtual como front-end y back-end, habilite el punto de conexión de servicio de Cosmos DB para la subred de back-end.  

* Habilite el acceso en la cuenta de Cosmos DB para la subred de back-end.  

* Cree dos máquinas virtuales, una en la subred de back-end y otra en la subred de front-end.  

* Intente acceder a la cuenta de Azure Cosmos DB desde ambas máquinas virtuales. Debe ser capaz de conectarse desde la máquina virtual creada en la subred de back-end, pero no desde la que se creo en la subred de front-end. La solicitud generará un error 404 al intentar conectarse desde la subred de front-end, que confirma que el acceso a Azure Cosmos DB está protegido mediante el uso del punto de conexión de servicio de Virtual Network. La máquina de la subred de back-end funcionará correctamente.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>¿Qué ocurre si accede a una cuenta de Azure Cosmos DB que tiene habilitada la lista de control de acceso (ACL) de Virtual Network?  

Se devuelve el error HTTP 404.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>¿Las subredes de una máquina virtual creada en regiones distintas pueden acceder a una cuenta de Azure Cosmos DB en otra región? Por ejemplo, si la cuenta de Azure Cosmos DB está en Oeste de EE. UU. o Este de EE. UU. y las redes virtuales están en varias regiones, ¿la red virtual puede acceder a Azure Cosmos DB?  

Sí, las redes virtuales creadas en regiones diferentes pueden acceder a la nueva funcionalidad. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>¿Una cuenta de Azure Cosmos DB puede tener un punto de conexión de servicio de Virtual Network y un firewall?  

Sí, el punto de conexión de servicio de Virtual Network y un firewall pueden coexistir. En general, debe asegurarse de que el acceso a Azure Portal está siempre habilitado antes de configurar un punto de conexión de servicio de Virtual Network para poder ver las métricas asociadas con el contenedor.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>¿Puedo "permitir el acceso a otros servicios de Azure desde una región de Azure determinada" si el acceso del punto de conexión de servicio está habilitado para Azure Cosmos DB?  

Esto solo es necesario si desea que otros servicios propios de Azure accedan a la cuenta de Azure Cosmos DB, como Azure Data Factory, Azure Search o cualquier servicio implementado en una región de Azure determinada.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>¿Cuántos puntos de conexión de servicio de Virtual Network se necesitan para Azure Cosmos DB?  

Se admiten sesenta y cuatro puntos de conexión de servicio de Virtual Network para una cuenta de Azure Cosmos DB.

### <a name="what-is-the-relationship-of-service-endpoint-with-respect-to-network-security-group-nsg-rules"></a>¿Cuál es la relación del punto de conexión de servicio con respecto a las reglas del grupo de seguridad de red (NSG)?  

La regla de Azure Cosmos DB del NSG permite restringir el acceso solo al intervalo de direcciones IP de Azure Cosmos DB.
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>¿Cuál es la relación entre un firewall para direcciones IP y la funcionalidad del punto de conexión de servicio de Virtual Network?  

Estas dos características se complementan entre sí para garantizar el aislamiento de los recursos de Azure Cosmos DB y protegerlos. El uso del firewall para las direcciones IP garantiza que las direcciones IP estáticas puedan acceder a la cuenta de Azure Cosmos DB.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>¿La dirección IP de un dispositivo local conectada a través de la puerta de enlace de Azure Virtual Network (VPN) o de la puerta de enlace de ExpressRoute puede acceder a la cuenta de Azure Cosmos DB?  

La dirección IP o el intervalo de direcciones IP de un dispositivo local deben agregarse a la lista de direcciones IP estáticas para poder acceder a la cuenta de Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>¿Qué ocurre si elimina una red virtual que tiene un punto de conexión de servicio configurado para Azure Cosmos DB?  

Cuando se elimina una red virtual, la información de la ACL asociada con dicha cuenta de Azure Cosmos DB también se elimina y, además, se elimina la interacción entre la red virtual y la cuenta de Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>¿Qué ocurre si se elimina una cuenta de Azure Cosmos DB que tiene habilitado un punto de conexión de servicio de Virtual Network?

Los metadatos asociados con la cuenta específica de Azure Cosmos DB se eliminan de la subred. Además, el usuario final es responsable de eliminar la subred y la red virtual utilizadas.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>¿Puede usar una red virtual emparejada para crear el punto de conexión de servicio para Azure Cosmos DB?  

No, solo la red virtual y sus subredes pueden crear los puntos de conexión de servicio de Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>¿Qué ocurre con la dirección IP de origen del recurso, como una máquina virtual de la subred que tiene el punto de conexión de servicio de Azure Cosmos DB habilitado?

Si los puntos de conexión de servicio de Virtual Network están habilitados, las direcciones IP de los recursos de la subred de la red virtual pasarán de usar las direcciones IPV4 públicas a usar las direcciones privadas de Azure Virtual Network para el tráfico que fluye hacia Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>¿Azure Cosmos DB reside en la instancia de Azure Virtual Network proporcionada por el cliente?  

Azure Cosmos DB es un servicio multiinquilino con direcciones IP públicas. Al restringir el acceso a una subred de una instancia de Azure Virtual Network con el uso de la característica del punto de conexión de servicio, se restringe el acceso para la cuenta de Azure Cosmos DB a través de una instancia determinada de Azure Virtual Network y su subred.  La cuenta de Azure Cosmos DB no reside en dicha instancia de Azure Virtual Network. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>¿Qué ocurre si se registra algo en Log Analytics/OMS si está habilitado?  

Azure Cosmos DB insertará los registros con la dirección IP (sin el último octeto) con el estado 403 para la solicitud bloqueada por la ACL.  

## <a name="next-steps"></a>Pasos siguientes
Para configurar un firewall para Azure Cosmos DB, vea el artículo sobre la [compatibilidad con un firewall](firewall-support.md).

