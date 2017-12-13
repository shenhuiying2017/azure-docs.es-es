---
title: "Escalabilidad de un clúster de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información sobre cómo escalar rápidamente un clúster de Service Fabric."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2017
ms.author: adegeo
ms.openlocfilehash: baed3e290ed29d1459455441e7cac7c5b1b1ebd9
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="scale-a-service-fabric-cluster"></a>Escalabilidad de un clúster de Service Fabric

Este tutorial es la segunda parte de una serie y en él se describe cómo reducir y escalar horizontalmente un clúster existente. Cuando haya terminado, habrá aprendido cómo escalar el clúster y cómo limpiar los recursos restantes.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Leer el recuento de nodos de clúster
> * Agregar nodos de clúster (escalabilidad horizontal)
> * Quitar nodos de clúster (reducción horizontal)

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Crear un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure mediante una plantilla
> * Escalar o reducir un clúster horizontalmente
> * [Actualización del sistema de tiempo de ejecución de un clúster](service-fabric-tutorial-upgrade-cluster.md)
> * [Implementar API Management con Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale la [versión 4.1 o superior del módulo de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) o la [CLI de Azure 2.0](/cli/azure/install-azure-cli).
- Creación de un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure
- Si implementa un clúster de Windows, configure un entorno de desarrollo de Windows. Instale [Visual Studio 2017](http://www.visualstudio.com) y las cargas de trabajo de **desarrollo Azure**, **desarrollo web y ASP.NET**, y **desarrollo a través de plataformas .NET Core**.  Después, configure un [entorno de desarrollo .NET](service-fabric-get-started.md).
- Si implementa un clúster Linux, configure un entorno de desarrollo Java en [Linux](service-fabric-get-started-linux.md) o [MacOS](service-fabric-get-started-mac.md).  Instale la [CLI de Service Fabric](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Conexión al clúster

Para completar correctamente esta parte del tutorial, debe conectarse al clúster de Service Fabric y al conjunto de escalado de máquinas virtuales (que hospeda el clúster). El conjunto de escalado de máquinas virtuales es el recurso de Azure que hospeda Service Fabric en Azure.

Cuando se conecta a un clúster, puede consultarlo para obtener información. Puede utilizar el clúster para obtener información sobre qué nodos reconoce el clúster. La conexión al clúster en el código siguiente usa el mismo certificado creado en la primera parte de esta serie. Asegúrese de establecer las variables `$endpoint` y `$thumbprint` con sus valores.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Ahora que está conectado, puede usar un comando para obtener el estado de cada nodo del clúster. Para PowerShell, use el comando `Get-ServiceFabricClusterHealth` y, para **sfctl**, use el comando ``.

## <a name="scale-out"></a>Escalado horizontal

Al escalar horizontalmente, agrega más instancias de máquina virtual al conjunto de escalado. Estas instancias se convierten en los nodos que usa Service Fabric. Service Fabric sabe cuándo el conjunto de escalado tiene varias instancias agregadas (mediante la escalabilidad horizontal) y reacciona automáticamente. El código siguiente obtiene un conjunto de escalado por el nombre y aumenta la **capacidad** de dicho conjunto en 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Este código establece la capacidad en 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Reducción horizontal

La reducción horizontal funciona de la misma forma que la escalabilidad horizontal, salvo que se usa un valor de **capacidad** inferior. Al reducir el conjunto de escalado horizontalmente, se quitan instancias de máquina virtual de dicho conjunto. Normalmente, Service Fabric no es consciente de lo que ha ocurrido y considera que un nodo ha desaparecido. Service Fabric informa entonces de un estado incorrecto del clúster. Para evitar ese estado incorrecto, debe informar a Service Fabric que espera que el nodo desaparezca.

### <a name="remove-the-service-fabric-node"></a>Eliminación del nodo de Service Fabric

> [!NOTE]
> Esta parte solo se aplica al nivel de durabilidad *Bronce*. Para obtener más información sobre la durabilidad, consulte [planeamiento de capacidad del clúster de Service Fabric][durability].

Al reducir horizontalmente un conjunto de escalado de máquinas virtuales, dicho conjunto, en la mayoría de los casos, elimina la última instancia de máquina virtual que se creó. Por tanto, debe buscar el último nodo creado, el nodo de coincidencia y el nodo de Service Fabric. Puede encontrar el último nodo si consulta la propiedad `NodeInstanceId` con el valor más alto en los nodos de Service Fabric. Los ejemplos de código siguientes se ordenan por la instancia del nodo y devuelven los detalles sobre la instancia con el valor de identificador más alto. 

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```azurecli
`sfctl node list --query "sort_by(items[*], &instanceId)[-1]"`
```

El clúster de Service Fabric debe saber que este nodo se va a quitar. Debe realizar tres pasos:

1. Deshabilite el nodo para que ya no se realice ninguna réplica de los datos.  
PowerShell: `Disable-ServiceFabricNode`  
sfcli: `sfctl node disable`

2. Detenga el nodo para que el tiempo de ejecución de Service Fabric se cierre sin problemas y para que la aplicación obtenga una solicitud de finalización.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfcli: `sfctl node transition --node-transition-type Stop`

2. Quite el nodo del clúster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfcli: `sfctl node remove-state`

Una vez que estos tres pasos se han aplicado al nodo, este último se puede quitar del conjunto de escalado. Si utiliza cualquier nivel de durabilidad además de [bronce][durability], estos pasos se realizan para cuando se quita la instancia del conjunto de escalado.

El bloque de código siguiente obtiene el último nodo creado, deshabilita, detiene y quita el nodo del clúster.

```powershell
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300
    
    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }
    
    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

En el código de **sfctl** siguiente, el comando siguiente se usa para obtener los valores de **node-name** y **node-instance-id** del último nodo creado: `sfctl node list --query "sort_by(items[*], &instanceId)[-1].[instanceId,name]"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Use las siguientes consultas de **sfctl** para comprobar el estado de cada paso:
>
> **Comprobar el estado de desactivación**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].nodeDeactivationInfo"`
>
> **Comprobar el estado de detención**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>Reducción horizontal del conjunto de escalado

Ahora que ya se ha quitado el nodo de Service Fabric del clúster, se puede reducir horizontalmente el conjunto de escalado de máquinas virtuales. En el ejemplo siguiente, la capacidad del conjunto de escalado se reduce en 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Este código establece la capacidad en 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Leer el recuento de nodos de clúster
> * Agregar nodos de clúster (escalabilidad horizontal)
> * Quitar nodos de clúster (reducción horizontal)


Luego, pase al siguiente tutorial para aprender a actualizar el sistema de tiempo de ejecución de un clúster.
> [!div class="nextstepaction"]
> [Actualización del sistema de tiempo de ejecución de un clúster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
