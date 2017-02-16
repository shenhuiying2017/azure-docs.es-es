---
title: "Creación y administración de un clúster de Azure Service Fabric independiente | Microsoft Docs"
description: "Cree y administre un clúster de Azure Service Fabric en cualquier máquina (física o virtual) que ejecute Windows Server, ya sea local o en una nube."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2016
ms.author: dkshir;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 6db229794c90e985de834bd3bfb6e0b030de2df5
ms.openlocfilehash: 0cb59a2e94a9c985cb56d9dd20c05e2e22a45151


---
# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Creación y administración de un clúster que se ejecute en Windows Server
Puede usar Azure Service Fabric para crear clústeres de Service Fabric en las máquinas virtuales o los equipos que ejecutan Windows Server. Es decir, podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos con Windows Server que estén conectados entre sí, ya sea de manera local o con algún proveedor de servicios en la nube. Service Fabric proporciona un paquete de instalación para crear clústeres de Service Fabric, llamado paquete independiente de Windows Server.

Este artículo lo guía por los pasos para crear un clúster localmente mediante el paquete independiente para Service Fabric, aunque puede adaptarse con facilidad a cualquier otro entorno, por ejemplo, otros proveedores de nube.

> [!NOTE]
> Este paquete de Windows Server independiente está disponible en el mercado y puede usarse para las implementaciones de producción. Además, puede contener nuevas características de Service Fabric que están en versión preliminar. Desplácese hacia abajo hasta Características de versión preliminar incluidas en este paquete. Sección de la lista de las características de versión preliminar. Puede [descargar una copia de los términos de licencia](http://go.microsoft.com/fwlink/?LinkID=733084) ahora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-standalone-package"></a>Obtención de soporte técnico para el paquete independiente de Service Fabric
* Pregunte a la comunidad sobre el paquete independiente de Service Fabric para Windows Server en el [foro de Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra una incidencia para obtener [soporte técnico profesional para Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Obtenga más información sobre el [soporte técnico profesional de Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* También puede obtener soporte técnico para este paquete como parte del [soporte técnico premier de Microsoft](https://support.microsoft.com/en-us/premier). 

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-standalone-package"></a>Descarga del paquete independiente de Service Fabric
[Descargue el paquete independiente para Service Fabric para Windows Server 2012 R2 y versiones posteriores](http://go.microsoft.com/fwlink/?LinkId=730690), que se denomina Microsoft.Azure.ServiceFabric.WindowsServer.&lt;versión&gt;.zip.

En el paquete de descarga, encontrará los siguientes archivos:

| **Nombre de archivo** | **Descripción breve** |
| --- | --- |
| MicrosoftAzureServiceFabric.cab |Archivo .cab que contiene los archivos binarios que se implementan en cada máquina del clúster. |
| ClusterConfig.Unsecure.DevCluster.json |Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster de desarrollo no seguro con una sola máquina (o máquina virtual) y tres nodos, incluidos los datos de cada nodo del clúster. |
| ClusterConfig.Unsecure.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster no seguro con varias máquinas (o máquinas virtuales), incluida la información para cada máquina del clúster. |
| ClusterConfig.Windows.DevCluster.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster de desarrollo seguro con una sola máquina (o máquina virtual) y tres nodos, incluidos los datos de cada nodo del clúster. El clúster está protegido mediante [identidades de Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster seguro con varias máquinas (o máquinas virtuales) mediante la utilización de la seguridad de Windows, incluida la información para cada máquina del clúster seguro. El clúster está protegido mediante [identidades de Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster de desarrollo seguro con una sola máquina (o máquina virtual) y tres nodos, incluida la información de cada nodo del clúster. El clúster se protege mediante certificados X509. |
| ClusterConfig.x509.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster seguro con varias máquinas (o máquinas virtuales), incluida la información para cada nodo del clúster seguro. El clúster se protege mediante certificados X509. |
| EULA_ENU.txt |Términos de licencia para usar el paquete independiente de Windows Server para Microsoft Azure Service Fabric. Puede [descargar una copia de los términos de licencia](http://go.microsoft.com/fwlink/?LinkID=733084) ahora. |
| Readme.txt |Un vínculo a las notas de la versión e instrucciones de instalación básica. Se trata de un subconjunto de las instrucciones de este documento. |
| CreateServiceFabricCluster.ps1 |Un script de PowerShell que crea el clúster mediante la configuración de ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Un script de PowerShell que elimina el clúster mediante la configuración de ClusterConfig.json. |
| ThirdPartyNotice.rtf |Aviso de software de terceros que está en el paquete. |
| AddNode.ps1 |Un script de PowerShell para agregar un nodo a un clúster implementado existente. |
| RemoveNode.ps1 |Un script de PowerShell para quitar un nodo de un clúster implementado existente. |
| CleanFabric.ps1 |Un script de PowerShell para limpiar una instalación independiente de Service Fabric de la máquina actual. Las instalaciones anteriores de MSI se deben quitar mediante sus propios desinstaladores asociados. |
| TestConfiguration.ps1 |Un script de PowerShell para analizar la infraestructura como se especifica en Cluster.json. |

## <a name="plan-and-prepare-your-cluster-deployment"></a>Planeamiento y preparación para la implementación del clúster
Realice los pasos siguientes antes de crear el clúster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Paso 1: Planear la infraestructura de clúster
Va a crear un clúster de Service Fabric en máquinas que posee, por lo que podrá decidir qué tipos de errores desea que resista el clúster. Por ejemplo, ¿necesita separar redes eléctricas o conexiones a Internet que proporcionan estas máquinas? Además, debe tener en cuenta la seguridad física de estas máquinas. ¿Donde se encuentran las máquinas y quién tiene acceso a ellas? Una vez que haya tomado estas decisiones, asigne las máquinas de manera lógica a los distintos dominios de error (vea el paso 4). El planeamiento de la infraestructura para clústeres de producción es más complicado que el de los clústeres de prueba.

<a id="preparemachines"></a>

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Paso 2: Preparar las máquinas para cumplir los requisitos previos
Requisitos previos para cada máquina que desee agregar al clúster:

* Se recomiendan como mínimo 16 GB de RAM.
* Se recomiendan 40 GB de espacio disponible en disco como mínimo.
* Se recomienda una CPU de cuatro núcleos o más.
* Conectividad a una o varias redes seguras para todas las máquinas.
* Windows Server 2012 R2 o Windows Server 2012 (debe tener instalado [KB2858668](https://support.microsoft.com/kb/2858668) ).
* [.NET Framework 4.5.1 o posterior](https://www.microsoft.com/download/details.aspx?id=40773)(instalación completa).
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
* El [servicio RemoteRegistry](https://technet.microsoft.com/library/cc754820) se debe estar ejecutando en todas las máquinas.

El administrador de clúster que implemente y configure el clúster debe tener [privilegios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) en cada una de las máquinas. No se puede instalar Service Fabric en un controlador de dominio.

### <a name="step-3-determine-the-initial-cluster-size"></a>Paso 3: Determinar el tamaño inicial del clúster
Cada nodo de un clúster de Service Fabric independiente tiene el sistema en tiempo de ejecución de Service Fabric implementado y es miembro del clúster. En una implementación típica de producción, hay un nodo por cada instancia de sistema operativo (física o virtual). El tamaño del clúster depende de sus necesidades empresariales. Sin embargo, debe tener un tamaño mínimo de clúster de tres nodos (máquinas o máquinas virtuales).
Con fines de desarrollo, puede tener más de un nodo en una máquina determinada. En un entorno de producción, Service Fabric solo admite un nodo por máquina virtual o física.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Paso 4: Determinar el número de dominios de error y de actualización
Un *dominio de error* (FD) es una unidad física de error, y está directamente relacionado con la infraestructura física de los centros de datos. Un dominio de error consta de componentes de hardware (equipos, conmutadores, redes, etc.) que comparten un único punto de error. Aunque no hay ninguna asignación 1:1 entre dominios de error y racks, en términos generales, cada bastidor puede considerarse un dominio de error. A la hora de valorar los nodos del clúster, se recomienda que los nodos se distribuyan entre tres dominios de error como mínimo.

Si especifica dominios de error en ClusterConfig.json, puede elegir el nombre de cada uno. Service Fabric admite FD jerárquicos, así que puede reflejar la topología de infraestructura en ellos.  Por ejemplo, los siguientes FD son válidos:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Un *dominio de actualización* (UD) es una unidad lógica de nodos. Durante una actualización de Service Fabric organizada (una actualización de la aplicación o del clúster), todos los nodos de un UD se eliminan para llevar a cabo este proceso, mientras que los nodos de otros UD siguen estando disponibles para atender las solicitudes. Las actualizaciones de firmware que realice en las máquinas no respetan los UD, así que debe realizar este proceso de forma individual.

La manera más sencilla de pensar en estos conceptos es considerar los FD como la unidad de error no planeada, y los UD, como la unidad de mantenimiento planeado.

Si especifica dominios de actualización en ClusterConfig.json, puede elegir el nombre de cada uno. Por ejemplo, los siguientes nombres son válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Para más información sobre los dominios de actualización y los dominios de error, vea [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Paso 5: Descarga del paquete independiente de Windows Server para Service Fabric
[Descargue el paquete independiente de Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en una máquina de implementación que no forme parte del clúster, o bien en una de las máquinas que formarán parte del clúster. Puede cambiar el nombre de la carpeta descomprimida `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>

## <a name="create-your-cluster"></a>Creación del clúster
Después de haber realizado los pasos de planeación y preparación anterior, ya puede crear el clúster.

### <a name="step-1-modify-cluster-configuration"></a>Paso 1: Modificar la configuración de clúster
El clúster se describe en un archivo ClusterConfig.json. Para ver detalles sobre las secciones de este archivo, vea [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md).
Abra uno de los archivos ClusterConfig.json del paquete descargado y modifique las siguientes opciones:

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

| **Opciones de configuración.** | **Descripción** |
| --- | --- |
| **NodeTypes** |Los tipos de nodo permiten separar los nodos de clúster en grupos distintos. Un clúster debe tener al menos un tipo NodeType. Todos los nodos de un grupo tienen las siguientes características comunes:  <br> **nombre** : es el nombre del tipo de nodo. <br>**Endpoint Ports** : diversos puntos de conexión (puertos) con nombre asociados a este tipo de nodo. Puede usar cualquier número de puerto que desee, siempre que no entren en conflicto con nada de este manifiesto y que no estén siendo usados por otra aplicación que se ejecute en el equipo o en la máquina virtual. <br> **Propiedades de ubicación**: Describen las propiedades de este tipo de nodo que se usan como restricciones de ubicación para los servicios del sistema o para sus servicios. Estas propiedades son pares de clave y valor definidos por el usuario que proporcionan metadatos adicionales para un nodo determinado. Algunos ejemplos de propiedades de nodo son si el nodo tiene una unidad de disco duro o una tarjeta gráfica, el número de ejes de la unidad de disco duro, los núcleos y otras propiedades físicas. <br> **Capacities** : las capacidades del nodo definen el nombre y la cantidad de un recurso concreto que un nodo tiene disponible para consumir. Por ejemplo, un nodo puede definir que tenga capacidad para una métrica llamada "MemoryInMb" y 2048 MB de memoria disponible de forma predeterminada. Estas funcionalidades se usan en tiempo de ejecución para garantizar que los servicios que requieren una cantidad determinada de recursos se coloquen en nodos donde esos recursos estén disponibles en la cantidad necesaria.<br>**IsPrimary** : si hay varias opciones NodeType definidas, asegúrese de establecer solo una como principal con el valor *true*; es donde se ejecutan los servicios del sistema. Todos los demás tipos de nodo se deben establecer en *false* |
| **Nodos** |Se trata de los detalles de cada uno de los nodos que forman parte del clúster (tipo de nodo, nombre de nodo, dirección IP, dominio de error y dominio de actualización del nodo). Las máquinas en las que desee que se cree el clúster en función de las necesidades se mostrarán aquí con sus direcciones IP. <br> Si usa la misma dirección IP para todos los nodos, se crea un clúster one-box, que puede usar con fines de prueba. No use clústeres one-box para implementar cargas de trabajo de producción. |

### <a name="step-2-run-the-testconfiguration-script"></a>Paso 2: Ejecutar el script TestConfiguration
El script TestConfiguration prueba la infraestructura definida en cluster.json para asegurarse de que se han asignado los permisos necesarios, de que las máquinas están conectadas entre sí y de que se han definido otros atributos, para que la implementación se realice correctamente. Básicamente se trata de un pequeño analizador de procedimientos recomendados. Se seguirán agregando más validaciones a esta herramienta con el tiempo para que sea más eficaz.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

### <a name="step-3-run-the-create-cluster-script"></a>Paso 3: Ejecutar el script de creación de clúster
Cuando haya modificado la configuración del clúster en el documento JSON y haya agregado la información de los nodos, ejecute el script de PowerShell de creación de clústeres *CreateServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON. Cuando haya finalizado, acepte los términos de licencia.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

> [!NOTE]
> Los registros de implementación están disponibles de forma local en la máquina o máquina virtual donde ejecutó el comando de PowerShell CreateServiceFabricCluster. Los encontrará en una subcarpeta denominada "DeploymentTraces" dentro de la carpeta donde ejecutó el comando de PowerShell. Para ver si se implementó Service Fabric correctamente en una máquina, busque los archivos instalados en el directorio C:\ProgramData; los procesos FabricHost.exe y Fabric.exe se pueden ver en ejecución en el Administrador de tareas.
> 
> 

### <a name="step-4-connect-to-the-cluster"></a>Paso 4: Conectarse al clúster
Para conectarse a un clúster seguro, vea [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md) en Service Fabric.

Para conectarse a un clúster no seguro, ejecute el siguiente comando de PowerShell:

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>Paso 5: Abrir Service Fabric Explorer
Ahora puede conectarse al clúster con Service Fabric Explorer directamente desde una de las máquinas con http://localhost:19080/Explorer/index.html o de forma remota con http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Agregar y quitar nodos
Puede agregar o quitar nodos del clúster de Service Fabric independiente a medida que cambien las necesidades empresariales. Lea [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md) para obtener pasos detallados.

## <a name="remove-a-cluster"></a>Eliminación de un clúster
Para quitar un clúster, ejecute el script de PowerShell *RemoveServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON. También puede especificar una ubicación para el registro que se va a eliminar.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
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

<a id="previewfeatures"></a>

## <a name="preview-features-included-in-this-package"></a>Características de versión preliminar incluidas en este paquete
Ninguno.


> [!NOTE]
> Con la nueva [versión de GA del clúster independiente para Windows Server (versión 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), puede actualizar el clúster a versiones futuras, manual o automáticamente. Consulte el documento [Actualización del clúster de Service Fabric independiente en Windows Server](service-fabric-cluster-upgrade-windows-server.md) para obtener más información.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)
* [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Actualización de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-upgrade-windows-server.md)
* [Creación de un clúster de Service Fabric independiente con máquinas virtuales de Azure con Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
* [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png



<!--HONumber=Dec16_HO2-->


