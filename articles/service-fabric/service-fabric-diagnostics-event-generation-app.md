---
title: Supervisión del nivel de aplicación de Azure Service Fabric | Microsoft Docs
description: Obtenga información sobre los eventos y los registros de nivel de servicio y aplicación usados para supervisar y diagnosticar los clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: f3e7b9c7432538c0f78662213544d4d691652f13
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="application-and-service-level-logging"></a>Registro en el nivel de aplicación y de servicio

La instrumentación del código es la base para la mayoría de los demás aspectos de la supervisión de los servicios. La instrumentación es la única manera de saber que algo no funciona correctamente y diagnosticar lo que debe solucionarse. Aunque técnicamente es posible que conectar un depurador a un servicio de producción, no es un procedimiento habitual. Por lo tanto, es importante disponer de datos de instrumentación detallados.

Algunos productos instrumentan el código automáticamente. Aunque estas soluciones pueden funcionar bien, casi siempre se necesita instrumentación manual. Al final, debe tener suficiente información para depurar desde la aplicación de manera forense. Este documento describe los distintos enfoques para instrumentar el código y se indica cuándo elegir uno u otro.

Para ver ejemplos sobre cómo usar estas sugerencias, consulte [Adición del registro a la aplicación de Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Cuando se crea una solución de Service Fabric a partir de una plantilla en Visual Studio, se genera una clase derivada de **EventSource** (**ServiceEventSource** o **ActorEventSource**). Se crea una plantilla en la que podrá agregar eventos para la aplicación o el servicio. El nombre de **EventSource** **debe** ser único y debe cambiarse a partir de la cadena de plantilla predeterminada de MyCompany-&lt;solución&gt;-&lt;proyecto&gt;. El hecho de tener varias definiciones de **EventSource** con el mismo nombre genera un problema en tiempo de ejecución. Cada evento definido debe tener un identificador único. Si el identificador no es único, se produce un error en tiempo de ejecución. En algunas organizaciones se asignan previamente rangos de valores para los identificadores, lo cual evita conflictos entre los equipos de desarrollo independientes. Para más información, consulte el [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentación de MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Registro de ASP.NET Core

Es importante planear minuciosamente la instrumentación del código. Un plan de instrumentación correcto puede ayudarle a evitar que se desestabilice el código base y sea necesario volver a instrumentarlo. Para reducir el riesgo, puede elegir una biblioteca de instrumentación como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), componente de Microsoft ASP.NET Core. ASP.NET Core tiene una interfaz [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que puede usar con su proveedor preferido al tiempo que reduce al mínimo el efecto sobre el código existente. Puede utilizar el código de ASP.NET Core en Windows y Linux, y en .NET Framework completo, por lo que el código de instrumentación es estándar.

## <a name="choosing-a-logging-provider"></a>Elección de un proveedor de registro

Si la aplicación depende del alto rendimiento, **EventSource** suele ser el mejor enfoque. *Por lo general*, **EventSource** utiliza menos recursos y su rendimiento es mejor que el del registro de ASP.NET Core o de cualquiera de las soluciones de terceros disponibles.  Esto no supone un problema para muchos servicios, pero si está orientado al rendimiento, **EventSource** sería una opción mejor. Sin embargo, para obtener las ventajas del registro estructurado, **EventSource** requiere una mayor inversión por parte del equipo de ingeniería. Si es posible, cree un prototipo rápido de algunas opciones de registro y, luego, seleccione la que mejor se adapte a sus necesidades.

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya elegido el proveedor de registro para instrumentar las aplicaciones y los servicios, los registros y los eventos deben agregarse antes de que se puedan enviar a cualquier plataforma de análisis. Obtenga información sobre [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) y [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para entender mejor algunas de las opciones recomendadas.
