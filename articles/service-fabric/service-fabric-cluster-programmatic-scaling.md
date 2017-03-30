---
title: "Escalado mediante programación de Azure Service Fabric | Microsoft Docs"
description: "Escalar o reducir horizontalmente mediante programación un clúster de Azure Service Fabric, de acuerdo a desencadenadores personalizados"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: mikerou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 8d7052fabeb348b4bba744b43d9af78f058175a8
ms.lasthandoff: 03/17/2017


---

# <a name="scale-a-service-fabric-cluster-programmatically"></a>Escalado mediante programación de un clúster de Service Fabric 

Los aspectos básicos del escalado de un clúster de Service Fabric en Azure se tratan en la documentación sobre [escalado de clústeres](./service-fabric-cluster-scale-up-down.md). Ese artículo trata sobre como los clústeres de Service Fabric se basan en conjuntos de escalado de máquinas virtuales y se pueden escalar de forma manual o mediante reglas de escalado automático. En este documento se examinan los métodos programáticos de coordinación de operaciones de escalado de Azure para escenarios más avanzados. 

## <a name="reasons-for-programmatic-scaling"></a>Motivos para el escalado mediante programación
En muchos escenarios, el escalado de forma manual o mediante reglas de escalado automático son buenas soluciones. Sin embargo, en otros escenarios, pueden no ser la mejor opción. Entre las desventajas potenciales de estos métodos se incluyen:

- El escalado manual requiere que inicie sesión y solicite de forma explícita las operaciones de escalado. Si las operaciones de escalado se requieren con frecuencia o en momentos imprevisibles, este enfoque puede no ser una buena solución.
- Cuando las reglas de escalado automático quitan una instancia de un conjunto de escalado de máquinas virtuales, no quitan automáticamente el conocimiento de ese nodo desde el clúster de Service Fabric asociado, a menos que el tipo de nodo tenga un nivel de durabilidad Silver o Gold. Dado que las reglas de escalado automático funcionan a nivel de conjunto de escalado (en lugar de al nivel de Service Fabric), las reglas de escalado automático pueden quitar nodos de Service Fabric sin cerrarlos correctamente. Esta eliminación forzada de un nodo dejará un estado de nodo de Service Fabric "fantasma" después de operaciones de escalado. Es necesario que una persona (o un servicio) limpie periódicamente el estado de los nodos eliminados en el clúster de Service Fabric.
  - Tenga en cuenta que un tipo de nodo con un nivel de durabilidad Gold o Silver limpiará automáticamente los nodos eliminados.  
- Aunque hay [muchas métricas](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) compatibles con las reglas de escalado automático, se trata aún de un conjunto limitado. Si su escenario requiere un escalado automático basado en alguna métrica que no se trata en ese conjunto, es posible que las reglas de escalado automático no sean una buena opción.

En función de estas limitaciones, puede que desee implementar más modelos de escalado automático personalizados. 

## <a name="scaling-apis"></a>API de escalado
Hay algunas API de Azure que permiten que las aplicaciones trabajen de forma programática con conjuntos de escalado de máquinas virtuales y clústeres de Service Fabric. Si las opciones existentes de escalado automático no funcionan para su escenario, estas API permiten implementar una lógica de escalado personalizada. 

Un enfoque para implementar esta funcionalidad de escalado automático "casero" consiste en agregar un nuevo servicio sin estado a la aplicación de Service Fabric para administrar las operaciones de escalado. Dentro del método `RunAsync` del servicio, un conjunto de desencadenadores puede determinar si es necesario el escalado (incluyendo la comprobación de parámetros como el tamaño máximo del clúster y las recuperaciones de escalado).   

La API que se utiliza para las interacciones de conjunto de escalado de máquinas virtuales (tanto para comprobar el número actual de instancias de máquina virtual como para modificarlo) es la fluida [Azure Compute Management Library](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/1.0.0-beta50). Esta biblioteca de proceso fluida proporciona una API fácil de usar para interactuar con los conjuntos de escalado de máquinas virtuales.

