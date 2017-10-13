---
title: "Inclusión de los microservicios de Azure Service Fabric en contenedores (versión preliminar)"
description: "Azure Service Fabric ha agregado una nueva funcionalidad para incluir los microservicios de Service Fabric en contenedores. Esta funcionalidad actualmente está en su versión preliminar."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 6f8ad0bad8d1ae861e6b72f7e1a32ab0675813c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Inclusión de Reliable Services y Reliable Actors de Service Fabric en contenedores (versión preliminar)

Service Fabric admite la inclusión de microservicios de Service Fabric en contenedores (servicios basados en Reliable Services y Reliable Actors). Para más información, consulte [Service Fabric y los contenedores](service-fabric-containers-overview.md).


 Esta característica se encuentra en versión preliminar y en este artículo se proporcionan los distintos pasos para que el servicio se ejecute dentro de un contenedor.  

> [!NOTE]
> Esta característica se encuentra en versión preliminar y no se admite en producción. Actualmente, esta característica solo funciona para Windows.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Pasos para incluir la aplicación de Service Fabric en contenedores

1. Abra la aplicación de Service Fabric en Visual Studio.

2. Agregue la clase [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) al proyecto. El código de esta clase es una aplicación auxiliar para cargar correctamente los archivos binarios del entorno en tiempo de ejecución de Service Fabric dentro de la aplicación cuando se ejecuta dentro de un contenedor.

3. Para cada paquete de código que le gustaría incluir en contenedores, inicialice el cargador en el punto de entrada del programa. Agregue el constructor estático que se muestra en el siguiente fragmento de código al archivo de punto de entrada del programa.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Compile y [empaquete](service-fabric-package-apps.md#Package-App) el proyecto. Para compilar y crear un paquete, haga clic con el botón derecho en el proyecto de aplicación en el Explorador de soluciones y elija el comando **Empaquetar**.

5. Para cada paquete de código que necesite incluir en contenedores, ejecute el script de PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). El uso es como sigue:
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  El script crea una carpeta con los artefactos de Docker en $dockerPackageOutputDirectoryPath. Modifique el archivo de Docker generado para exponer los puertos, ejecutar scripts de configuración, etc. según sus necesidades.

6. A continuación, necesita [compilar](service-fabric-get-started-containers.md#Build-Containers) e [insertar](service-fabric-get-started-containers.md#Push-Containers) el paquete de contenedor de Docker en el repositorio.

7. Modifique los archivos ApplicationManifest.xml y ServiceManifest.xml para agregar la imagen del contenedor, la información del repositorio, la autenticación del registro y la asignación de puerto al host. Para modificar los manifiestos, consulte [Cree la primera aplicación contenedora en Service Fabric en Windows](service-fabric-get-started-containers.md). La definición de paquete de código en el manifiesto de servicio debe reemplazarse por la imagen de contenedor correspondiente. Asegúrese de cambiar EntryPoint a un tipo ContainerHost.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. Agregue la asignación de host a puerto para el replicador y el punto de conexión de servicio. Puesto que Service Fabric asigna ambos puertos en tiempo de ejecución, ContainerPort se establece en cero para usar el puerto asignado para la asignación.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Para probar esta aplicación, debe implementarla en un clúster que esté ejecutando la versión 5.7 o posterior. Además, debe editar y actualizar la configuración del clúster para habilitar esta característica de versión preliminar. Siga los pasos de este [artículo](service-fabric-cluster-fabric-settings.md) para agregar la configuración que se muestra a continuación.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. Después, [implemente](service-fabric-deploy-remove-applications.md) el paquete de aplicación editado en este clúster.

Ahora debería tener una aplicación de Service Fabric en contenedores ejecutando el clúster.

## <a name="next-steps"></a>Pasos siguientes
* Más información acerca de cómo ejecutar [contenedores en Service Fabric](service-fabric-get-started-containers.md).
* Más información acerca del [ciclo de vida de aplicaciones](service-fabric-application-lifecycle.md) de Service Fabric.
