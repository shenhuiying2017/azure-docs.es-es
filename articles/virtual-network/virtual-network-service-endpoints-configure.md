---
title: "Configuración de puntos de conexión del servicio Azure Virtual Network | Microsoft Docs"
description: "Aprenda a habilitar y deshabilitar puntos de conexión de servicio desde la red virtual"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: c9c23462f80533a224c3c2ac3658b9630f1798f9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="configure-virtual-network-service-endpoints"></a>Configuración de los puntos de conexión de servicio de Virtual Network

Los puntos de conexión de servicio de red virtuales le permiten proteger los recursos de servicio de Azure para Azure Virtual Network, quitando totalmente el acceso a Internet a estos recursos. Los puntos de conexión de servicio proporcionan una conexión directa desde la red virtual a un servicio de Azure, lo que permite usar el espacio de direcciones privadas de la red virtual para acceder a los servicios de Azure. El tráfico destinado a los servicios de Azure a través de puntos de conexión de servicio siempre se mantiene en la red troncal de Microsoft Azure. Aprenda más sobre los [puntos de conexión de servicio Virtual Network](virtual-network-service-endpoints-overview.md).

En este artículo se proporcionan los pasos necesarios para habilitar y deshabilitar los puntos de conexión de servicio. Cuando los puntos de conexión están habilitados en una subred para un servicio de Azure, puede proteger los recursos de servicio específicos para una red virtual.