Para interactuar con el clúster de Service Fabric propiamente dicho, utilice [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Por supuesto, el código de escalado no tiene que ejecutarse como un servicio en el clúster que se va a escalar. Ambos `IAzure` y `FabricClient` pueden conectarse a sus recursos asociados de Azure de forma remota, por lo que el servicio de escalado podría ser fácilmente una aplicación de consola o el servicio de Windows que se ejecuta desde fuera de la aplicación de Service Fabric. 

## <a name="credential-management"></a>Administración de credenciales
Una dificultad que se presenta al escribir un servicio para controlar el escalado es que el servicio tiene que tener acceso a recursos de conjunto de escalado de máquinas virtuales sin un inicio de sesión interactivo. El acceso al clúster de Service Fabric es fácil si el servicio de escalado está modificando su propia aplicación de Service Fabric, pero se requieren credenciales para tener acceso al conjunto de escalado. Para iniciar sesión, puede usar un [entidad de servicio](https://github.com/Azure/azure-sdk-for-net/blob/Fluent/AUTH.md#creating-a-service-principal-in-azure) creada con la [CLI de Azure 2.0](https://github.com/azure/azure-cli).

Una entidad de servicio se puede crear con los pasos siguientes:

1. Inicie sesión en la CLI de Azure (`az login`) como un usuario con acceso al conjunto de escalado de máquinas virtuales
2. Cree la entidad de servicio con `az ad sp create-for-rbac`
    1. Anote el appId (denominado "ID de cliente" en otros lugares), el nombre, la contraseña y el inquilino para su uso posterior.
    2. También necesitará el identificador de suscripción, que se puede ver con `az account list`

La biblioteca de proceso fluida puede iniciar sesión con estas credenciales como sigue:

```C#
var credentials = AzureCredentials.FromServicePrincipal(AzureClientId, AzureClientKey, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Una vez iniciada la sesión, el recuento de instancias de conjunto de escalado se puede consultar a través de `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Escalado horizontal
Con SDK de proceso fluido de Azure, se pueden agregar instancias al conjunto de escalado de máquinas virtuales con unas pocas llamadas.

```C#
var scaleSet = AzureClient?.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = Math.Min(MaximumNodeCount, NodeCount.Value + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

**Actualmente hay [un error](https://github.com/Azure/azure-sdk-for-net/issues/2716) que impide que este código funcione**, pero se ha conseguido una corrección, por lo que el problema se solucionará pronto en las versiones publicadas de Microsoft.Azure.Management.Compute.Fluent. El error es que al cambiar las propiedades (como la capacidad) del conjunto de escalado de máquinas virtuales con la API de proceso fluida, se pierden los ajustes protegidos de la plantilla de Resource Manager del conjunto de escalado. Estos ajustes que se pierden provocan (entre otras cosas) que los servicios de Service Fabric no se configuren correctamente en nuevas instancias de máquina virtual.

Como solución temporal, se puede invocar los cmdlets de PowerShell desde el servicio de escalado para establecer el mismo cambio (aunque esta ruta significa que las herramientas de PowerShell tienen que estar presentes):

```C#
using (var psInstance = PowerShell.Create())
{
    psInstance.AddScript($@"
        $clientId = ""{AzureClientId}""
        $clientKey = ConvertTo-SecureString -String ""{AzureClientKey}"" -AsPlainText -Force
        $Credential = New-Object -TypeName ""System.Management.Automation.PSCredential"" -ArgumentList $clientId, $clientKey
        Login-AzureRmAccount -Credential $Credential -ServicePrincipal -TenantId {AzureTenantId}
        
        $vmss = Get-AzureRmVmss -ResourceGroupName {ResourceGroup} -VMScaleSetName {NodeTypeToScale}
        $vmss.sku.capacity = {newCapacity}
        Update-AzureRmVmss -ResourceGroupName {ResourceGroup} -Name {NodeTypeToScale} -VirtualMachineScaleSet $vmss
    ");

    psInstance.Invoke();

    if (psInstance.HadErrors)
    {
        foreach (var error in psInstance.Streams.Error)
        {
            ServiceEventSource.Current.ServiceMessage(Context, $"ERROR adding node: {error.ToString()}");
        }
    }                
}
```

Tal y como cuando se agrega manualmente un nodo, agregar una instancia de conjunto de escalado debe ser todo lo que se necesita para iniciar un nuevo nodo de Service Fabric, ya que la plantilla de conjunto de escalado incluye extensiones para unir automáticamente nuevas instancias al clúster de Service Fabric. 

## <a name="scaling-in"></a>Reducir horizontalmente

Reducir horizontalmente es similar a escalar horizontalmente. Los cambios reales del conjunto de escalado de máquinas virtuales son prácticamente los mismos. Pero, tal y como se ha descrito anteriormente, Service Fabric solo limpia automáticamente los nodos eliminados con una durabilidad Gold o Silver. Por lo tanto, en el caso de reducción horizontal de durabilidad Bronze, es necesario interactuar con el clúster de Service Fabric para apagar el nodo que desea eliminar y a continuación eliminar su estado.

Preparar el nodo para el apagado implica buscar el nodo que se va a eliminar (el nodo agregado más recientemente) y desactivarlo. Para nodos que no sean de raíz, se pueden encontrar nodos más recientes mediante la comparación de `NodeInstanceId`. 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

Tenga en cuenta que lo nodos *raíz* no parece que sigan siempre la convención de que se quiten primero los identificadores de instancia mayores.

Cuando se encuentre el nodo que se va a quitar, puede desactivarse y eliminarse utilizando la misma instancia `FabricClient` y la `IAzure` que se usó anteriormente.

```C#
var scaleSet = AzureClient?.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = Math.Max(MinimumNodeCount, NodeCount.Value - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Una vez que se ha eliminado la instancia de máquina virtual, se puede quitar el estado del nodo de Service Fabric.

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

Como anteriormente, tiene que buscar una solución alternativa al no funcionamiento de `IVirtualMachineScaleSet.Update()` hasta que se solucione el problema [Azure/azure-sdk-de-net #2716](https://github.com/Azure/azure-sdk-for-net/issues/2716).

## <a name="potential-drawbacks"></a>Desventajas potenciales

Como se muestra en los fragmentos de código anterior, crear su propio servicio de escalado proporciona el mayor grado de control y personalización sobre el comportamiento de escalado de su aplicación. Esto puede ser útil para escenarios que requieren un control preciso sobre cuándo y cómo una aplicación se escala o reduce horizontalmente. Sin embargo, este control acarrea el inconveniente de la complejidad del código. Para usar este enfoque tiene que tener en propiedad código de escalado, lo que no es fácil.

La forma en la que debe enfocar el escalado de Service Fabric depende de su escenario. Si el escalado no es común, la capacidad de agregar o quitar nodos de forma manual es probablemente suficiente. Para escenarios más complejos, las reglas de escalado automático y los SDK con la capacidad de escalar mediante programación ofrecen unas eficaces alternativas.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a implementar su propia lógica de escalado automático, familiarícese con los siguientes conceptos y API útiles:

- [Escalado manual o con reglas de escalado automático](./service-fabric-cluster-scale-up-down.md)
- [Bibliotecas fluidas de administración de Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (resulta útil para interactuar con los conjuntos de escalado de máquinas virtuales subyacentes de un clúster de Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (resulta útil para interactuar con un clúster de Service Fabric y sus nodos)
