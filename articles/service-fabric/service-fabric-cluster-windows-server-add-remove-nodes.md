---
title: "Incorporación o eliminación de un clúster de Service Fabric independiente | Microsoft Docs"
description: "Obtenga información sobre cómo agregar o quitar nodos en un clúster de Azure Service Fabric en una máquina virtual o física con Windows Server, la que podría estar en el entorno local o en alguna nube."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2016
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 3f7d2861512ba02e3b158db78fbee771da1c788b
ms.openlocfilehash: 0d15e9a68c91c85e6a9250cc31e03e24b32cf7bf


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Incorporación o eliminación de nodos de un clúster de Service Fabric independiente con Windows Server
Una vez que [cree su clúster de Service Fabric independiente en máquinas de Windows Server](service-fabric-cluster-creation-for-windows-server.md), puede que las necesidades empresariales cambien y que deba agregar o quitar varios nodos del clúster. En este artículo, se muestran los pasos detallados para lograr este objetivo.

## <a name="add-nodes-to-your-cluster"></a>Incorporación de nodos al clúster
1. Prepare la VM o la máquina que desea agregar al clúster con los pasos que se indican en la sección [Preparar las máquinas para cumplir los requisitos previos de la implementación del clúster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planee a qué dominio de error y de actualización va a agregar esta máquina o VM.
3. Abra una conexión de Escritorio remoto (RDP) en la máquina o VM que desea agregar al clúster.
4. Copie o [descargue el paquete independiente de Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) en esta máquina o VM y descomprímalo.
5. Ejecute PowerShell como administrador y vaya a la ubicación del paquete no descomprimido.
6. Ejecute el script *AddNode.ps1* de PowerShell con los parámetros que describen el nuevo nodo que se va a agregar. En el ejemplo siguiente se agrega un nuevo nodo denominado VM5, con el tipo NodeType0 y la dirección IP 182.17.34.52, en UD1 y fd:/dc1/r0. *ExistingClusterConnectionEndPoint* es un punto de conexión para un nodo que ya está presente en el clúster existente. Para este punto de conexión, puede elegir la dirección IP de *cualquier* nodo del clúster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA

```
Puede comprobar si el nuevo nodo se agrega al ejecutar el cmdlet [ServiceFabricNode Get](https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode).


## <a name="remove-nodes-from-your-cluster"></a>Eliminación de nodos del clúster
Según el nivel de confiabilidad elegido para el clúster, no podrá quitar los primeros nodos de n (3/5/7/9) del tipo de nodo principal También tenga en cuenta que no se admite la ejecución del comando RemoveNode en un clúster de desarrollo.

1. Abra una conexión de Escritorio remoto (RDP) en la máquina o máquina virtual que desea quitar del clúster.
2. Copie o [descargue el paquete independiente de Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprima el paquete en esta VM o máquina.
3. Ejecute PowerShell como administrador y vaya a la ubicación del paquete no descomprimido.
4. Ejecute *RemoveNode.ps1* en PowerShell. En el ejemplo siguiente se quita el nodo actual del clúster. *ExistingClientConnectionEndpoint* es un punto de conexión de cliente para cualquier nodo que permanecerá en el clúster. Elija la dirección IP y el puerto del punto de conexión de *cualquier* **otro nodo** del clúster. Este **otro nodo** a su vez actualizará la configuración del clúster para el nodo quitado. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

> [!NOTE]
> No se pueden quitar algunos nodos debido a las dependencias de servicios del sistema. Estos nodos son principales y se pueden identificar con una consulta al manifiesto de clúster mediante `Get-ServiceFabricClusterManifest` y la búsqueda de entradas de nodo marcadas con `IsSeedNode=”true”`. 
> 
> 

Incluso después de quitar un nodo, puede que aparezca como si estuviera inactivo en las consultas y SFX, tenga en cuenta que se trata de un defecto conocido. Se corregirá en una próxima versión. 

## <a name="next-steps"></a>Pasos siguientes
* [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)
* [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
* [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)
* [Creación de un clúster de Service Fabric independiente con VM de Azure con Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)




<!--HONumber=Dec16_HO2-->


