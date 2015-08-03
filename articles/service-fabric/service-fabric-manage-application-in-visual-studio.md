<properties
   pageTitle="Administración de aplicaciones de Service Fabric en Visual Studio"
   description="Puede administrar sus servicios y aplicaciones de Service Fabric de Microsoft Azure a través de Visual Studio."
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
   ms.date="04/14/2015"
   ms.author="jesseb"/>

# Administración de aplicaciones de Service Fabric en Visual Studio

Puede administrar sus servicios y aplicaciones de Service Fabric de Microsoft Azure a través de Visual Studio. Cuando haya [configurado su entorno de desarrollo](../service-fabric-setup-your-development-environment), puede usar Visual Studio para crear aplicaciones de Service Fabric, agregar servicios, o empaquetar, registrar e implementar aplicaciones en el clúster de desarrollo local.

Para administrar su aplicación de Service Fabric, en el Explorador de soluciones haga doble clic en su proyecto de aplicación.

![Para administrar la aplicación de Service Fabric, haga clic con el botón secundario en el proyecto de aplicación][manageservicefabric]

## Implementación de la aplicación de Service Fabric

La implementación de una aplicación de Service Fabric combina los pasos siguientes en una operación sencilla.

1. Creación del paquete de aplicación
2. Carga del paquete de aplicación en el almacén de imágenes
3. Registro del tipo de aplicación
4. Eliminación de cualquier instancia de aplicación en ejecución
5. Creación de una nueva instancia de aplicación

En Visual Studio, para realizar la implementación, haga clic en el botón secundario en la aplicación de Service Fabric en el **Explorador de soluciones** y haga clic en **Implementar**. Al presionar **F5** también se implementará su aplicación y se asociará el depurador a todas las instancias de aplicación.

La implementación se puede quitar con **Quitar implementación**. De esta manera se revertirán los pasos de implementación anteriores.

1. Eliminar cualquier instancia de aplicación en ejecución
2. Anular el registro del tipo de aplicación
3. Quitar el paquete de aplicación del almacén de imágenes

## Adición de un servicio a su aplicación de Service Fabric

Puede agregar nuevos servicios de tejido a su aplicación para ampliar su funcionalidad. Para garantizar que el servicio se incluye en el paquete de aplicación, agregue el servicio a través del elemento de menú **Nuevo servicio del tejido...**.

![Agregar un nuevo Service Fabric a la aplicación][newservice]

Seleccione un tipo de proyecto de Service Fabric para agregarlo a la aplicación y especifique un nombre para el servicio. Vea [Elección de un marco para el servicio](service-fabric-choose-framework.md) para ayudarle a decidir qué tipo de servicio desea usar.

![Seleccionar un tipo de proyecto de Service Fabric para agregarlo a la aplicación][addserviceproject]

El nuevo servicio se agregará a la solución y al paquete de aplicación existente. Se agregarán las referencias del servicio y una instancia predeterminada del servicio al manifiesto de aplicación. El servicio se creará e iniciará la próxima vez que implemente la aplicación.

![Se agregará el nuevo servicio a su manifiesto de aplicación][newserviceapplicationmanifest]

## Empaquetado de la aplicación de Service Fabric

Se tiene que crear un paquete de aplicación para implementar la aplicación y sus servicios en un clúster. El paquete organiza el manifiesto de aplicación, los manifiestos de servicio y otros archivos necesarios en un diseño específico. Visual Studio configura y administra el paquete en la carpeta del proyecto de aplicación, en el directorio 'pkg'. Haga clic en **Paquete** para crear o actualizar el paquete de aplicación. Puede que desee hacerlo si implementa la aplicación mediante scripts de Powershell personalizados.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

- [Modelo de aplicación de Service Fabric](../service-fabric-application-model)
- [Implementación de la aplicación de Service Fabric](../service-fabric-deploy-remove-applications)
- [Depuración de la aplicación de Service Fabric](../service-fabric-debugging-your-application)
- [Visualización del clúster mediante el Explorador de Service Fabric](../service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
 

<!---HONumber=July15_HO4-->