---
title: "Solución de problemas con el seguimiento de eventos | Microsoft Docs"
description: "Los problemas más habituales que aparecen al implementar servicios en Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mattrowmsft
manager: timlt
editor: 
ms.assetid: 5eb8ef21-da04-4ac8-8b9a-5f7ff1e0a180
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/31/2016
ms.author: mattrow
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45cfb660db9ea1d93d136d223d65602f3a373574


---
# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Solución de problemas comunes al implementar servicios en Azure Service Fabric
Al ejecutar servicios en su equipo de desarrollador, es fácil usar [herramientas de depuración de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Para clústeres remotos, los [informes de estado](service-fabric-view-entities-aggregated-health.md) resultan siempre un buen punto de partida. La manera más sencilla de obtener acceso a estos informes es a través de PowerShell o [SFX](service-fabric-visualizing-your-cluster.md). En este artículo se supone que está depurando un clúster remoto y que tiene un conocimiento básico de cómo usar cualquiera de estas herramientas.

## <a name="application-crash"></a>Bloqueo de aplicaciones
El informe "La partición se encuentra por debajo del número de instancias o de réplicas de destino" es un buen indicio de que se bloquea el servicio. Para averiguar dónde se bloquea su servicio, será necesario investigar un poco más. Cuando el servicio se ejecuta a escala, su mejor amigo será un conjunto de seguimiento bien elaborados.  Le recomendamos que pruebe [Diagnósticos de Microsoft Azure](service-fabric-diagnostics-how-to-setup-wad.md) para recopilar esos seguimientos y usar una solución como [Elasticsearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) para ver y buscar los seguimientos.

![Estado de las particiones SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

### <a name="during-service-or-actor-initialization"></a>Durante la inicialización del actor o del servicio
Las excepciones antes de que se inicialice el tipo de servicio harán que se bloquee el proceso. Para estos tipos de bloqueos, el registro de eventos de la aplicación mostrará el error desde el servicio.
Estas son las excepciones más comunes que se ven antes de que se inicialice el servicio.

***System.IO.FileNotFoundException***

Este error a menudo se debe a que faltan las dependencias de ensamblado. Compruebe la propiedad CopyLocal en Visual Studio o en la caché global de ensamblados para el nodo.

***System.Runtime.InteropServices.COMException***
 *en System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)*

 Esto indica que el nombre del tipo de servicio registrado no coincide con el manifiesto de servicio.

[Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md) se puede configurar para cargar automáticamente el registro de eventos de la aplicación para todos los nodos.

### <a name="runasync-or-onactivateasync"></a>RunAsync() o OnActivateAsync()
Si el bloqueo se produce durante la inicialización o al ejecutar su actor o tipo de servicio registrado, Azure Service Fabric detectará la excepción. Puede verlos desde los proveedores de EventSource detallados en la sección "Pasos siguientes".

## <a name="next-steps"></a>Pasos siguientes
Más información sobre el diagnóstico existente ofrecido por Service Fabric:

* [Diagnósticos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Diagnóstico de Reliable Services](service-fabric-reliable-services-diagnostics.md)




<!--HONumber=Nov16_HO3-->


