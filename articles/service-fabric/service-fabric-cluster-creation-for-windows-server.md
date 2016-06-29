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
   ms.date="06/14/2016"
   ms.author="chackdan"/>


# Creación de un clúster de Service Fabric de Azure local o en la nube

Service Fabric de Azure permite crear clústeres de Service Fabric en las máquinas virtuales o los equipos que ejecutan Windows Server. Es decir, podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos con Windows Server o Linux que estén conectados entre sí, ya sea de manera local o con algún proveedor de servicios en la nube. Service Fabric ofrece un paquete de instalación para crear clústeres de Service Fabric.

Este artículo le guiará por los pasos para crear un clúster mediante el paquete independiente de Service Fabric local, aunque puede adaptarse fácilmente a cualquier otro entorno, por ejemplo, otras nubes.

>[AZURE.NOTE] Esta oferta independiente está actualmente en vista previa. [Haga clic aquí](http://go.microsoft.com/fwlink/?LinkID=733084) si desea descargar ahora una copia del CLUF.

<a id="downloadpackage"></a>
## Descarga del paquete independiente de Service Fabric


[Descargue el paquete independiente para Service Fabric para Windows Server 2012 R2](http://go.microsoft.com/fwlink/?LinkId=730690), que se denomina *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

En el paquete de descarga, encontrará los siguientes archivos:

|**Nombre de archivo**|**Descripción breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|Archivo CAB que contiene los archivos binarios que se implementarán en cada equipo del clúster.|
|ClusterConfig.JSON|Archivo de configuración del clúster que contiene toda la configuración de clúster, incluidos los datos de cada máquina que forme parte del clúster.|
|EULA.txt|Términos de licencia del uso del paquete independiente de Microsoft Azure Service Fabric. [Haga clic aquí](http://go.microsoft.com/fwlink/?LinkID=733084) si desea descargar ahora una copia del CLUF.|
|Readme.txt|Vínculo a las notas de la versión e instrucciones de instalación básica. Se trata de un pequeño subconjunto de las instrucciones que encontrará en esta página.|
|CreateServiceFabricCluster.ps1|Script de PowerShell que crea el clúster mediante la configuración en el archivo ClusterConfig.JSON.|
|RemoveServiceFabricCluster.ps1|Script de PowerShell para quitar los clústeres con la configuración de ClusterConfig.JSON.|

## Planeación y preparación para la implementación de clústeres
Deben realizarse los siguientes pasos antes de crear el clúster.

### Paso 1: Planear la infraestructura de clúster
Va a crear un clúster de Service Fabric en las máquinas que posee, por lo que podrá decidir en qué tipos de errores desea que resista el clúster. Por ejemplo, ¿necesita separar redes eléctricas o conexiones a Internet que proporcionan estas máquinas? Además, debe tener en cuenta la seguridad física de estas máquinas. ¿Dónde se encuentra la ubicación física y quién tiene acceso a ella? Una vez que haya tomado estas decisiones, asigne las máquinas de manera lógica a los distintos dominios de error (consúltese a continuación para obtener más información). La planeación de la infraestructura para clústeres de producción será mucho más complicada que la de los clústeres de prueba.

### Paso 2: Preparar las máquinas para cumplir los requisitos previos
Requisitos previos para cada equipo que desee agregar al clúster:

- Se recomienda como mínimo de 2 GB de memoria.
- Conectividad de red: asegúrese de que las máquinas se encuentren en una o en varias redes seguras.
- Windows Server 2012 R2 o Windows Server 2012 (debe tener instalado KB2858668).
- .NET framework 4.5.1 o posterior (instalación completa).
- Windows PowerShell 3.0.
- El administrador de clústeres que implementa y configura el clúster debe tener privilegios de administrador en cada uno de los equipos.
- El servicio RemoteRegistry se debe estar ejecutando en todos los equipos.

### Paso 3: Determinar el tamaño inicial del clúster
Cada nodo consta de una pila completa de Service Fabric y es un miembro individual del clúster de Service Fabric. En una implementación típica de Service Fabric hay un nodo por cada instancia de sistema operativo (física o virtual). El tamaño del clúster depende de sus necesidades empresariales; sin embargo, debe tener un tamaño mínimo de tres nodos (máquinas y VM). Tenga en cuenta que, para fines de desarrollo, puede tener más de un nodo en una máquina determinada. En un entorno de producción, Service Fabric solo admite un nodo por máquina virtual o física.

### Paso 4: Determinar el número de dominios de error y de actualización
Un **dominio de error (FD)** es una unidad física de error y está directamente relacionada con la infraestructura física de los centros de datos. Un dominio de error consta de componentes de hardware (equipos, conmutadores etc.) que comparten un único punto de error. Aunque no hay ninguna asignación 1:1 entre dominios de error y racks, en términos generales, cada bastidor puede considerarse un dominio de error. Al tener en cuenta los nodos del clúster, se recomienda encarecidamente que los nodos se distribuyan entre, al menos, 3 dominios de error.

Cuando especifique FD en *ClusterConfig.JSON*, elija el nombre del FD. Service Fabric admite FD jerárquicos, así que puede reflejar la topología de infraestructura en ellos. Por ejemplo, se permiten o siguientes

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **dominio de actualización (UD)** es una unidad lógica de nodos. Durante una actualización de Service Fabric organizada (de la aplicación o del clúster), todos los nodos de un UD se eliminan para llevar a cabo este proceso, mientras que los nodos de otros UD siguen estando disponibles para atender las solicitudes. Las actualizaciones de firmware que realice en las máquinas no respetarán los UD, así que debe realizar este proceso de forma individual.

La manera más sencilla de pensar en estos conceptos es considerar los FD como la unidad de error no planeada, y los UD, como la unidad de mantenimiento planeado.

Al especificar dominios de actualización en *ClusterConfig.JSON*, elija el nombre del UD. Por ejemplo, se permiten todos los siguientes:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

### Paso 5: Descargar el paquete independiente de Service Fabric para Windows Server
[Descargue el paquete independiente de Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en un equipo de implementación que no forme parte del clúster o en una de las máquinas que formará parte del clúster.

<a id="createcluster"></a>
## Creación del clúster

Después de haber realizado los pasos descritos en la sección de planeación y preparación anterior, ya podrá crear el clúster.

### Paso 1: Modificar la configuración de clúster
Abra *ClusterConfig.JSON* desde el paquete descargado. Puede usar cualquier editor que elija y modificar las siguientes opciones:

|**Opciones de configuración.**|**Descripción**|
|-----------------------|--------------------------|
|NodeTypes|Los tipos de nodo permiten separar los nodos de clúster en grupos distintos. Un clúster debe tener al menos un tipo NodeType. Todos los nodos de un grupo tienen las siguientes características comunes. <br> *Name*: se trata del nombre del tipo de nodo. <br>*EndPoints*: se trata de diversos puntos de conexión con nombre (puertos) asociados a este tipo de nodo. Puede usar cualquier número de puerto que desee, siempre que no entre en conflicto con cualquier elemento de este manifiesto y que no lo esté utilizando otro programa de la máquina o la VM. <br> *PlacementProperties*: describen propiedades para este tipo de nodo, que se utilizará como restricciones de posición en los servicios del sistema o los suyos. Estas propiedades son pares de clave/valor definidos por el usuario que proporcionan metadatos adicionales para un nodo determinado. Entre los ejemplos de propiedades de nodos estarían si el nodo tiene una unidad de disco duro o una tarjeta de vídeo, el número de ejes de su unidad de disco duro, núcleos y otras propiedades físicas. <br> *Capacities*: las capacidades de nodo definen el nombre y la cantidad de un recurso concreto que un nodo específico tiene disponible para utilizar. Por ejemplo, un nodo puede definir que tenga capacidad para una métrica llamada "MemoryInMb" y 2048 MB de memoria disponible de forma predeterminada. Estas capacidades se usan en tiempo de ejecución para garantizar que los servicios que requieren una cantidad determinada de recursos se colocan en nodos con esos recursos sigan estando disponibles.|
|Nodos|Se trata de los detalles de cada uno de los nodos que formarán parte del clúster (tipo de nodo, nombre de nodo, dirección IP, dominio de error y dominio de actualización del nodo). Las máquinas que desee que cree el clúster en función de las necesidades se mostrarán aquí con su dirección IP. <br> Si utiliza las mismas direcciones IP para todos los nodos, se creará un clúster one-box, que puede usar con fines de prueba. Los clústeres one-box no deben usarse para implementar cargas de trabajo de producción.|

### Paso 2: Ejecutar el script de creación de clúster
Una vez que se haya modificado la configuración del clúster en el documento JSON y haya agregado la información de los nodos, ejecute el script de creación de clústeres de PowerShell desde la carpeta del paquete, y transfiera la ruta al archivo de configuración y la ubicación de la raíz del paquete.

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script puede o no formar parte del clúster.

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590\ClusterConfig.JSON -MicrosoftServiceFabricCabFilePath C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590\MicrosoftAzureServiceFabric.cab
```

## Pasos siguientes

Después de crear un clúster, asegúrese también de protegerlo:
- [Seguridad de clúster](service-fabric-cluster-security.md)

Lea la información siguiente para empezar a trabajar en el desarrollo o la implementación de aplicaciones:
- [SDK de Service Fabric e introducción](service-fabric-get-started.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).

Obtenga más información sobre los clústeres independientes y los de Azure:
- [Información general de la función de creación de clúster independiente y una comparación con los clústeres administrados de Azure](service-fabric-deploy-anywhere.md)

<!---HONumber=AcomDC_0615_2016-->