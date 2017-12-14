---
title: "Azure Service Fabric Docker Compose (versión preliminar) | Microsoft Docs"
description: "Azure Service Fabric acepta el formato de Docker Compose para facilitar la organización de los contenedores existentes mediante Service Fabric. La compatibilidad con Docker Compose está actualmente en versión preliminar."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 772e51519d1ad45ababa0f4c1f4b402d280f9c14
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Uso de complementos de volumen y controladores de registro de Docker en el contenedor
Azure Service Fabric admite la especificación de [complementos de volumen de Docker](https://docs.docker.com/engine/extend/plugins_volume/) y [controladores de registro de Docker](https://docs.docker.com/engine/admin/logging/overview/) en el servicio de contenedor. Puede conservar los datos en [Azure Files](https://azure.microsoft.com/services/storage/files/) cuando el contenedor se mueve o se reinicia en un host diferente.

Actualmente solo se admiten controladores de volumen en contenedores de Linux. Si va a usar contenedores de Windows, puede asignar un volumen a un [recurso compartido de SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) de Azure Files sin un controlador de volumen. Para esta asignación, actualice las máquinas virtuales (VM) del clúster a la versión más reciente de Windows Server 1709.


## <a name="install-the-docker-volumelogging-driver"></a>Instalación del controlador de volumen/registro de Docker

Si el controlador de volumen/registro de Docker no está instalado en la máquina, puede instalarlo manualmente mediante los protocolos RDP/SSH. Puede realizar la instalación con estos protocolos mediante un [script de inicio de conjunto de escalado de máquinas virtuales](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) o un [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

A continuación se muestra un ejemplo del script para instalar el [controlador de volumen de Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/):

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Especificación del complemento o el controlador en el manifiesto
Los complementos se especifican en el manifiesto de aplicación de la manera siguiente:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

La etiqueta **Origen** del elemento **Volumen** hace referencia a la carpeta de origen. La carpeta de origen puede ser una carpeta de la máquina virtual que hospeda los contenedores o un almacén remoto persistente. La etiqueta **Destino** es la ubicación a la que se asigna el **origen** dentro del contenedor en ejecución. Por lo tanto, el destino no puede ser una ubicación que ya exista dentro del contenedor.

Al especificar un complemento de volumen, Service Fabric crea automáticamente el volumen con los parámetros especificados. La etiqueta **Origen** es el nombre del volumen y la etiqueta **Controlador** especifica el complemento del controlador de volumen. Se pueden especificar opciones mediante la etiqueta **DriverOption** de la manera siguiente:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Si se especifica un controlador de registro de Docker, debe implementar agentes (o contenedores) para administrar los registros en el clúster. La etiqueta **DriverOption** se puede usar para especificar opciones para el controlador de registro.

## <a name="next-steps"></a>Pasos siguientes
Para implementar contenedores en un clúster de Service Fabric, consulte [Implementación de un contenedor en Service Fabric](service-fabric-deploy-container.md).
