---
title: "Tipos de nodos y conjuntos de escalado de máquinas virtuales de Azure Service Fabric | Microsoft Docs"
description: "Aprenda a relacionar los tipos de nodos de Azure Service Fabric con los conjuntos de escalado de máquinas virtuales y a conectarse de forma remota a una instancia de conjunto de escalado o un nodo de clúster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nodos y conjuntos de escalado de máquinas virtuales de Azure Service Fabric
Los conjuntos de escalado de máquinas virtuales son un recurso de Azure Compute. Puede usarlos para implementar y administrar una colección de máquinas virtuales como conjunto. Configure un conjunto de escalado independiente para cada tipo de nodo que defina en un clúster de Azure Service Fabric. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente, tiene diferentes conjuntos de puertos abiertos y puede usar distintas métricas de capacidad.

En la siguiente ilustración, se muestra un clúster que tiene dos tipos de nodos llamados front-end y back-end. A su vez, cada tipo de nodo tiene cinco nodos.

![Un clúster que tiene dos tipos de nodos][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Asignación de instancias de conjuntos de escalado de máquinas virtuales a los nodos
Como se muestra en la ilustración anterior, las instancias del conjunto de escalado empiezan por la instancia 0 y, a continuación, se van incrementando de uno en uno. La numeración se refleja en los nombres de los nodos. Por ejemplo, el nodo BackEnd_0 es la instancia 0 del conjunto de escalado de BackEnd. En concreto, este conjunto de escalado tiene cinco instancias, denominadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 y BackEnd_4.

Al escalar verticalmente un conjunto de escalado, se crea una nueva instancia. El nuevo nombre de la instancia de conjunto de escalado será normalmente el nombre del conjunto de escalado seguido del número de instancia. En nuestro ejemplo, es BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Asignación de los equilibradores de carga del conjunto de escalado a los tipos de nodos y conjuntos de escalado
Si ha implementado el clúster en Azure Portal o ha usado la plantilla de Azure Resource Manager de ejemplo, obtendrá una lista de todos los recursos en un grupo de recursos. Puede ver los equilibradores de carga de cada conjunto de escalado o tipo de nodo. El nombre del equilibrador de carga usa el formato siguiente: **LB-&lt;nombre del tipo de nodo&gt;**. Por ejemplo: LB-sfcluster4doc-0, tal y como se muestra en la ilustración siguiente:

![Recursos][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conexión remota a una instancia de conjunto de escalado de máquinas virtuales o a un nodo de clúster
Configure un conjunto de escalado independiente para cada tipo de nodo que defina en un clúster. Puede escalar o reducir verticalmente los tipos de nodo de forma independiente. También puede utilizar diferentes SKU de máquina virtual. A diferencia de las máquinas virtuales de instancia única, las instancias de conjuntos de escalado no tienen direcciones IP virtuales. Esto puede resultar un poco complicado si desea obtener una dirección IP y un puerto que pueda usar para conectarse de manera remota a una instancia específica.

Para buscar una dirección IP y un puerto que pueda usar para conectarse remotamente a una instancia concreta, realice los pasos siguientes.

**Paso 1**: busque la dirección IP virtual del tipo de nodo obteniendo las reglas NAT de entrada para el Protocolo de escritorio remoto (RDP).

Primero, obtenga los valores de las reglas NAT de entrada que se definieron como parte de la definición del recurso para `Microsoft.Network/loadBalancers`.

En Azure Portal, en la página del equilibrador de carga, seleccione **Configuración** > **Reglas NAT de entrada**. Con ello obtendrá la dirección IP y el puerto que puede usar para conectarse de forma remota a la primera instancia del conjunto de escalado. 

![Equilibrador de carga][LBBlade]

En la siguiente ilustración, la dirección IP y el puerto son **104.42.106.156** y **3389**.

![Reglas NAT][NATRules]

**Paso 2**: busque el puerto que puede usar para conectarse de forma remota al nodo o a la instancia específica del conjunto de escalado.

Asignación de instancias de conjuntos de escalado a los nodos. Use la información del conjunto de escalado para determinar el puerto exacto que se usará.

Los puertos se asignan en un orden ascendente que coincide con el de la instancia del conjunto de escalado. En el ejemplo anterior del tipo de nodo FrontEnd, la siguiente tabla muestra los puertos para cada una de las cinco instancias de nodo. Aplique la misma asignación a la instancia del conjunto de escalado.

| **Instancia del conjunto de escalado de máquinas virtuales** | **Puerto** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**Paso 3**: conéctese de forma remota a la instancia específica del conjunto de escalado.

En la siguiente ilustración se muestra el uso de la Conexión a Escritorio remoto para conectarse a la instancia FrontEnd_1 del conjunto de escalado:

![Conexión del Escritorio remoto][RDP]

## <a name="change-the-rdp-port-range-values"></a>Cambio de los valores del intervalo de puertos RDP

### <a name="before-cluster-deployment"></a>Antes de la implementación del clúster
Si configura el clúster mediante una plantilla de Resource Manager, especifique el intervalo en `inboundNatPools`.

Vaya a la definición del recurso de `Microsoft.Network/loadBalancers`. Busque la descripción de `inboundNatPools`.  Reemplace los valores `frontendPortRangeStart` y `frontendPortRangeEnd`.

![valores inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Después de la implementación del clúster
Cambiar los valores de intervalo de puertos RDP una vez implementado el clúster es más complejo. Para asegurarse de que no recicla las máquinas virtuales, use Azure PowerShell para establecer los nuevos valores. 

> [!NOTE]
> Asegúrese de que tiene Azure PowerShell 1.0 o una versión posterior instalada en el equipo. Si no tiene Azure Powershell 1.0 o una versión posterior, es muy recomendable que siga los pasos descritos en el artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).

1. Inicie sesión en la cuenta de Azure. Si se produce un error en el siguiente comando de PowerShell, compruebe que PowerShell se instaló correctamente.

    ```
    Login-AzureRmAccount
    ```

2. Ejecute el siguiente código para obtener detalles sobre el equilibrador de carga y ver los valores para la descripción de `inboundNatPools`:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Establezca `frontendPortRangeEnd` y `frontendPortRangeStart` en los valores que desee.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Cambio del nombre de usuario y la contraseña de RDP para los nodos

Para cambiar la contraseña para todos los nodos de un determinado tipo de nodo, realice los siguientes pasos. Estos cambios se aplicarán a todos los nodos actuales y futuros del conjunto de escalado.

1. Abra PowerShell como administrador. 
2. Ejecute los comandos siguientes para iniciar sesión y seleccionar la suscripción para la sesión. Cambie el parámetro `SUBSCRIPTIONID` para el identificador de la suscripción. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Ejecute el siguiente script. Use los valores `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME`, y `PASSWORD` pertinentes. Los valores `USERNAME` y `PASSWORD` son las nuevas credenciales que utilizará en futuras sesiones del Protocolo de escritorio remoto. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Descripción general de la característica "Deploy anywhere" y comparación con los clústeres administrados de Azure](service-fabric-deploy-anywhere.md).
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* Obtenga más información en [SDK de Service Fabric e introducción](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