Los puntos de conexión de servicio pueden configurarse mediante [Azure Portal](#azure-portal), [Azure PowerShell](#azure-powershell), la [interfaz de la línea de comandos de Azure](#azure-cli) o una [ plantilla](#resource-manager-template) de Azure Resource Manager.

>[!NOTE]
Durante la versión preliminar, se admite la característica de puntos de conexión de servicio de redes virtuales para regiones específicas. Para obtener la lista de regiones admitidas, consulte la página de [actualizaciones de la red virtual de Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Introducción a la configuración de puntos de conexión de servicio

- Los puntos de conexión de servicio solamente pueden configurarse en redes virtuales que se implementan a través del modelo de implementación de Azure Resource Manager.

- Los puntos de conexión de servicio se establecen en cada subred de una red virtual.

- Para una subred, solo se puede configurar un punto de conexión de servicio para un servicio. Puede configurar varios puntos de conexión de servicio para servicios diferentes (por ejemplo, Azure Storage, Azure SQL).

- Puede habilitar los puntos de conexión en una subred nueva o existente.

- La ubicación se configura automáticamente para un punto de conexión. De forma predeterminada, los puntos de conexión de servicio están configurados para la región de la red virtual. Para Azure Storage, para admitir escenarios de conmutación por error regionales, los puntos de conexión se configuran automáticamente para las [regiones emparejadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)

  >[!NOTE]
  Dependiendo del tamaño de la subred o red virtual, habilitar el punto de conexión de servicio puede tardar algún tiempo en finalizar. Asegúrese de que no hay ninguna tarea crítica en curso cuando se habilitan los puntos de conexión de servicio. Los puntos de conexión de servicio cambian las rutas en todas las NIC de la subred y pueden finalizar todas las conexiones TCP abiertas. 

- La llamada de punto de conexión de servicio devuelve "correcto" después de que se hayan cambiado los flujos de tráfico para el servicio en todas las NIC de la subred a direcciones IP privadas de red virtual.

- Rutas eficaces para validar la configuración de puntos de conexión:

   Para validar si el punto de conexión de servicio está configurado correctamente, las "rutas eficaces" en cualquier NIC de la subred muestra una nueva ruta "predeterminada" con nextHopType: VirtualNetworkServiceEndpoint, por servicio y por región. Obtenga más información sobre la [solución de problemas con rutas eficaces](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   Solo se pueden ver rutas eficaces si tiene uno o más interfaces de red (NIC) configuradas y asociadas a una máquina virtual en ejecución de la subred.

## <a name="azure-portal"></a>Azure Portal

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Establecimiento del punto de conexión de servicio en una subred durante la creación de la red virtual

1. Abra [Azure Portal](https://portal.azure.com/).
Inicie sesión con la cuenta de Azure. Si no tiene una cuenta de Azure, puede registrarse para obtener una evaluación gratuita. La cuenta debe tener todos los [permisos](#provisioning) necesarios para crear una red virtual y un punto de conexión de servicio.
2. Haga clic en +Nuevo > Redes > Red virtual > +Agregar.
3. En la hoja "Crear red virtual", escriba los valores siguientes y luego haga clic en Crear:

Configuración | Valor
------- | -----
Nombre    | myVnet
Espacio de direcciones | 10.0.0.0/16
Nombre de subred|mySubnet
Intervalo de direcciones de subred|10.0.0.0/24
Grupos de recursos|Deje seleccionado Crear nuevo y después introduzca un nombre.
Ubicación|Cualquier región admitida, por ejemplo, este de Australia
La suscripción|Seleccione su suscripción.
__ServiceEndpoints__|habilitado
__Servicios__ | Seleccione uno de los servicios disponibles, o todos. En la versión preliminar, servicios admitidos: __"Microsoft.Storage", "Microsoft.Sql"__.

Seleccione los servicios para los puntos de conexión: ![Selección de servicios de punto de conexión de servicio](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Valide que todos los valores son correctos y haga clic en "Crear".

![Establecer puntos de conexión de servicio](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Para finalizar la protección de recursos de servicio de Azure a la red virtual, haga clic en la documentación del servicio en [Pasos siguientes](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Validación de la configuración de puntos de conexión de servicio

Confirme que los puntos de conexión de servicio se configuran mediante los siguientes pasos:

- En Recursos, haga clic en "Redes virtuales". Busque la red virtual.
- Haga clic en el nombre de la red virtual y vaya a "Puntos de conexión de servicio".
- Los puntos de conexión configurados se muestran como "Correcto". También se pueden ver las ubicaciones configuradas automáticamente.

![Confirmación de la configuración de puntos de conexión de servicio](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Rutas eficaces para validar la configuración de puntos de conexión

Para ver la ruta eficaz en una interfaz de red (NIC) en la subred, haga clic en una NIC de esa subred. En "Soporte y solución de problemas", haga clic en "Rutas eficaces". Si se configura el punto de conexión, verá una nueva ruta "predeterminada" con prefijos de direcciones del servicio como destino y nextHopType como "VirtualNetworkServiceEndpoint".

![Rutas eficaces para puntos de conexión de servicio](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Configuración de puntos de conexión de servicio para subredes existentes en una red virtual

1. En los recursos, haga clic en "Redes virtuales" y busque cualquier red virtual existente
2. Haga clic en el nombre de la red virtual y vaya a "Puntos de conexión de servicio".
3. Haga clic en "Agregar". Seleccione "Servicio". Puede crear un punto de conexión para un único servicio a la vez. 
4. Seleccione todas las subredes en la que desea aplicar el punto de conexión. Haga clic en "Agregar".

![Configuración de puntos de conexión de servicio de subred](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Eliminación de puntos de conexión de servicio
1. En Recursos, haga clic en "Redes virtuales". Busque una red virtual existente mediante el filtrado en nombre de red virtual.
2. Haga clic en el nombre de la red virtual y vaya a "Puntos de conexión de servicio".
3. Haga clic en el nombre del servicio y haga clic con el botón derecho en la entrada de punto de conexión de servicio.
4. Seleccione "Eliminar".

![Eliminación del punto de conexión de servicio](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Configuración de los requisitos previos:

- Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de PowerShell. Si no está familiarizado con Azure PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Para iniciar una sesión de PowerShell, vaya a **Inicio**, escriba **powershell** y, luego, haga clic en **PowerShell**.
- En PowerShell, inicie sesión en Azure especificando el comando `login-azurermaccount`. La cuenta debe tener todos los [permisos](#provisioning) necesarios para crear una red virtual y un punto de conexión de servicio.

### <a name="get-available-service-endpoints-for-azure-region"></a>Obtención de los puntos de conexión de servicio disponibles para la región de Azure

Utilice el comando siguiente para obtener la lista de servicios admitidos para los puntos de conexión, para una región de Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Salida: 
Nombre | ID | Tipo
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Adición de un punto de conexión de servicio de Azure Storage a una subred *mySubnet* al crear la red virtual *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Puede habilitar varios servicios mediante el uso de listas separadas por comas de nombres de servicio.
Ejemplo: "Microsoft.Storage", "Microsoft.Sql"

Salida esperada:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Para finalizar la protección de recursos de servicio de Azure a la red virtual, haga clic en la documentación del servicio en [Pasos siguientes](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Adición de varios puntos de conexión de servicio a una red existente

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Salida esperada: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Visualización de los puntos de conexión de servicio configurados en una subred

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Salida:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Eliminación de los puntos de conexión de servicio en una subred
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>CLI de Azure

Configuración de los requisitos previos:
- Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. Para más información, consulte [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)(Introducción a la CLI de Azure 2.0).
 - La cuenta debe tener todos los [permisos](#provisioning) necesarios para crear una red virtual y un punto de conexión de servicio.

 Para obtener una lista completa de comandos para las redes virtuales, consulte los [comandos de red virtual de CLI de Azure](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest).

### <a name="get-available-service-endpoints-for-azure-region"></a>Obtención de los puntos de conexión de servicio disponibles para la región de Azure

Utilice el comando siguiente para obtener la lista de servicios admitidos para los puntos de conexión, para una región de Azure, por ejemplo "EastUS".
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Salida:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Adición de un punto de conexión de servicio de Azure Storage a una subred *mySubnet* al crear la red virtual *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Para agregar varios puntos de conexión: --service-endpoints Microsoft.Storage Microsoft.Sql

Salida:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Para finalizar la protección de recursos de servicio de Azure a la red virtual, haga clic en la documentación del servicio en [Pasos siguientes](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Adición de varios puntos de conexión de servicio a una red existente

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Salida esperada:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Visualización de los puntos de conexión de servicio configurados en una subred

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Eliminación de los puntos de conexión de servicio en una subred
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Salida: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Plantilla de Resource Manager

### <a name="securing-azure-service-resources-to-vnets"></a>Protección de recursos de servicio de Azure para redes virtuales

Puede proteger los recursos específicos de Azure en la red virtual a través de puntos de conexión de servicio.

Descargue la [plantilla de Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) de ejemplo para proteger una cuenta de almacenamiento a una subred de una red virtual.

La plantilla crea una red virtual con dos subredes, una máquina virtual con una NIC en cada una de las subredes. Habilita el punto de conexión en una subred y protege una cuenta de almacenamiento en esa subred.

Puede descargar la plantilla y modificar sus elementos para que se ajuste a su escenario.

Las instrucciones se proporcionan con la plantilla para implementar la plantilla mediante Azure Portal, PowerShell o la CLI de Azure. Asegúrese de que tiene los [permisos](#provisioning) requeridos para establecer el punto de conexión y proteger la cuenta.

Para proteger los recursos de Azure en una subred:

- Un punto de conexión de servicio debe estar configurado en esa subred.
- El recurso debe protegerse en la red virtual mediante la adición de una regla de red virtual en el recurso.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Eliminación de puntos de conexión de servicio con recursos protegidos en la subred
Si se protegen los recursos de servicio de Azure en la subred y se elimina el punto de conexión de servicio, ya no se puede acceder al recurso desde la subred.
Volver a habilitar solo el punto de conexión no restaurará el acceso a los recursos protegidos anteriormente en la subred.

Para volver a proteger el recurso del servicio para esta subred, debe:

- habilitar de nuevo el punto de conexión
- quitar la regla de red virtual anterior en el recurso
- agregar una nueva regla que proteja los recursos en la subred

## <a name="provisioning"></a>Aprovisionamiento

Los puntos de conexión de servicio pueden configurarse en redes virtuales de forma independiente por un usuario con acceso de escritura a la red virtual.

Para proteger los recursos de servicio de Azure a una red virtual, el usuario debe tener permiso "Microsoft.Network/JoinServicetoaSubnet" para las subredes que se van a agregar. De forma predeterminada, este permiso se incluye en los roles de administrador de servicios integrado y puede modificarse mediante la creación de roles personalizados.

Obtenga más información sobre los [roles integrados](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) y la asignación de permisos específicos a [roles personalizados](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Las redes virtuales y los recursos de servicio de Azure pueden encontrarse en la misma o en diferentes suscripciones. Si están en distintas suscripciones, los recursos deben estar en el mismo inquilino de Active Directory (AD), en el momento de esta versión preliminar.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más instrucciones para proteger los recursos de servicio en las redes virtuales, consulte vínculos siguientes:

[Protección de cuentas de Azure Storage para las redes virtuales](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Protección de cuentas de Azure SQL para las redes virtuales](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
