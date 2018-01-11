---
title: "Creación de un clúster de Azure Service Fabric independiente | Microsoft Docs"
description: "Cree un clúster de Azure Service Fabric en cualquier máquina (física o virtual) que ejecute Windows Server, ya sea local o en una nube."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik;dekapur
ms.openlocfilehash: 4b3b1e8a368419fe4014802f7b2a35b7ae3fba3c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Creación de un clúster independiente con Windows Server
Puede usar Azure Service Fabric para crear clústeres de Service Fabric en las máquinas virtuales o los equipos que ejecutan Windows Server. Es decir, podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos con Windows Server que estén conectados entre sí, ya sea de manera local o con algún proveedor de servicios en la nube. Service Fabric proporciona un paquete de instalación para crear clústeres de Service Fabric, llamado paquete independiente de Windows Server.

Este artículo le guía por los pasos para crear un clúster independiente de Service Fabric.

> [!NOTE]
> Este paquete de Windows Server independiente está disponible en el mercado y puede usarse para las implementaciones de producción. Además, puede contener nuevas características de Service Fabric que están en versión preliminar. Desplácese hacia abajo hasta "[Características de versión preliminar incluidas en este paquete](#previewfeatures_anchor)". Sección de la lista de las características de versión preliminar. Puede [descargar una copia de los términos de licencia](http://go.microsoft.com/fwlink/?LinkID=733084) ahora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Soporte técnico para el paquete de Service Fabric para Windows Server
* Pregunte a la comunidad sobre el paquete independiente de Service Fabric para Windows Server en el [foro de Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra una incidencia para obtener [soporte técnico profesional para Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Más información sobre el soporte técnico profesional de Microsoft[aquí](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* También puede obtener soporte técnico para este paquete como parte del [soporte técnico Premier de Microsoft](https://support.microsoft.com/en-us/premier).
* Para más información, consulte [Opciones de soporte técnico de Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Para recopilar registros como soporte técnico, ejecute el [recopilador de registros independiente de Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Descarga del paquete de Service Fabric para Windows Server
Para crear el clúster, use el paquete de Service Fabric para Windows Server (versión 2012 R2 y posteriores) que se encuentra aquí: <br>
[Vínculo de descarga del paquete independiente de Service Fabric de Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Puede encontrar detalles sobre el contenido del paquete [aquí](service-fabric-cluster-standalone-package-contents.md).

El paquete en tiempo de ejecución de Service Fabric se descarga automáticamente en el momento de la creación del clúster. Si la implementación se realiza desde una máquina no está conectada a Internet, descargue el paquete en tiempo de ejecución fuera de banda desde aquí: <br>
[Vínculo de descarga del entorno de tiempo de ejecución de Service Fabric para Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Puede encontrar ejemplos de configuración de clúster independiente en: <br>
[Ejemplos de configuración de clúster independiente](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Creación de clústeres
Service Fabric se puede implementar en un clúster de desarrollo de una máquina mediante el archivo *ClusterConfig.Unsecure.DevCluster.json* que se incluye en los [ejemplos](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Desempaquete el paquete independiente en su máquina, copie el archivo de configuración de ejemplo en la máquina local y ejecute luego el script *CreateServiceFabricCluster.ps1* mediante una sesión de administrador de PowerShell, desde la carpeta del paquete independiente:
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Paso 1A: Creación de un clúster de desarrollo local poco seguro
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Para detalles de solución de problemas, consulte la sección Configuración del entorno del artículo sobre el [planeamiento la preparación de la implementación del clúster](service-fabric-cluster-standalone-deployment-preparation.md).

Si ya ha terminado de ejecutar los escenarios de desarrollo, puede quitar el clúster de Service Fabric de la máquina; para ello, consulte los pasos de la sección "[Eliminación de un clúster](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Paso 1B: Creación de un clúster de varias máquinas
Después de haber realizado los pasos de planeamiento y preparación detallados en el siguiente vínculo, está listo para crear el clúster de producción mediante el archivo de configuración del clúster. <br>
[Planeamiento y preparación de la implementación de un clúster independiente de Service Fabric](service-fabric-cluster-standalone-deployment-preparation.md)

1. Valide el archivo de configuración que se ha escrito ejecutando el script *TestConfiguration.ps1* desde la carpeta del paquete independiente:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    El resultado debería ser parecido al que se muestra a continuación. Si el campo inferior "Pasado" se devuelve como "True", se han superado las comprobaciones de integridad y el clúster parece ser implementable según la configuración de entrada.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Cree el clúster: ejecute el script *CreateServiceFabricCluster.ps1* para implementar el clúster de Service Fabric en las máquinas de la configuración. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Se escriben seguimientos de implementación en la máquina virtual/máquina física en la que ejecutó el script CreateServiceFabricCluster.ps1 de PowerShell. Estos seguimientos se pueden encontrar en la subcarpeta DeploymentTraces, que se encuentra en el directorio desde el que se ejecutó el script. Para ver si Service Fabric se ha implementado correctamente en una máquina, busque los archivos instalados en el directorio FabricDataRoot, como se detalla en la sección FabricSettings del archivo de configuración del clúster (de forma predeterminada c:\ProgramData\SF). También, los procesos FabricHost.exe y Fabric.exe se pueden ver ejecutando el Administrador de tareas.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Paso 1C: Creación de un clúster sin conexión (desconectado de internet)
El paquete en tiempo de ejecución de Service Fabric se descarga automáticamente durante la creación del clúster. Al implementar un clúster en máquinas sin conexión a internet, debe descargar el paquete en tiempo de ejecución de Service Fabric por separado y proporcionar su ruta de acceso durante la creación del clúster.
El paquete en tiempo de ejecución se puede descargar por separado desde otra máquina conectada a internet, desde el [vínculo de descarga: Service Fabric en tiempo de ejecución para Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie el paquete en tiempo de ejecución donde vaya a implementar el clúster desconectado y cree el clúster mediante la ejecución de `CreateServiceFabricCluster.ps1` con el parámetro `-FabricRuntimePackagePath` incluido, tal y como se muestra a continuación: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
donde `.\ClusterConfig.json` y `.\MicrosoftAzureServiceFabric.cab` son las rutas de acceso a la configuración del clúster y el archivo .cab en tiempo de ejecución, respectivamente.


### <a name="step-2-connect-to-the-cluster"></a>Paso 2: Conexión al clúster
Para conectarse a un clúster seguro, vea [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md) en Service Fabric.

Para conectarse a un clúster no seguro, ejecute el siguiente comando de PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Ejemplo:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Paso 3: Incorporación de Service Fabric Explorer
Ahora puede conectarse al clúster con Service Fabric Explorer directamente desde una de las máquinas con http://localhost:19080/Explorer/index.html o de forma remota con http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Agregar y quitar nodos
Puede agregar o quitar nodos del clúster de Service Fabric independiente a medida que cambien las necesidades empresariales. Lea [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md) para obtener pasos detallados.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Eliminación de un clúster
Para quitar un clúster, ejecute el script de PowerShell *RemoveServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON. También puede especificar una ubicación para el registro que se va a eliminar.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Datos de telemetría recopilados y cómo omitirlos
De forma predeterminada, el producto recopila datos de telemetría sobre el uso de Service Fabric para mejorar el producto. El Analizador de procedimientos recomendados que se ejecuta como parte de la instalación, comprueba la conectividad a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Si no está accesible, el programa de instalación genera un error, a menos que decida no participar en la telemetría.

1. La canalización de telemetría intenta cargar los datos siguientes en [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) una vez al día. Es una carga de mejor esfuerzo y no tiene ningún impacto en la funcionalidad del clúster. Solo se envía la telemetría desde el nodo que ejecuta el administrador de conmutación por error principal. Ningún otro nodo envía telemetría.
2. La telemetría consta de lo siguiente:

* Número de servicios
* Número de ServiceTypes
* Número de aplicaciones
* Número de ApplicationUpgrades
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de UnhealthyFailoverUnits
* Número de réplicas
* Número de InBuildReplicas
* Número de StandByReplicas
* Número de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de nodos
* IsContextComplete: True/False
* ClusterId: se trata de un GUID generado aleatoriamente para cada clúster
* ServiceFabricVersion
* Dirección IP de la máquina virtual o la máquina desde la que se carga la telemetría

Para deshabilitar la telemetría, agregue lo siguiente al elemento *properties* en la configuración del clúster: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Características de versión preliminar incluidas en este paquete
Ninguno.


> [!NOTE]
> Con la nueva [versión de GA del clúster independiente para Windows Server (versión 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), puede actualizar el clúster a versiones futuras, manual o automáticamente. Consulte el documento [Actualización del clúster de Service Fabric independiente en Windows Server](service-fabric-cluster-upgrade-windows-server.md) para obtener más información.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Implementación y eliminación de aplicaciones con PowerShell](service-fabric-deploy-remove-applications.md)
* [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)
* [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Actualización de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-upgrade-windows-server.md)
* [Creación de un clúster de Service Fabric independiente con máquinas virtuales de Azure con Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
* [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
