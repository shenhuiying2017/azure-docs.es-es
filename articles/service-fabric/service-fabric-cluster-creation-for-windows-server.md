<properties
   pageTitle="Creación y administración de un clúster de Service Fabric de Azure independiente | Microsoft Azure"
   description="Aprenda a crear y administrar un clúster de Service Fabric de Azure en cualquier máquina (física o virtual) que ejecute Windows Server, ya sea local o en una nube."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>


# Creación y administración de un clúster que se ejecute en Windows Server

Service Fabric de Azure permite crear clústeres de Service Fabric en las máquinas virtuales o los equipos que ejecutan Windows Server. Es decir, podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos con Windows Server que estén conectados entre sí, ya sea de manera local o con algún proveedor de servicios en la nube. Service Fabric proporciona un paquete de instalación para crear clústeres de Service Fabric, llamado paquete independiente de Windows Server.

Este artículo lo guía por los pasos para crear un clúster localmente mediante el paquete independiente para Service Fabric, aunque puede adaptarse con facilidad a cualquier otro entorno, por ejemplo, otros proveedores de nube.

>[AZURE.NOTE] Este paquete de Windows Server independiente puede contener características que están actualmente en versión preliminar y no se admiten para uso comercial. Para ver la lista de características que se encuentran en versión preliminar, desplácese hasta el final de este documento. [Haga clic aquí](http://go.microsoft.com/fwlink/?LinkID=733084) si desea descargar ahora una copia del CLUF.


<a id="getsupport"></a>
## Obtención de soporte técnico para el paquete independiente de Service Fabric

- Pregunte a la comunidad sobre el paquete independiente de Service Fabric para Windows Server en el [foro de Azure Service Fabric.](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=AzureServiceFabric?)

- Abra una incidencia para obtener [soporte técnico profesional para Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146). Obtenga más información sobre el [soporte técnico profesional de Microsoft](https://support.microsoft.com/es-ES/gp/offerprophone?wa=wsignin1.0).

<a id="downloadpackage"></a>
## Descarga del paquete independiente de Service Fabric


[Descargue el paquete independiente para Service Fabric de Windows Server 2012 R2 y versiones posteriores](http://go.microsoft.com/fwlink/?LinkId=730690), llamado *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;versión&gt;.zip*.


En el paquete de descarga, encontrará los siguientes archivos:

|**Nombre de archivo**|**Descripción breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|Archivo CAB que contiene los archivos binarios que se implementan en cada máquina del clúster.|
|ClusterConfig.Unsecure.DevCluster.json|Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster de desarrollo no seguro con una sola máquina o máquina virtual y tres nodos, incluidos los datos de cada nodo del clúster. |
|ClusterConfig.Unsecure.MultiMachine.json|Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster no seguro con varias máquinas virtuales o máquinas, incluida la información para cada máquina del clúster.|
|ClusterConfig.Windows.DevCluster.json|Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster de desarrollo seguro con una sola máquina o máquina virtual y tres nodos, incluida la información para cada nodo del clúster. El clúster se protege mediante [identidades de Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster seguro con varias máquinas virtuales o máquinas y seguridad de Windows, incluida la información para cada máquina del clúster seguro. El clúster se protege mediante [identidades de Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster de desarrollo seguro con una sola máquina o máquina virtual y tres nodos, incluida la información para cada nodo del clúster. El clúster se protege mediante certificados X509.|
|ClusterConfig.x509.MultiMachine.json|Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster seguro con varias máquinas virtuales o máquinas, incluida la información para cada nodo del clúster seguro. El clúster se protege mediante certificados X509.|
|EULA\_ENU.txt|Términos de licencia para usar el paquete independiente de Windows Server para Microsoft Azure Service Fabric. [Haga clic aquí](http://go.microsoft.com/fwlink/?LinkID=733084) si desea descargar ahora una copia del CLUF.|
|Readme.txt|Vínculo a las notas de la versión e instrucciones de instalación básica. Se trata de un subconjunto de las instrucciones que encontrará en esta página.|
|CreateServiceFabricCluster.ps1|Script de PowerShell que crea el clúster mediante la configuración en el archivo ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script de PowerShell que quita un clúster mediante la configuración en el archivo ClusterConfig.json.|
|ThirdPartyNotice.rtf |Aviso de software de terceros que está en el paquete.|
|AddNode.ps1|Script de PowerShell para agregar un nodo a un clúster implementado existente.|
|RemoveNode.ps1|Script de PowerShell para quitar un nodo de un clúster implementado existente.|
|CleanFabric.ps1|Script de PowerShell para eliminar una instalación independiente de Service Fabric de la máquina actual. Las instalaciones anteriores de MSI se deben quitar mediante su propio desinstalador asociado.|
|TestConfiguration.ps1|Script de PowerShell para analizar la infraestructura como se especifica en cluster.JSON.|


## Planeamiento y preparación para la implementación del clúster
Realice los pasos siguientes antes de crear el clúster.

### Paso 1: Planear la infraestructura de clúster
Va a crear un clúster de Service Fabric en máquinas que posee, por lo que podrá decidir qué tipos de errores desea que resista el clúster. Por ejemplo, ¿necesita separar redes eléctricas o conexiones a Internet que proporcionan estas máquinas? Además, debe tener en cuenta la seguridad física de estas máquinas. ¿Dónde se encuentra la ubicación física y quién tiene acceso a ella? Cuando haya tomado estas decisiones, puede asignar las máquinas de manera lógica a los distintos dominios de error (desplácese hacia abajo para más información). El planeamiento de la infraestructura para clústeres de producción es más complicado que el de los clústeres de prueba.

<a id="preparemachines"></a>
### Paso 2: Preparar las máquinas para cumplir los requisitos previos
Requisitos previos para cada máquina que desee agregar al clúster:

- Es recomendable disponer de 16 GB de RAM como mínimo
- Se recomiendan 40 GB de espacio de disco disponible como mínimo
- Es recomendable disponer de una CPU de 4 núcleos como mínimo
- Conectividad de red: asegúrese de que las máquinas se encuentren en redes seguras.
- Windows Server 2012 R2 o Windows Server 2012 (debe tener instalado [KB2858668](https://support.microsoft.com/kb/2858668)).
- [.NET Framework 4.5.1 o posterior](https://www.microsoft.com/download/details.aspx?id=40773) (instalación completa).
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- El administrador de clúster que implemente y configure el clúster debe tener [privilegios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) en cada una de las máquinas.
- El [servicio RemoteRegistry](https://technet.microsoft.com/library/cc754820) se debe estar ejecutando en todas las máquinas.
- No se puede instalar Service Fabric en un controlador de dominio

### Paso 3: Determinar el tamaño inicial del clúster
Cada nodo de un clúster de Service Fabric independiente tiene el sistema en tiempo de ejecución de Service Fabric implementado y es miembro del clúster. En una implementación típica de producción, hay un nodo por cada instancia de sistema operativo (física o virtual). El tamaño del clúster depende de sus necesidades empresariales; sin embargo, debe tener un tamaño mínimo de tres nodos (máquinas y VM). Con fines de desarrollo, puede tener más de un nodo en una máquina determinada. En un entorno de producción, Service Fabric solo admite un nodo por máquina virtual o física.

### Paso 4: Determinar el número de dominios de error y de actualización
Un **dominio de error (FD)** es una unidad física de error, y está directamente relacionado con la infraestructura física de los centros de datos. Un dominio de error consta de componentes de hardware (equipos, conmutadores, red, etc.) que comparten un único punto de error. Aunque no hay ninguna asignación 1:1 entre dominios de error y racks, en términos generales, cada bastidor puede considerarse un dominio de error. A la hora de valorar los nodos del clúster, se recomienda que los nodos se distribuyan entre tres dominios de error como mínimo.

Si especifica dominios de error en *ClusterConfig.json*, puede elegir el nombre de cada uno. Service Fabric admite FD jerárquicos, así que puede reflejar la topología de infraestructura en ellos. Por ejemplo, los siguientes FD son válidos:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **dominio de actualización (UD)** es una unidad lógica de nodos. Durante una actualización de Service Fabric organizada (una actualización de la aplicación o del clúster), todos los nodos de un UD se eliminan para llevar a cabo este proceso, mientras que los nodos de otros UD siguen estando disponibles para atender las solicitudes. Las actualizaciones de firmware que realice en las máquinas no respetarán los UD, así que debe realizar este proceso de forma individual.

La manera más sencilla de pensar en estos conceptos es considerar los FD como la unidad de error no planeada, y los UD, como la unidad de mantenimiento planeado.

Si especifica dominios de actualización en *ClusterConfig.json*, puede elegir el nombre de cada uno. Por ejemplo, los siguientes son válidos:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Para más información sobre los dominios de actualización y los dominios de error, lea el artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Paso 5: Descarga del paquete independiente de Windows Server para Service Fabric
[Descargue el paquete independiente de Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en una máquina de implementación que no forme parte del clúster, o bien en una de las máquinas que formarán parte del clúster.

<a id="createcluster"></a>
## Creación del clúster

Después de haber realizado los pasos descritos en la sección de planeación y preparación anterior, ya puede crear el clúster.

### Paso 1: Modificar la configuración de clúster
El clúster se describe en un archivo *ClusterConfig.json*. Para ver detalles sobre las secciones de este archivo, lea el artículo [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md). Abra uno de los archivos *ClusterConfig.json* del paquete descargado y modifique las siguientes opciones:

|**Opciones de configuración.**|**Descripción**|
|-----------------------|--------------------------|
|**NodeTypes**|Los tipos de nodo permiten separar los nodos de clúster en grupos distintos. Un clúster debe tener al menos un tipo NodeType. Todos los nodos de un grupo tienen las siguientes características comunes: <br> **nombre**: es el nombre del tipo de nodo. <br>**Endpoint Ports**: diversos puntos de conexión (puertos) con nombre asociados a este tipo de nodo. Puede usar cualquier número de puerto que desee, siempre que no entren en conflicto con nada de este manifiesto y que no estén siendo usados por otra aplicación que se ejecute en el equipo o en la máquina virtual. <br> **Propiedades de ubicación**: describen las propiedades de este tipo de nodo que se usarán como restricciones de ubicación para los servicios del sistema o para sus servicios. Estas propiedades son pares de clave y valor definidos por el usuario que proporcionan metadatos adicionales para un nodo determinado. Algunos ejemplos de propiedades de nodo son si el nodo tiene una unidad de disco duro o una tarjeta gráfica, el número de ejes de la unidad de disco duro, los núcleos y otras propiedades físicas. <br> **Capacities**: las capacidades del nodo definen el nombre y la cantidad de un recurso concreto que un nodo tiene disponible para consumir. Por ejemplo, un nodo puede definir que tenga capacidad para una métrica llamada "MemoryInMb" y 2048 MB de memoria disponible de forma predeterminada. Estas funcionalidades se usan en tiempo de ejecución para garantizar que los servicios que requieren una cantidad determinada de recursos se coloquen en nodos donde esos recursos estén disponibles en la cantidad necesaria.<br>**IsPrimary**: si hay varias opciones NodeType definidas, asegúrese de establecer solo una como principal con el valor *true*; es donde se ejecutan los servicios del sistema. Todos los demás tipos de nodo se deben establecer en *false*.|
|**Nodos**|Se trata de los detalles de cada uno de los nodos que forman parte del clúster (tipo de nodo, nombre de nodo, dirección IP, dominio de error y dominio de actualización del nodo). Las máquinas en las que desee que se cree el clúster en función de las necesidades se mostrarán aquí con sus direcciones IP. <br> Si usa la misma dirección IP para todos los nodos, se crea un clúster one-box, que puede usar con fines de prueba. No use clústeres one-box para implementar cargas de trabajo de producción.|

### Paso 5: Ejecutar el script TestConfiguration

Este script permite comprobar la infraestructura definida en cluster.JSON para asegurarse de que existen los permisos necesarios, que las máquinas están conectadas entre sí etc., para que la implementación se realice correctamente. Básicamente se trata de un pequeño analizador de procedimientos recomendados. Se seguirán agregando más validaciones a esta herramienta con el tiempo para que sea más eficaz.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script puede o no formar parte del clúster.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer.5.3.202.9494> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer.5.3.202.9494\DeploymentTraces
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

### Paso 3: Ejecutar el script de creación de clúster
Cuando haya modificado la configuración del clúster en el documento JSON y haya agregado la información de los nodos, ejecute el script de PowerShell de creación de clústeres *CreateServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON y la ubicación del archivo CAB del paquete.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script puede o no formar parte del clúster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] Los registros de implementación están disponibles de forma local en la máquina o máquina virtual donde ejecutó el comando de PowerShell CreateServiceFabricCluster. Los encontrará en una subcarpeta denominada "DeploymentTraces" dentro de la carpeta donde ejecutó el comando de PowerShell. Además, para ver si se implementó Service Fabric correctamente en una máquina, busque los archivos instalados en el directorio C:\\ProgramData; los procesos FabricHost.exe y Fabric.exe se pueden ver en ejecución en el Administrador de tareas.

### Paso 4: Conectarse al clúster

Consulte [este documento](service-fabric-connect-to-secure-cluster.md) para obtener instrucciones para conectarse a un clúster seguro.

Para conectarse a un clúster no seguro, ejecute el siguiente comando de PowerShell.

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### Paso 5: Abrir Service Fabric Explorer

Ahora puede conectarse al clúster con Service Fabric Explorer directamente desde una de las máquinas con http://localhost:19080/Explorer/index.html o de forma remota con http://<*direcciónIPDeUnaMáquina*>:19080/Explorer/index.html.



## Incorporación y eliminación de nodos del clúster

Puede agregar o quitar nodos del clúster de Service Fabric independiente a medida que cambien las necesidades empresariales. Lea [Add or Remove nodes to a Service Fabric standalone cluster](service-fabric-cluster-windows-server-add-remove-nodes.md) (Incorporación o eliminación de nodos de un clúster de Service Fabric independiente) para obtener pasos detallados.


## Eliminación de su clúster

Para quitar un clúster, ejecute el script de PowerShell *RemoveServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON; opcionalmente, especifique una ubicación para el registro de la eliminación.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script puede o no formar parte del clúster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```

<a id="telemetry"></a>
## Datos de telemetría recopilados y cómo omitirlos

De forma predeterminada, el producto recopila datos de telemetría sobre el uso de Service Fabric para mejorar el producto. El analizador de procedimientos recomendados que se ejecuta como parte de la instalación, comprueba la conectividad a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Si no está accesible, el programa de instalación generará un error, a menos que decida no participar en la telemetría.

1) La canalización de telemetría intenta cargar los datos siguientes en [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) una vez al día. Es una carga de mejor esfuerzo y no tiene ningún impacto en la funcionalidad del clúster. Solo se envía la telemetría desde el nodo que ejecuta el administrador de conmutación por error principal. Ningún otro nodo envía telemetría.

2) La telemetría consta de lo siguiente:

1.            Número de servicios,
1.            Número de ServiceTypes
1.            Número de aplicaciones
1.            Número de ApplicationUpgrades
1.            Número de FailoverUnits
1.            Número de InBuildFailoverUnits
1.            Número de UnhealthyFailoverUnits
1.            Número de réplicas
1.            Número de InBuildReplicas
1.            Número de StandByReplicas
1.            Número de OfflineReplicas
1.            CommonQueueLength
1.            QueryQueueLength
1.            FailoverUnitQueueLength
1.            CommitQueueLength
1.            Número de nodos
1.            IsContextComplete: True/False
1.            ClusterId: se trata de un GUID generado aleatoriamente para cada clúster
1.            ServiceFabricVersion
1.             Dirección IP de la máquina virtual o la máquina desde la que se carga la telemetría


Para deshabilitar la telemetría, agregue lo siguiente al elemento "properties" en la configuración del clúster: "enableTelemetry": false

<a id="previewfeatures"></a>
## Características de versión preliminar incluidas en este paquete

Ninguno.

## Pasos siguientes
- [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)
- [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Creación de un clúster de Service Fabric independiente con máquinas virtuales de Azure con Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
- [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)


<!--Image references-->
[TrustedZone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

<!---HONumber=AcomDC_0928_2016-->