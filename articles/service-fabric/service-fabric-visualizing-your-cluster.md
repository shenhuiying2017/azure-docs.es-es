---
title: "Visualización del clúster mediante el Explorador de Service Fabric | Microsoft Docs"
description: "El Explorador de Service Fabric es una herramienta basada en web para inspeccionar y administrar aplicaciones y nodos en la nube en un clúster de Service Fabric de Microsoft Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: c58de22e29b6403e88bf22bdfe704a25757cdfca
ms.contentlocale: es-es
ms.lasthandoff: 06/13/2017

---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualización del clúster mediante el Explorador de Service Fabric
El Explorador de Service Fabric es una herramienta web para inspeccionar y administrar aplicaciones y nodos en un clúster de Azure Service Fabric. El Explorador de Service Fabric se hospeda directamente en el clúster para que siempre esté disponible, independientemente de dónde se ejecuta el clúster.

## <a name="video-tutorial"></a>Tutorial en vídeo

Para información sobre cómo usar Service Fabric Explorer, vea el siguiente vídeo de Microsoft Virtual Academy:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Conexión al Explorador de Service Fabric
Si ha seguido las instrucciones para [preparar el entorno de desarrollo](service-fabric-get-started.md), puede iniciar el Explorador de Service Fabric en el clúster local en http://localhost:19080/Explorer.

## <a name="understand-the-service-fabric-explorer-layout"></a>Información sobre el diseño del Explorador de Service Fabric
Puede desplazarse por el Explorador de Service Fabric desde el árbol situado a la izquierda. En la raíz del árbol, el panel del clúster proporciona información general del clúster, incluido un resumen de la aplicación y del estado del nodo.

