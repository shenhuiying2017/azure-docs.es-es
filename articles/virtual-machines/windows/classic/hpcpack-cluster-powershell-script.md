---
title: "Script de PowerShell para implementar un clúster de Windows HPC | Microsoft Docs"
description: "Ejecución de un script de PowerShell para implementar un clúster de HPC Pack 2012 R2 de Windows completo en máquinas virtuales de Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 85b125ab19671b61d2541af6378c95feb88bf952
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Creación de un clúster de proceso de alto rendimiento (HPC) de Windows con el script de implementación de HPC Pack IaaS
Ejecute el script de PowerShell de implementación de HPC Pack IaaS para implementar un clúster de HPC Pack 2012 R2 completo para cargas de trabajo Windows en máquinas virtuales de Azure. El clúster consta de un nodo principal unido a Active Directory en el que se ejecuta Windows Server y Microsoft HPC Pack, y los recursos de cálculo de Windows adicionales que especifique. Si desea implementar un clúster de HPC Pack en Azure para cargas de trabajo de Linux, consulte [Creación de un clúster de informática de alto rendimiento (HPC) en máquinas virtuales de Linux con el script de implementación de HPC Pack IaaS](../../linux/classic/hpcpack-cluster-powershell-script.md). También puede usar una plantilla del Administrador de recursos de Azure para implementar un clúster de HPC Pack. Para ver ejemplos, consulte [Create an HPC cluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) (Creación de un clúster de HPC) y [Create an HPC cluster with a custom compute node image](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) (Creación de un clúster de HPC con una imagen de nodo de proceso personalizado).

> [!IMPORTANT] 
> El script de PowerShell que se describe en este artículo crea un clúster de Microsoft HPC Pack 2012 R2 en Azure mediante el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.
> Además, el script que se describe en este artículo no es compatible con HPC Pack 2016.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Archivos de configuración de ejemplo
En los siguientes ejemplos, sustituya por sus propios valores el nombre o la identificación de la suscripción y los nombres de cuenta y servicio.

### <a name="example-1"></a>Ejemplo 1
El siguiente archivo de configuración implementa un clúster de HPC Pack que tiene un nodo principal con bases de datos locales y cinco nodos de proceso que ejecutan el sistema operativo Windows Server 2012 R2. Todos los servicios en la nube se crean directamente en la ubicación oeste de EE. UU. El nodo principal actúa como controlador de dominio del bosque de dominio.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Ejemplo 2
El archivo de configuración siguiente implementa un clúster de HPC Pack en un bosque de dominio existente. El clúster tiene un nodo principal con bases de datos locales y 12 nodos de proceso con la extensión de máquina virtual BGInfo aplicada.
La instalación automática de actualizaciones de Windows está deshabilitada para todas las máquinas virtuales en el bosque de dominio. Todos los servicios en la nube se crean directamente en la ubicación de Este de Asia. Los nodos de proceso se crean en tres servicios en la nube y tres cuentas de almacenamiento: de *MyHPCCN-0001* a *MyHPCCN-0005* en *MyHPCCNService01* y *mycnstorage01*; de *MyHPCCN-0006* a *MyHPCCN0010* en *MyHPCCNService02* y *mycnstorage02*; y de *MyHPCCN-0011* a *MyHPCCN-0012* en *MyHPCCNService03* y *mycnstorage03*). Los nodos de proceso se crean a partir de una imagen privada existente capturada desde un nodo de proceso. El servicio de crecimiento y reducción automático está habilitado con intervalos de crecimiento y reducción predeterminados.

```Xml
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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Ejemplo 3
El archivo de configuración siguiente implementa un clúster de HPC Pack en un bosque de dominio existente. El clúster contiene un nodo principal, un servidor de bases de datos con un disco de datos de 500 GB, dos nodos de agente que ejecutan el sistema operativo Windows Server 2012 R2 y cinco nodos de proceso que ejecutan el sistema operativo Windows Server 2012 R2. El servicio en la nube MyHPCCNService se crea en el grupo de afinidad *MyIBAffinityGroup*, mientras que los restantes servicios en la nube se crean en el grupo de afinidad *MyAffinityGroup*. La API de REST del Programador de trabajos de HPC y el portal web de HPC están habilitados en el nodo principal.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Ejemplo 4
El archivo de configuración siguiente implementa un clúster de HPC Pack en un bosque de dominio existente. El clúster tiene dos nodos principales con bases de datos locales y se crean dos plantillas de nodo de Azure y tres nodos de Azure de tamaño medio para la plantilla de nodo *AzureTemplate1*de Azure. Se ejecuta un archivo de script en el nodo principal después de configurar este nodo.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Solución de problemas
* **Error "La red virtual no existe"**: si ejecuta el script para implementar varios clústeres en Azure simultáneamente con una única suscripción, puede producirse un error de "La red virtual *Nombre de\_red virtual* no existe" en una implementación o varias.
  Si se produce este error, vuelva a ejecutar el script para la implementación en la que ocurrió el error.
* **Problemas de acceso a Internet desde la red virtual de Azure** : si crea un clúster con un nuevo controlador de dominio mediante el script de implementación o promueve manualmente una máquina virtual del nodo principal a un controlador de dominio, puede experimentar problemas al conectar las máquinas virtuales a Internet. Este problema puede ocurrir si se configura automáticamente un servidor de reenviador DNS en el controlador de dominio y este servidor de reenviador DNS no se resuelve correctamente.
  
    Para evitar este problema, inicie sesión en el controlador de dominio y, o bien, quite la configuración de reenviador, o bien, configure un servidor de reenviador DNS válida. Para configurar esta opción, en Administrador del servidor, haga clic **Herramientas** >
    **DNS** para abrir el Administrador de DNS y, a continuación, haga doble clic en **Reenviadores**.
* **Problemas de acceso a la red RDMA desde máquinas virtuales de proceso intensivo** : si agrega máquinas virtuales de nodo de agente o de nodo de proceso de Windows Server mediante un tamaño compatible con RDMA, como A8 o A9, puede experimentar problemas para conectar estas máquinas virtuales a la red de aplicación RDMA. Una razón por la que puede ocurrir esto es que la extensión HpcVmDrivers no esté correctamente instalada cuando las máquinas virtuales se agregan al clúster. Por ejemplo, la extensión puede bloquearse en el estado de instalación.
  
    Para evitar este problema, compruebe primero el estado de la extensión en las máquinas virtuales. Si la extensión no está instalada correctamente, intente quitar los nodos del clúster de HPC y, a continuación, vuelva a agregarlos. Por ejemplo, puede agregar máquinas virtuales de nodos de proceso mediante la ejecución del script Add-HpcIaaSNode.ps1 en el nodo principal.

## <a name="next-steps"></a>Pasos siguientes
* Pruebe a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte la [guía de introducción](https://technet.microsoft.com/library/jj884144)de HPC Pack.
* Para ver un tutorial sobre cómo crear un script de implementación del clúster y ejecutar una carga de trabajo de HPC, consulte [Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pruebe las herramientas de HPC Pack para iniciar, detener, agregar y quitar nodos de proceso de un clúster creado. Consulte [Administración de nodos de proceso en un clúster de HPC Pack en Azure](hpcpack-cluster-node-manage.md).
* Para configurar el envío de trabajos al clúster desde un equipo local, consulte [Envío de trabajos HPC desde un equipo local a un clúster de HPC Pack implementado en Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

