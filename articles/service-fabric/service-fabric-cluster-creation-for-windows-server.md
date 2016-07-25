<properties
   pageTitle="Creación de un clúster de Service Fabric de Azure local o con cualquier proveedor de servicios en la nube | Microsoft Azure"
   description="Aprenda a crear un clúster de Service Fabric de Azure en cualquier máquina (física o virtual) que ejecute Windows Server, ya sea local o en cualquier nube."
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
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# Creación de un clúster que se ejecute en Windows Server

Service Fabric de Azure permite crear clústeres de Service Fabric en las máquinas virtuales o los equipos que ejecutan Windows Server. Es decir, podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos con Windows Server o Linux que estén conectados entre sí, ya sea de manera local o con algún proveedor de servicios en la nube. Service Fabric proporciona un paquete de instalación para crear clústeres de Service Fabric, llamado paquete independiente de Windows Server.

Este artículo lo guía por los pasos para crear un clúster localmente mediante el paquete independiente para Service Fabric, aunque puede adaptarse con facilidad a cualquier otro entorno, por ejemplo, otros proveedores de nube.

>[AZURE.NOTE] Este paquete de independiente de Windows Server se encuentra actualmente en versión preliminar y no se admite para cargas de trabajo de producción. [Haga clic aquí](http://go.microsoft.com/fwlink/?LinkID=733084) si desea descargar ahora una copia del CLUF.

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
|EULA.txt|Términos de licencia para usar el paquete independiente de Windows Server para Microsoft Azure Service Fabric. [Haga clic aquí](http://go.microsoft.com/fwlink/?LinkID=733084) si desea descargar ahora una copia del CLUF.|
|Readme.txt|Vínculo a las notas de la versión e instrucciones de instalación básica. Se trata de un subconjunto de las instrucciones que encontrará en esta página.|
|CreateServiceFabricCluster.ps1|Script de PowerShell que crea el clúster mediante la configuración en el archivo ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script de PowerShell que quita un clúster mediante la configuración en el archivo ClusterConfig.json.|
|AddNode.ps1|Script de PowerShell para agregar un nodo a un clúster implementado existente.|
|RemoveNode.ps1|Script de PowerShell para quitar un nodo de un clúster implementado existente.|


## Planeamiento y preparación para la implementación del clúster
Deben realizarse los siguientes pasos antes de crear el clúster.

### Paso 1: Planear la infraestructura de clúster
Va a crear un clúster de Service Fabric en máquinas que posee, por lo que podrá decidir qué tipos de errores desea que resista el clúster. Por ejemplo, ¿necesita separar redes eléctricas o conexiones a Internet que proporcionan estas máquinas? Además, debe tener en cuenta la seguridad física de estas máquinas. ¿Dónde se encuentra la ubicación física y quién tiene acceso a ella? Una vez que haya tomado estas decisiones, asigne las máquinas de manera lógica a los distintos dominios de error (consúltese a continuación para obtener más información). El planeamiento de la infraestructura para clústeres de producción será más complicado que el de los clústeres de prueba.

### Paso 2: Preparar las máquinas para cumplir los requisitos previos
Requisitos previos para cada equipo que desee agregar al clúster:

- Se recomienda como mínimo de 2 GB de memoria.
- Conectividad de red. Asegúrese de que las máquinas se encuentren en una o varias redes seguras.
- Windows Server 2012 R2 o Windows Server 2012 (debe tener instalado KB2858668).
- .NET framework 4.5.1 o posterior (instalación completa).
- Windows PowerShell 3.0.
- El administrador de clúster que implementa y configura el clúster debe poseer privilegios de administrador en cada una de las máquinas.
- El servicio RemoteRegistry se debe estar ejecutando en todas las máquinas.

### Paso 3: Determinar el tamaño inicial del clúster
Cada nodo tiene el sistema en tiempo de ejecución de Service Fabric implementado y es miembro del clúster. En una implementación típica de producción, hay un nodo por cada instancia de sistema operativo (física o virtual). El tamaño del clúster depende de sus necesidades empresariales; sin embargo, debe tener un tamaño mínimo de tres nodos (máquinas y VM). Tenga en cuenta que, para fines de desarrollo, puede tener más de un nodo en una máquina determinada. En un entorno de producción, Service Fabric solo admite un nodo por máquina virtual o física.

### Paso 4: Determinar el número de dominios de error y de actualización
Un **dominio de error (FD)** es una unidad física de error y está directamente relacionado con la infraestructura física de los centros de datos. Un dominio de error consta de componentes de hardware (equipos, conmutadores, red, etc.) que comparten un único punto de error. Aunque no hay ninguna asignación 1:1 entre dominios de error y racks, en términos generales, cada bastidor puede considerarse un dominio de error. Al tener en cuenta los nodos del clúster, se recomienda encarecidamente que los nodos se distribuyan entre, al menos, 3 dominios de error.

Al especificar dominios de error en *ClusterConfig.json*, puede elegir el nombre de cada uno. Service Fabric admite FD jerárquicos, así que puede reflejar la topología de infraestructura en ellos. Por ejemplo, los siguientes FD son válidos:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **dominio de actualización (UD)** es una unidad lógica de nodos. Durante una actualización de Service Fabric organizada (una actualización de la aplicación o del clúster), todos los nodos de un UD se eliminan para llevar a cabo este proceso, mientras que los nodos de otros UD siguen estando disponibles para atender las solicitudes. Las actualizaciones de firmware que realice en las máquinas no respetarán los UD, así que debe realizar este proceso de forma individual.

La manera más sencilla de pensar en estos conceptos es considerar los FD como la unidad de error no planeada, y los UD, como la unidad de mantenimiento planeado.

Al especificar dominios de actualización en *ClusterConfig.json*, puede elegir el nombre de cada uno. Por ejemplo, los siguientes son válidos:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Para más información sobre los dominios de actualización y los de error, lea el artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Paso 5: Descarga del paquete independiente de Windows Server para Service Fabric
[Descargue el paquete independiente de Windows Server para Service Fabric](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en una máquina de implementación que no forme parte del clúster, o bien en una de las máquinas que formarán parte del clúster.

<a id="createcluster"></a>
## Creación del clúster

Después de haber realizado los pasos descritos en la sección de planeación y preparación anterior, ya podrá crear el clúster.

### Paso 1: Modificar la configuración de clúster
El clúster se describe en un archivo *ClusterConfig.json*. Para ver detalles sobre las secciones de este archivo, lea el artículo [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md). Abra uno de los archivos *ClusterConfig.json* del paquete descargado y modifique las siguientes opciones:

|**Opciones de configuración.**|**Descripción**|
|-----------------------|--------------------------|
|**NodeTypes**|Los tipos de nodo permiten separar los nodos de clúster en grupos distintos. Un clúster debe tener al menos un tipo NodeType. Todos los nodos de un grupo tienen las siguientes características comunes. <br> **Name**: se trata del nombre del tipo de nodo. <br>**Endpoints Ports**: son diversos puntos de conexión con nombre (puertos) asociados a este tipo de nodo. Puede usar cualquier número de puerto que desee, siempre que no entre en conflicto con ningún otro elemento de este manifiesto y no lo esté utilizando otra aplicación que se ejecute en la máquina o la máquina virtual. <br> **Placement Properties**: describen propiedades para este tipo de nodo que puede usar como restricciones de posición para los servicios del sistema o los suyos. Estas propiedades son pares de clave/valor definidos por el usuario que proporcionan metadatos adicionales para un nodo determinado. Algunos ejemplos de propiedades de nodo son si el nodo tiene una unidad de disco duro o una tarjeta gráfica, el número de ejes de la unidad de disco duro, los núcleos y otras propiedades físicas. <br> **Capacities**: las funcionalidades de nodo definen el nombre y la cantidad de un recurso concreto que un nodo específico tiene disponible para consumir. Por ejemplo, un nodo puede definir que tenga capacidad para una métrica llamada "MemoryInMb" y 2048 MB de memoria disponible de forma predeterminada. Estas funcionalidades se usan en tiempo de ejecución para garantizar que los servicios que requieren una cantidad determinada de recursos se coloquen en nodos donde esos recursos sigan disponibles.<br>**IsPrimary**: si hay varias opciones NodeType definidas, asegúrese de que solo se establezca una como principal con el valor *true*, que es donde se ejecutan los servicios del sistema. Todos los demás tipos de nodo se deben establecer en el valor *false*.|
|**Nodos**|Se trata de los detalles de cada uno de los nodos que forman parte del clúster (tipo de nodo, nombre de nodo, dirección IP, dominio de error y dominio de actualización del nodo). Las máquinas en las que desee que se cree el clúster en función de las necesidades se mostrarán aquí con sus direcciones IP. <br> Si usa las mismas direcciones IP para todos los nodos, se crea un clúster one-box, que puede usar con fines de prueba. Los clústeres one-box no deben usarse para implementar cargas de trabajo de producción.|

### Paso 2: Ejecutar el script de creación de clúster
Una vez que haya modificado la configuración del clúster en el documento JSON y haya agregado la información de los nodos, ejecute el script de PowerShell de creación de clústeres *CreateServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON y la ubicación del archivo CAB del paquete.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script puede o no formar parte del clúster.

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] Los registros de implementación están disponibles de forma local en la máquina o máquina virtual donde ejecutó el comando de PowerShell CreateServiceFabricCluster. Los encontrará en una subcarpeta denominada "DeploymentTraces" dentro de la carpeta donde ejecutó el comando de PowerShell. Además, para ver si se implementó Service Fabric correctamente en una máquina, busque los archivos instalados en el directorio C:\\ProgramData; los procesos FabricHost.exe y Fabric.exe se pueden ver en ejecución en el Administrador de tareas.

### Paso 3: Conexión al clúster
Ahora puede conectarse al clúster con Service Fabric Explorer directamente desde una de las máquinas con http://localhost:19080/Explorer/index.html o de forma remota con http://<*direcciónIPDeUnaMáquina*>:19080/Explorer/index.html.

## Incorporación de nodos al clúster

1. Prepare la máquina o máquina virtual que desee agregar al clúster (consulte el paso 2 de la sección anterior, Planeamiento y preparación para la implementación del clúster).
2. Planee a qué dominio de error y de actualización va a agregar esta máquina o máquina virtual.
3. Copie o [descargue el paquete independiente de Windows Server para Service Fabric](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en la máquina o máquina virtual que planea agregar al clúster.
4. Abra un símbolo del sistema de administración de PowerShell y acceda a la ubicación del paquete descomprimido.
5. Ejecute el script de PowerShell *AddNode.ps1* con los parámetros que describen el nuevo nodo que se va a agregar. En el ejemplo siguiente se agrega un nuevo nodo denominado VM5, con el tipo NodeType0 y la dirección IP 182.17.34.52, en UD1 y FD1. *ExistingClusterConnectionEndPoint* es un punto de conexión para un nodo que ya está presente en el clúster existente. Puede elegir *cualquier* dirección IP de nodo para esto en el clúster.

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## Eliminación de nodos del clúster

1. Abra una conexión de Escritorio remoto (RDP) en la máquina o máquina virtual que desea quitar del clúster.
2. Copie o [descargue el paquete independiente de Windows Server para Service Fabric](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en la máquina o máquina virtual que planea agregar al clúster.
3. Abra un símbolo del sistema de administración de PowerShell y acceda a la ubicación del paquete descomprimido.
4. Ejecute el script de PowerShell *RemoveNode.ps1*. En el ejemplo siguiente se quita el nodo actual del clúster. *ExistingClusterConnectionEndPoint* es un punto de conexión para un nodo que ya está presente en el clúster existente. Puede elegir *cualquier* dirección IP de nodo para esto en el clúster.

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## Eliminación de su clúster
Para quitar un clúster, ejecute el script de PowerShell *RemoveServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON y la ubicación del archivo CAB del paquete.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script puede o no formar parte del clúster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## Cómo crear un clúster de tres nodos con máquinas virtuales IaaS de Azure
En los pasos siguientes, se describe cómo crear un clúster en máquinas virtuales IaaS de Azure mediante el instalador independiente de Windows Server. Tenga en cuenta que la administración del sistema en tiempo de ejecución de Service Fabric en este clúster IaaS recae enteramente en usted, a diferencia de los clústeres creados mediante el Portal de Azure que se actualizan con el proveedor de recursos de Service Fabric.

1. Inicie sesión en el Portal de Azure y cree una máquina virtual Windows Server 2012 R2 Datacenter en un grupo de recursos.
2. Agregue dos máquinas virtuales Windows Server 2012 R2 Datacenter más al mismo grupo de recursos. Asegúrese de que cada una ellas tenga el mismo nombre de usuario administrador y contraseña que cuando se creó. Una vez creada, verá las tres máquinas virtuales en la misma red virtual.
3. Conéctese a cada una de ellas y desactive el Firewall de Windows mediante el Administrador del servidor, panel Servidor local. Esto garantiza que el tráfico de red pueda comunicarse entre las máquinas. En cada máquina, obtenga la dirección IP abriendo un símbolo del sistema y escribiendo *ipconfig*. También puede ver la dirección IP de cada máquina si selecciona el recurso de red virtual para el grupo de recursos en el Portal de Azure.
4. Conéctese a una de las máquinas y pruebe si puede hacer ping correctamente a las otras dos.
5. Conéctese a una de las máquinas virtuales, descargue el paquete independiente de Windows Server a una carpeta nueva en la máquina y descomprímalo.
6. Abra el archivo *ClusterConfig.Unsecure.MultiMachine.json* en el Bloc de notas y edite cada nodo con las tres direcciones IP de las máquinas, cambie el nombre del clúster al principio y guarde el archivo. A continuación, se muestra un ejemplo parcial del manifiesto del clúster, con las direcciones IP de cada máquina.

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

7. Abra una ventana de PowerShell ISE y vaya a la carpeta donde descargó y descomprimió el paquete del instalador independiente y guardó el archivo de manifiesto anterior. Ejecute el siguiente comando de PowerShell.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Debería ver que se ejecuta el comando de PowerShell, se conecta a cada máquina y crea un clúster. Después de aproximadamente 1 minuto, puede probar si el clúster está operativo conectándose a Service Fabric Explorer en una de las direcciones IP de máquina, como http://10.7.0.5:19080/Explorer/index.html. Dado que se trata de un clúster independiente en máquinas virtuales IaaS, si desea protegerlo, debe implementar certificados en las máquinas virtuales o configurar una de ellas como controlador de Windows Server Active Directory (AD) para la autenticación de Windows, al igual que haría localmente. Consulte Pasos siguientes para configurar clústeres seguros.

## Pasos siguientes
- [Creación de clústeres independientes de Service Fabric en Windows Server o Linux](service-fabric-deploy-anywhere.md)
- [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md)

- [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
- [Protección de un clúster de Windows independiente mediante certificados](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->