![Panel de clúster del Explorador de Service Fabric][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Visualización del diseño del clúster
Los nodos de un clúster de Service Fabric se colocan en una cuadrícula bidimensional de dominios de error y dominios de actualización. Esta colocación garantiza que las aplicaciones permanecen disponibles en presencia de errores de hardware y actualizaciones de aplicaciones. Puede ver cómo se dispone el clúster actual mediante el mapa del clúster.

![Mapa de clúster del Explorador de Service Fabric][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Visualización de aplicaciones y servicios
El clúster contiene dos subárboles: uno para las aplicaciones y otro para los nodos.

Puede usar la vista de aplicaciones para navegar por la jerarquía lógica de Service Fabric: aplicaciones, servicios, particiones y réplicas.

En el ejemplo siguiente, la aplicación **MyApp** se compone de dos servicios: **MyStatefulService** y **WebService**. Como **MyStatefulService** es un servicio con estado, incluye una partición con una réplica principal y dos réplicas secundarias. Por el contrario, el servicio WebSvcService no tiene estado y contiene una única instancia.

![Vista de aplicación del explorador de Service Fabric][sfx-application-tree]

En cada nivel del árbol, el panel principal muestra la información pertinente sobre el elemento. Por ejemplo, puede ver el estado de mantenimiento y la versión para un servicio determinado.

![Panel Essentials del explorador de Service Fabric][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visualización de los nodos del clúster
En la vista de nodos se muestra el diseño físico del clúster. Para un nodo determinado, puede comprobar qué aplicaciones tienen el código implementado en ese nodo. Más específicamente, puede ver qué réplicas están en ejecución.

## <a name="actions"></a>Acciones
El explorador de Service Fabric ofrece una forma rápida de invocar acciones en nodos, aplicaciones y servicios dentro del clúster.

Por ejemplo, para eliminar una instancia de la aplicación, elija la aplicación en el árbol de la izquierda y, luego, elija **Acciones** > **Eliminar aplicación**.

![Eliminación de una aplicación en el explorador de Service Fabric][sfx-delete-application]

> [!TIP]
> Puede realizar las mismas acciones haciendo clic en los puntos suspensivos situados junto a cada elemento.
>
>

En la tabla siguiente se enumeran las acciones disponibles para cada entidad:

| **Entidad** | **Acción** | **Descripción** |
| --- | --- | --- |
| Tipo de aplicación |Tipo de anulación de aprovisionamiento |Quita el paquete de aplicación del almacén de imágenes del clúster. Requiere que todas las aplicaciones de ese tipo se quiten en primer lugar. |
| Application |Eliminar aplicación |Elimine la aplicación, incluidos todos sus servicios y su estado (si hubiera). |
| Servicio |Eliminar servicio |Elimine el servicio y su estado (si hubiera). |
| Nodo |Activar |Active el nodo. |
| Nodo | Desactivar (pausa) | Pause el nodo en su estado actual. Los servicios siguen ejecutándose, pero Service Fabric no introduce ni saca nada proactivamente, a menos que se requiera para prevenir una interrupción o una incoherencia de datos. Esta acción se utiliza normalmente para habilitar los servicios de depuración en un nodo específico para asegurarse de que no se mueven durante la inspección. | |
| Nodo | Desactivar (reiniciar) | Saque todos los servicios de la memoria de un nodo y cierre los servicios persistentes de forma segura. Suele usarse cuando es necesario reiniciar los procesos de host o el equipo. | |
| Nodo | Desactivar (quitar datos) | Cierre todos los servicios que se ejecutan en el nodo después de la creación de suficientes réplicas de reserva con seguridad. Se utiliza normalmente cuando un nodo (o al menos su almacenamiento) se está sacando permanentemente de circulación. | |
| Nodo | Quitar el estado del nodo | Quite información de las réplicas de un nodo del clúster. Se utiliza normalmente cuando un nodo con error ya se considera irrecuperable. | |
| Nodo | Reiniciar | Reinicie el nodo para simular un error de nodo. Puede encontrar más información [aquí](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps) | |

Como muchas acciones son destructivas, le pediremos que confirme su intención antes de que finalice la acción.

> [!TIP]
> Todas las acciones que pueden realizarse a través del Explorador de Service Fabric también pueden realizarse a través de PowerShell o una API de REST, para habilitar la automatización.
>
>

También puede utilizar el Service Fabric Explorer a fin de crear instancias de aplicaciones para un tipo de aplicación y versión. Elija el tipo de aplicación en la vista de árbol y, luego, haga clic en el vínculo **Create app instance** (Crear instancia de aplicación) situado junto a la versión que desee en el panel derecho.

![Creación de una instancia de aplicación en Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> En la actualidad, no se pueden parametrizar las instancias de aplicaciones creadas mediante Service Fabric Explorer. Se crean utilizando los valores de parámetros predeterminados.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Conexión a un clúster de Service Fabric remoto
Si conoce el punto de conexión del clúster y tiene los permisos suficientes, puede tener acceso a Service Fabric Explorer desde cualquier explorador. Esto se debe a que Service Fabric Explorer no es más que otro servicio que se ejecuta en el clúster.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>Detección del punto de conexión del Explorador de Service Fabric para un clúster remoto
Si desea tener acceso a Service Fabric Explorer para un clúster determinado, dirija el explorador a:

http://&lt;su-punto-de-conexión-de-clúster&gt;:19080/Explorer

En el caso de clústeres de Azure, la dirección URL completa también está disponible en el panel de elementos esenciales del clúster de Azure Portal.

### <a name="connect-to-a-secure-cluster"></a>Conexión a un clúster seguro
Puede controlar el acceso de cliente a su clúster de Service Fabric con certificados o mediante Azure Active Directory (AAD).

Si intenta conectarse a Service Fabric Explorer en un clúster seguro, se le pedirá presentar un certificado de cliente o iniciar sesión con AAD, según la configuración del clúster.

## <a name="next-steps"></a>Pasos siguientes
* [Información general sobre Testability](service-fabric-testability-overview.md)
* [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Implementación de aplicaciones de Service Fabric con PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png

