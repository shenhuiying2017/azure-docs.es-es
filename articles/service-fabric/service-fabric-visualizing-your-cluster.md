<properties
   pageTitle="Visualización del clúster mediante el Explorador de Service Fabric | Microsoft Azure"
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
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Visualización del clúster mediante el Explorador de Service Fabric

El explorador de Service Fabric es una herramienta basada en web para inspeccionar y administrar aplicaciones y nodos en un clúster de Service Fabric. El explorador de Service Fabric se hospeda directamente en el clúster para que siempre esté disponible, con independencia de dónde se ejecuta el clúster.

## Conexión al explorador de Service Fabric

Si ha seguido las instrucciones para [preparar el entorno de desarrollo](service-fabric-get-started.md), puede iniciar el explorador de Service Fabric en el clúster local en http://localhost:19080/Explorer.

>[AZURE.NOTE]Si está usando Internet Explorer(IE) con el explorador de Service Fabric para administrar un clúster remoto, deberá configurar algunas opciones de Internet Explorer. Vaya a **Herramientas -> Configuración de Vista de compatibilidad** y desactive la casilla **Mostrar sitios de la intranet en Vista de compatibilidad** para asegurarse de toda la información se carga correctamente.

## Información sobre el diseño del explorador de Service Fabric

Puede desplazarse al explorador de Service Fabric desde el árbol situado a la izquierda. En la raíz del árbol, el panel del clúster proporciona información general del clúster, incluido un resumen de la aplicación y del estado del nodo.

![Panel de clúster del Explorador de Service Fabric][sfx-cluster-dashboard]

El clúster contiene dos subárboles: uno para las aplicaciones y otro para los nodos.

### Visualización de aplicaciones y servicios

La vista de aplicaciones le permite navegar por la jerarquía lógica de Service Fabric: aplicaciones, servicios, particiones y réplicas.

En el ejemplo siguiente, la aplicación **MyApp** se compone de dos servicios: **MyStatefulService** y **WebSvcService**. Como **MyStatefulService** es un servicio con estado, incluye una partición con una réplica principal y dos réplicas secundarias. Por el contrario, el servicio WebSvcService no tiene estado y contiene una única instancia.

![Vista de aplicación del explorador de Service Fabric][sfx-application-tree]

En cada nivel del árbol, el panel principal muestra la información pertinente sobre el elemento. Por ejemplo, puede ver el estado de mantenimiento y la versión para un servicio determinado.

![Panel Essentials del explorador de Service Fabric][sfx-service-essentials]

### Visualización de los nodos del clúster

La vista Nodos muestra el diseño físico del clúster. En un nodo determinado puede inspeccionar que las aplicaciones tienen implementado el código en ese nodo y más específicamente, qué réplicas se están ejecutando allí actualmente.

## Realización de acciones mediante el explorador de Service Fabric

El explorador de Service Fabric ofrece una forma rápida de invocar acciones en nodos, aplicaciones y servicios dentro del clúster.

Por ejemplo, para eliminar una instancia de la aplicación, solo hay que elegir la aplicación en el árbol de la izquierda y después elegir Acciones > Eliminar aplicación.

![Eliminación de una aplicación en el explorador de Service Fabric][sfx-delete-application]

Como muchas acciones son destructivas, le pediremos que confirme su intención antes de que finalice la acción.

>[AZURE.NOTE]Todas las acciones que pueden realizarse con el explorador de Service Fabric también pueden realizarse con PowerShell o una API de REST, mediante la habilitación de la automatización.



## Conexión a un clúster de Service Fabric remoto

Como el explorador de Service Fabric está basado en web y se ejecuta dentro del clúster, es accesible desde cualquier explorador, siempre que conozca el punto de conexión del clúster y tenga permisos suficientes para tener acceso a él.

### Detección del punto de conexión del explorador de Service Fabric para un clúster remoto

Puede detectar el punto de conexión del clúster desde el portal de Service Fabric. Para acceder al explorador de Service Fabric para un clúster determinado, simplemente conéctese a ese punto de conexión en el puerto 19007:

http://&lt;your-cluster-endpoint&gt;:19007

### Conexión a un clúster seguro

Puede controlar el acceso al clúster de Service Fabric al requerir a los clientes que presenten un certificado para conectarse a él.

Si intenta conectarse al explorador de Service Fabric en un clúster seguro, el explorador le pedirá que presente un certificado para poder tener acceso.

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
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=Nov15_HO4-->