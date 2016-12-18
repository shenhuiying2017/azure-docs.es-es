---
title: "Creación de un clúster independiente con VM de Azure con Windows | Microsoft Docs"
description: "Obtenga información sobre cómo crear y administrar un clúster de Azure Service Fabric en máquinas virtuales de Azure con Windows Server."
services: service-fabric
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/05/2016
ms.author: dkshir;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b6bd503377e033f4e694cc344ec121d99c0d6187


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Creación de un clúster independiente de tres nodos de Service Fabric con máquinas virtuales de Azure con Windows Server)
En este artículo se describe cómo crear un clúster en máquinas virtuales (VM) de Azure basadas en Windows con el instalador independiente de Service Fabric para Windows Server. Se trata de un caso especial de [Creación y administración de un clúster que se ejecute en Windows Server](service-fabric-cluster-creation-for-windows-server.md), donde las VM son [VM de Azure que ejecutan Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), pero no creará [un clúster de Service Fabric basado en la nube de Azure](service-fabric-cluster-creation-via-portal.md). La diferencia es que usted administra completamente el clúster independiente de Service Fabric que se crea con los pasos siguientes, mientras que es el proveedor de recursos de Service Fabric quien administra y actualiza los clústeres de Service Fabric basados en la nube de Azure.

## <a name="steps-to-create-the-standalone-cluster"></a>Pasos para crear el clúster independiente
1. Inicie sesión en el Portal de Azure y cree una VM de Windows Server 2012 R2 Datacenter en un grupo de recursos. Para más información, vea el artículo [Crear una VM de Windows en Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Agregue un par de VM Windows Server 2012 R2 Datacenter más al mismo grupo de recursos. Asegúrese de que cada una ellas tenga el mismo nombre de usuario administrador y contraseña que cuando se creó. Una vez creada, verá las tres máquinas virtuales en la misma red virtual.
3. Conéctese a cada una de ellas y desactive el Firewall de Windows mediante el [Administrador del servidor, panel Servidor local](https://technet.microsoft.com/library/jj134147.aspx). Esto garantiza que el tráfico de red pueda comunicarse entre las máquinas. Mientras esté conectado a cada máquina, abra un símbolo del sistema y escriba `ipconfig`para obtener la dirección IP. También puede ver la dirección IP de cada máquina si selecciona el recurso de red virtual para el grupo de recursos en el Portal de Azure.
4. Conéctese a una de las VM y pruebe si puede hacer ping correctamente a las otras dos.
5. Conéctese a una de las VM, [descargue el paquete de Service Fabric independiente para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) a una carpeta nueva en la máquina y extráigalo.
6. Abra el archivo *ClusterConfig.Unsecure.MultiMachine.json* en el Bloc de notas y edite cada nodo con las tres direcciones IP de las máquinas. Cambie el nombre del clúster en la parte superior y guarde el archivo.  A continuación, se puede ver un ejemplo parcial del manifiesto de clúster.
   
    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```
7. Abra una [ventana de PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Vaya a la carpeta donde extrajo el paquete del instalador independiente que descargó y donde guardó el archivo del manifiesto de clúster. Ejecute el siguiente comando de PowerShell.
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```
8. Debería ver que se ejecuta el comando de PowerShell, se conecta a cada máquina y crea un clúster. Después de aproximadamente un minuto, puede comprobar si el clúster está operativo. Para ello, conéctese a Service Fabric Explorer en la dirección IP de una de las máquinas, como `http://10.7.0.5:19080/Explorer/index.html`. Como se trata de un clúster independiente que usa VM de Azure, para protegerlo necesitará [implementar certificados en las VM de Azure](service-fabric-windows-cluster-x509-security.md) o configurar una de las máquinas como un [controlador de Windows Server Active Directory (AD) para la autenticación de Windows](service-fabric-windows-cluster-windows-security.md), igual que lo haría en un entorno local.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de clústeres independientes de Service Fabric en Windows Server o Linux](service-fabric-deploy-anywhere.md)
* [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)
* [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
* [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)




<!--HONumber=Nov16_HO3-->


