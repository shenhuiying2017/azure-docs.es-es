---
title: Tutorial sobre las actualizaciones de aplicaciones de Service Fabric | Microsoft Docs
description: "Este artículo le guía a través de la experiencia de implementación de una aplicación de Service Fabric, la modificación del código y la aplicación de actualizaciones con Visual Studio."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 940440688ec770a4aeb932b574bd6be173f494d4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial sobre la actualización de aplicaciones de Service Fabric con Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric simplifica el proceso de actualización de aplicaciones en la nube asegurándose de que solo se actualicen los servicios modificados y que el estado de las aplicaciones se supervise durante todo el proceso de actualización. También revierte automáticamente la aplicación a la versión anterior si se detecta algún problema. Las actualizaciones de aplicaciones de Service Fabric presentan un *tiempo de inactividad cero*, ya que la aplicación puede actualizarse sin que se ocasione tiempo de inactividad. Este tutorial explica cómo llevar a cabo una actualización acumulativa desde Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Paso 1: Crear y publicar el ejemplo de Visual Objects
En primer lugar, descargue la aplicación [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) desde GitHub. A continuación, compile y publique la aplicación, para ello, haga clic con el botón derecho en el proyecto de aplicación, **VisualObjects** y seleccione el comando **Publicar** en el elemento de menú de Service Fabric.

![Menú contextual para una aplicación de Service Fabric][image1]

Al seleccionar **Publicar** se muestra una ventana emergente donde puede establecer el **perfil de destino** en **PublishProfiles\Local.xml**. La ventana debe ser similar a la siguiente antes de hacer clic en **Publicar**.

![Publicación de una aplicación de Service Fabric][image2]

Ahora ya puede hacer clic en **Publicar** en el cuadro de diálogo. Ya puede usar el [explorador de Service Fabric para ver el clúster y la aplicación](service-fabric-visualizing-your-cluster.md). La aplicación Visual Objects tiene un servicio web al que puede acudir si escribe [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) en la barra de direcciones del explorador.  Debería ver 10 objetos visuales flotantes desplazándose por la pantalla.

**NOTA:** Si implementa en el perfil `Cloud.xml` (Azure Service Fabric), la aplicación debe estar disponible en **http://{ServiceFabricName}.{Region}.cloudapp.azure.com:8081/visualobjects/**. Asegúrese de tener `8081/TCP` configurado en Load Balancer (busque Load Balancer en el mismo grupo de recursos que la instancia de Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Paso 2: Actualizar el ejemplo de objetos visuales
Puede que observe que con la versión que se implementó en el Paso 1, los objetos visuales no giran. Vamos a actualizar esta aplicación a una donde los objetos visuales giren.

Seleccione el proyecto VisualObjects.ActorService en la solución de VisualObjects y abra el archivo **VisualObjectActor.cs** . Dentro de ese archivo, vaya al método `MoveObject`, comente `visualObject.Move(false)` y quite la marca de comentario de `visualObject.Move(true)`. Este cambio de código gira los objetos después de que se actualiza el servicio.  **Ahora puede compilar (no recompilar) la solución**, de forma que se compilarán los proyectos modificados. Si selecciona *Recompilar todo*, tiene que actualizar las versiones de todos los proyectos.

También necesitamos generar la versión de nuestra aplicación. Para realizar los cambios de versión después de hacer clic con el botón derecho en el proyecto **VisualObjects**, puede usar la opción **Editar versiones de manifiesto** de Visual Studio. Esta opción abre el cuadro de diálogo de versiones de la manera siguiente:

![Cuadro de diálogo de control de versiones][image3]

Actualice las versiones de los proyectos modificados y sus paquetes de código, junto con la aplicación, a la versión 2.0.0. Una vez realizados los cambios, el manifiesto debe ser similar al siguiente (las partes negrita muestran los cambios):

![Actualización de versiones][image4]

Las herramientas de Visual Studio pueden hacer resúmenes automáticos de las versiones si selecciona **Actualizar automáticamente las versiones de aplicación y servicio**. Si usa [SemVer](http://www.semver.org), tiene que actualizar el código o la versión del paquete de configuración solo si esa opción está seleccionada.

Guarde los cambios y marque la casilla **Actualizar la aplicación** .

## <a name="step-3--upgrade-your-application"></a>Paso 3: Actualizar la aplicación
Familiarícese con los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para comprender bien los distintos parámetros de actualización, tiempos de espera y criterios de estado que se pueden aplicar. Para los fines de este tutorial, el criterio de evaluación del estado del servicio se establece en el valor predeterminado (modo sin supervisión). Para configurar estas opciones, elija **Configuración de los valores de actualización** y modifique los parámetros según sea necesario.

Ahora, estamos preparados para iniciar la actualización de la aplicación mediante la opción **Publicar**. Esta opción actualiza la aplicación a la versión 2.0.0, en la que giran los objetos. Service Fabric actualiza un dominio de actualización a la vez (algunos objetos se actualizan primero, seguido de otros) y el servicio permanece accesible durante la actualización. Se puede comprobar el acceso al servicio a través del cliente (explorador).  

Ahora, a medida que se lleva a cabo la actualización de la aplicación, puede supervisar el proceso usando Service Fabric Explorer (pestaña **Upgrades in Progress** (Actualizaciones en curso) en las aplicaciones).

En unos minutos, todos los dominios de actualización deben estar actualizados (completados). La ventana de salida de Visual Studio también debe indicar que la actualización ha finalizado. Ahora *todos* los objetos visuales de la ventana del explorador deben haber comenzado a girar.

Puede que desee cambiar las versiones y pasar de la versión 2.0.0 a la versión 3.0.0 como ejercicio, o incluso volver de la versión 2.0.0 a la 1.0.0. Juegue con los tiempos de espera y las directivas de mantenimiento para familiarizarse. Al implementar en un clúster de Azure, en lugar de en un clúster local, los parámetros usados pueden ser diferentes. Se recomienda establecer los tiempos de espera de manera conservadora.

## <a name="next-steps"></a>Pasos siguientes
[actualización de aplicaciones mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) se explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
