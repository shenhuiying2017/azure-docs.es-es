---
title: "Versión preliminar de Azure Service Fabric Docker Compose | Microsoft Docs"
description: "Azure Service Fabric acepta el formato de Docker Compose para facilitar la orquestación de los contenedores existentes mediante Service Fabric. Esta compatibilidad se encuentra actualmente en versión preliminar."
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
ms.openlocfilehash: cf7b0dd3a81c35be4907dbba85b72ce4f87e3a9f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Uso de los complementos de volumen y los controladores de registro para el contenedor

Service Fabric admite la determinación de los [complementos de volumen de Docker](https://docs.docker.com/engine/extend/plugins_volume/) y de los [controladores de registro de Docker](https://docs.docker.com/engine/admin/logging/overview/) para Container Service. 

## <a name="install-volumelogging-driver"></a>Instalación del controlador de volumen o registro

Si el controlador de volumen o registro de Docker no está instalado en la máquina, instálelo manualmente a través de la realización de RDP/SSH en la máquina o a través de un script de inicio de VMSS. Por ejemplo, para instalar el controlador de volumen de Docker, realice un SSH en el equipo y ejecute:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Especificación del complemento o controlador en el manifiesto
Los complementos se especifican en el manifiesto de aplicación, como se muestra en el siguiente manifiesto:

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

En el ejemplo anterior, la etiqueta `Source` de `Volume` hace referencia a la carpeta de origen. La carpeta de origen puede ser una carpeta de la máquina virtual que hospeda los contenedores o un almacén remoto persistente. La etiqueta `Destination` es la ubicación a la que `Source` está asignado dentro del contenedor en ejecución. 

Al especificar un complemento de volumen, Service Fabric crea automáticamente el volumen con los parámetros especificados. La etiqueta `Source` es el nombre del volumen y la etiqueta `Driver` especifica el complemento de controlador del volumen. Las opciones pueden especificarse mediante la etiqueta `DriverOption`, como se muestra en el fragmento de código siguiente:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azurefile" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Si se especifica un controlador de registro de Docker, es necesario implementar agentes o contenedores para controlar los registros en el clúster.  La etiqueta `DriverOption` además se puede usar para especificar opciones del controlador de registro.

Consulte los artículos siguientes para implementar contenedores en un clúster de Service Fabric:


[Implementación de un contenedor en Service Fabric](service-fabric-deploy-container.md)

