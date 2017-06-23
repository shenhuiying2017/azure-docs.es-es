---
title: "Creación de un clúster independiente con VM de Azure con Windows | Microsoft Docs"
description: "Obtenga información sobre cómo crear y administrar un clúster de Azure Service Fabric en máquinas virtuales de Azure con Windows Server."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: ryanwi;chackdan
redirect_url: /azure/service-fabric/service-fabric-cluster-creation-via-arm
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.contentlocale: es-es
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Creación de un clúster independiente de tres nodos de Service Fabric con máquinas virtuales de Azure con Windows Server)
En este artículo se describe cómo crear un clúster en máquinas virtuales de Azure basadas en Windows con el instalador independiente de Service Fabric para Windows Server. Este escenario es un caso especial de [Creación y administración de un clúster que se ejecute en Windows Server](service-fabric-cluster-creation-for-windows-server.md), donde las máquinas virtuales son [máquinas virtuales de Azure que ejecutan Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), pero no creará [un clúster de Service Fabric basado en la nube de Azure](service-fabric-cluster-creation-via-portal.md). La distinción al seguir este patrón es que usted administra completamente el clúster independiente de Service Fabric que se crea con los pasos siguientes, mientras que es el proveedor de recursos de Service Fabric quien administra y actualiza los clústeres de Service Fabric basados en la nube de Azure.

## <a name="steps-to-create-the-standalone-cluster"></a>Pasos para crear el clúster independiente
1. Inicie sesión en Azure Portal y cree una máquina virtual de Windows Server 2012 R2 Datacenter o Windows Server 2016 Datacenter en un grupo de recursos. Para más información, vea el artículo [Crear una VM de Windows en Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Agregue un par de máquinas virtuales más al mismo grupo de recursos. Asegúrese de que cada una ellas tenga el mismo nombre de usuario administrador y contraseña que cuando se creó. Una vez creada, verá las tres máquinas virtuales en la misma red virtual.
3. Conéctese a cada una de ellas y desactive el Firewall de Windows mediante el [Administrador del servidor, panel Servidor local](https://technet.microsoft.com/library/jj134147.aspx). Esto garantiza que el tráfico de red pueda comunicarse entre las máquinas. Mientras esté conectado a cada máquina, abra un símbolo del sistema y escriba `ipconfig`para obtener la dirección IP. También puede ver la dirección IP de cada equipo del portal seleccionando el recurso de red virtual del grupo de recursos y comprobando las interfaces de red creadas para cada una de estas máquinas.
4. Conéctese a una de las VM y pruebe si puede hacer ping correctamente a las otras dos.
5. Conéctese a una de las VM, [descargue el paquete de Service Fabric independiente para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) a una carpeta nueva en la máquina y extráigalo.
6. Abra el archivo *ClusterConfig.Unsecure.MultiMachine.json* en el Bloc de notas y edite cada nodo con las tres direcciones IP de las máquinas. Cambie el nombre del clúster en la parte superior y guarde el archivo.  A continuación, se puede ver un ejemplo parcial del manifiesto de clúster.
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. Si piensa que va a ser un clúster seguro, decida la medida de seguridad que le gustaría usar y siga los pasos descritos en el vínculo asociado: [Certificado X509](service-fabric-windows-cluster-x509-security.md) o [Seguridad de Windows](service-fabric-windows-cluster-windows-security.md). Si desea configurar el clúster con la seguridad de Windows, debe configurar un controlador de dominio para administrar Active Directory. Tenga en cuenta no se admite el uso de una máquina de controlador de dominio como un nodo de Service Fabric.
8. Abra una [ventana de PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Vaya a la carpeta donde extrajo el paquete del instalador independiente que descargó y donde guardó el archivo de configuración de clúster. Ejecute el comando de PowerShell siguiente para implementar el clúster:
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

El script va a configurar de manera remota el clúster de Service Fabric y debe notificar el progreso a medida que se desarrolla la implementación.

9. Después de aproximadamente un minuto, puede comprobar si el clúster está operativo. Para ello, conéctese a Service Fabric Explorer con una de las direcciones IP de la máquina, como `http://10.1.0.5:19080/Explorer/index.html`. 

## <a name="next-steps"></a>Pasos siguientes
* [Creación de clústeres independientes de Service Fabric en Windows Server o Linux](service-fabric-deploy-anywhere.md)
* [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)
* [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
* [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)


