---
title: "Script de PowerShell para implementar el clúster de HPC Pack de Linux | Microsoft Docs"
description: "Ejecución de un script de PowerShell para implementar un clúster de HPC Pack de Linux en máquinas virtuales de Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f2b4081a0a5b158714cb016fd46570a19dae5d00


---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Creación de un clúster de informática de alto rendimiento (HPC) en máquinas virtuales Linux con el script de implementación de HPC Pack IaaS
Ejecute el script de PowerShell de implementación de HPC Pack IaaS para implementar un clúster de HPC completo para cargas de trabajo Linux en máquinas virtuales de Azure. El clúster consta de un nodo principal unido a Active Directory con Windows Server y Microsoft HPC Pack, y nodos de proceso que ejecutan una de las distribuciones de Linux compatibles con HPC Pack. Si desea implementar un clúster de HPC Pack en cargas de trabajo de Azure para Windows, consulte [Creación de un clúster de proceso de alto rendimiento (HPC) de Windows con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). También puede usar una plantilla del Administrador de recursos de Azure para implementar un clúster de HPC Pack. Si desea consultar un ejemplo, consulte [Create an HPC cluster with Linux compute nodes](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)(Creación de un clúster de HPC con nodos de proceso de Linux).

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Archivos de configuración de ejemplo
El archivo de configuración siguiente crea un nuevo controlador de dominio y un nuevo bosque de dominio e implementa un clúster de HPC Pack que tiene un nodo principal con bases de datos locales y 10 nodos de proceso de Linux. Todos los servicios en la nube se crean directamente en la ubicación de Este de Asia. Los nodos de proceso de Linux se crean en dos servicios en la nube y en dos cuentas de almacenamiento (es decir, de *MyLnxCN-0001* a *MyLnxCN-0005* en *MyLnxCNService01* y *mylnxstorage01*, y de *MyLnxCN-0006* a *MyLnxCN-0010* en *MyLnxCNService02* y *mylnxstorage02*). Los nodos de proceso se crean a partir de una imagen de Linux de CentOS OpenLogic versión 7.0. 

Sustituya sus propios valores por su nombre de suscripción y los nombres de cuenta y servicio.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Solución de problemas
* **Error "La red virtual no existe"**: si ejecuta el script de implementación de HPC Pack IaaS para implementar varios clústeres en Azure simultáneamente con una única suscripción, puede producirse un error de "La red virtual *nombre de\_red virtual* no existe" en una implementación o varias.
  Si se produce este error, vuelva a ejecutar el script para la implementación en la que ocurrió el error.
* **Problemas de acceso a Internet desde la red virtual de Azure** : si crea un clúster de HPC Pack con un nuevo controlador de dominio mediante el script de implementación o promueve manualmente una máquina virtual del nodo principal a un controlador de dominio, puede experimentar problemas al conectar las máquinas virtuales de la red virtual de Azure a Internet. Esto puede ocurrir si se configura automáticamente un servidor de reenviador DNS en el controlador de dominio y este servidor de reenviador DNS no se resuelve correctamente.
  
    Para evitar este problema, inicie sesión en el controlador de dominio y, o bien, quite la configuración de reenviador, o bien, configure un servidor de reenviador DNS válida. Para ello, en Administrador de servidores, haga clic **Herramientas** >
    **DNS** para abrir el Administrador de DNS y, a continuación, haga doble clic en **Reenviadores**.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para más información sobre las distribuciones de Linux compatibles, el movimiento de datos y el envío de trabajos a un clúster de HPC Pack con nodos de proceso de Linux.
* Para ver tutoriales que usan el script para crear un clúster y ejecutar una carga de trabajo HPC de Linux, consulte:
  * [Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
  * [Ejecución de OpenFOAM con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
  * [Ejecución de STAR-CCM+ con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)




<!--HONumber=Dec16_HO1-->


