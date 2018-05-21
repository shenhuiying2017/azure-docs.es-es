---
title: Controlador de volúmenes de Azure Files en Service Fabric (versión preliminar) | Microsoft Docs
description: Service Fabric admite el uso de Azure Files para realizar copias de seguridad de los volúmenes desde su contenedor. Esto se encuentra actualmente en versión preliminar.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: 2d98cff1a5869091aa81097bbb34da6e525a2ad5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Controlador de volúmenes de Azure Files en Service Fabric (versión preliminar)
El complemento de volúmenes de Azure Files es un [complemento de volúmenes de Docker](https://docs.docker.com/engine/extend/plugins_volume/) que proporciona volúmenes basados en [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) para los contenedores de Docker. Este complemento de volúmenes de Docker se empaqueta como aplicación de Service Fabric que se puede implementar en clústeres de Service Fabric. Su objetivo es proporcionar volúmenes basados en Azure Files a otras aplicaciones de contenedor de Service Fabric que se implementan en el clúster.

> [!NOTE]
> La versión 6.255.389.9494 del complemento de volúmenes de Azure Files es una versión preliminar que está disponible con este documento. Como versión preliminar, **no** se admite para su uso en entornos de producción.
>

## <a name="prerequisites"></a>requisitos previos
* La versión de Windows del complemento de volúmenes de Azure Files funciona en [Windows Server versión 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versión 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) o sistemas operativos posteriores únicamente. La versión de Linux del complemento de volúmenes de Azure Files funciona en todas las versiones de sistemas operativos admitidas por Service Fabric.

* Siga las instrucciones en la [documentación de Azure Files](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share) para crear un recurso compartido de archivos para la aplicación de contenedor de Service Fabric que se usará como volumen.

* Necesitará que [Powershell con el módulo de Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) o [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) esté instalado.

## <a name="deploy-the-service-fabric-azure-files-application"></a>Implementación de la aplicación de Azure Files en Service Fabric

La aplicación de Service Fabric que proporciona los volúmenes para los contenedores puede descargarse del [vínculo](https://aka.ms/sfvolume) siguiente. La aplicación puede implementarse en el clúster mediante [API de PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) o [FabricClient](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Mediante la línea de comandos, cambie el directorio al directorio raíz del paquete de aplicación descargado. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Copie el paquete de aplicación en el almacén de imágenes. Ejecute el comando siguiente con el valor adecuado en [ApplicationPackagePath] y [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Cargar el tipo de aplicación

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Crear la aplicación En el comando para crear la aplicación siguiente, tenga en cuenta el parámetro de aplicación **ListenPort**. Este valor especificado para este parámetro de aplicación es el puerto en el que el complemento de volúmenes de Azure Files escucha las solicitudes desde el demonio de Docker. Es importante asegurarse de que el puerto que se proporciona a la aplicación no entre en conflicto con ningún otro puerto que usen el clúster o las aplicaciones.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter no admite montajes de SMB de asignación en contenedores ([solo se admiten en Windows Server versión 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Esta restricción evita que haya asignaciones de volumen de red y controladores de volúmenes de Azure Files en versiones anteriores a 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implementación de la aplicación en un clúster de desarrollo local
El número de instancias de servicio predeterminado para la aplicación del complemento de volúmenes de Azure Files es -1; es decir, hay una instancia del servicio implementada en cada nodo del clúster. Sin embargo, al implementar la aplicación del complemento de volúmenes de Azure Files en un clúster de desarrollo local, el recuento de instancias de servicio debe especificarse como 1. Esto puede hacerse a través del parámetro de aplicación **InstanceCount**. Por lo tanto, el comando para implementar la aplicación del complemento de volúmenes de Azure Files en un clúster de desarrollo local es:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Configuración de las aplicaciones para usar el volumen
El siguiente fragmento de código muestra cómo se puede especificar un volumen basado en Azure Files en el manifiesto de aplicación de la aplicación. El elemento específico de interés es la etiqueta **Volume**:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
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

El nombre de controlador para el complemento de volúmenes de Azure Files es **sfazurefile**. Este valor se establece para el atributo **Driver** del elemento **Volume** en el manifiesto de aplicación.

En el elemento **Volume** en el fragmento de código anterior, el complemento de volúmenes de Azure Files requiere las siguientes etiquetas: 
- **Source**: hace referencia a la carpeta de origen, que puede ser una carpeta de la máquina virtual que hospeda los contenedores o un almacén remoto persistente.
- **Destination**: esta etiqueta es la ubicación a la que se asigna **Source** dentro del contenedor en ejecución. Por lo tanto, el destino no puede ser una ubicación que ya exista dentro del contenedor.

Como se muestra en los elementos **DriverOption** en el fragmento de código anterior, el complemento de volúmenes de Azure Files admite las siguientes opciones de controlador:

- **shareName**: nombre del recurso compartido de archivos de Azure Files que proporciona el volumen para el contenedor
- **storageAccountName**: nombre de la cuenta de almacenamiento de Azure que contiene el recurso compartido de archivos de Azure Files
- **storageAccountKey**: clave de acceso de la cuenta de almacenamiento de Azure que contiene el recurso compartido de archivos de Azure Files

Todas las opciones de controlador anteriores son obligatorias. 

## <a name="using-your-own-volume-or-logging-driver"></a>Uso de su propio controlador de volumen o registro
Service Fabric también permite el uso de sus propios controladores de volumen o de registro personalizados. Si el controlador de volumen/registro de Docker no está instalado en el clúster, puede instalarlo manualmente mediante los protocolos RDP/SSH. Puede realizar la instalación con estos protocolos mediante un [script de inicio de conjunto de escalado de máquinas virtuales](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) o un [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

A continuación se muestra un ejemplo del script para instalar el [controlador de volumen de Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/):

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

En las aplicaciones, para usar el controlador de volumen o de registro que ha instalado, deberá especificar los valores apropiados en los elementos **Volume** y **LogConfig** en  **ContainerHostPolicies** en el manifiesto de aplicación. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver ejemplos de contenedor, incluido el controlador de volumen, visite los [ejemplos de contenedor de Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Para implementar contenedores en un clúster de Service Fabric, consulte el artículo [Cree la primera aplicación contenedora en Service Fabric en Windows](service-fabric-deploy-container.md).


