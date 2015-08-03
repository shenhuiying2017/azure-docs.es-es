<properties
   pageTitle="Visualización del clúster mediante el Explorador de Service Fabric"
   description="El Explorador de Service Fabric es una útil herramienta de GUI para inspeccionar y administrar aplicaciones y nodos en la nube en un clúster de Service Fabric de Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/20/2015"
   ms.author="jesseb"/>

# Visualización del clúster mediante el Explorador de Service Fabric

El Explorador de Service Fabric es una herramienta visual para inspeccionar y administrar aplicaciones y nodos en la nube en un clúster de Service Fabric de Microsoft Azure. El Explorador de Service Fabric puede conectarse a clústeres de desarrollo local y clústeres de Azure. Para obtener información sobre los cmdlets de PowerShell de Service Fabric, consulte la sección **Pasos siguientes**.

> [AZURE.NOTE]Creación de clústeres de Service Fabric en Azure todavía no está disponible.

## Introducción al Explorador de Service Fabric

Asegúrese de que se configura el entorno de desarrollo local con las instrucciones siguientes en [Configuración del entorno de desarrollo de Service Fabric](service-fabric-get-started.md).

Ejecute el Explorador de Service Fabric desde la ruta de acceso de instalación local (%Archivos de programa%\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe). La herramienta se conectará automáticamente a un clúster de desarrollo local, si existe alguno. Muestra información sobre el clúster como:

- Aplicaciones que se ejecutan en el clúster
- Información acerca de los nodos del clúster
- Eventos de mantenimiento de aplicaciones y nodos
- Carga en las aplicaciones del clúster
- Supervisión del estado de actualización de aplicación

![Representación visual del clúster de Service Fabric y las aplicaciones implementadas][servicefabricexplorer]

Una de las visualizaciones importantes es la asignación de clústeres, visible en el panel para el clúster (por ejemplo, al hacer clic en **Clúster Onebox/Local**). El mapa de clúster muestra el conjunto de dominios de actualización y dominios de error y qué nodos se asignan a los dominios. Consulte la [información general técnica de Service Fabric](service-fabric-technical-overview.md) para familiarizarse con los conceptos clave de Service Fabric.

![El mapa del clúster muestra a qué dominios de actualización y dominios de error pertenece cada nodo.][clustermap]


## Visualización de aplicaciones y servicios

El Explorador Service Fabric le permite explorar las aplicaciones se ejecutan en el clúster. Expanda la **vista de aplicaciones** para ver información detallada de sus réplicas, particiones, servicios y aplicaciones.

El diagrama siguiente muestra que la aplicación denominada **"fabric:/Stateful1Application"** tiene un servicio sin estado denominado **"fabric:/Stateful1Application/MyFrontEnd"** y un servicio con estado denominado **"fabric:/Stateful1Application/Stateful1"**. El servicio sin estado tiene una partición con una réplica que se ejecuta en **Node.4**. El servicio con estado tiene dos particiones, cada una con tres réplicas que se ejecutan en varios nodos diferentes.

![Visualización de las aplicaciones que se ejecutan en el clúster de Service Fabric][applicationview]

Al hacer clic en una aplicación, servicio, partición o réplica, se proporciona información detallada sobre dicha entidad. El diagrama siguiente muestra el panel de mantenimiento de la réplica de servicio para una de las réplicas principales del servicio con estado. Incluye su rol, el nodo en que se está ejecutando, la dirección en la que se está escuchando, la ubicación de los archivos en el disco y los eventos de mantenimiento.

![Información detallada acerca de una réplica de Service Fabric][replicadetails]


## Conexión a un clúster de Service Fabric remoto

Para ver un clúster de Service Fabric remoto, haga clic en **Conectar** para que aparezca el cuadro de diálogo **Conectar al clúster de Service Fabric**. Escriba el **extremo de ServiceFabric** para el clúster y haga clic en **Conectar**. El extremo de Service Fabric suele ser el nombre público de escucha del servicio de clúster en el puerto 19000.

![Configuración de una conexión al clúster de Service Fabric remoto][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

- [Información general de Testability](service-fabric-testability-overview.md).
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Implementación de aplicaciones de Service Fabric con PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
 

<!---HONumber=July15_HO4-->