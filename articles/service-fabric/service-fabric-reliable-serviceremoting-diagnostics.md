---
title: "Diagnóstico y supervisión de Azure Service Fabric | Microsoft Docs"
description: "En este artículo se describen las características de supervisión del rendimiento del entorno de tiempo de ejecución Reliable ServiceRemoting de Service Fabric, incluidos los contadores de rendimiento y los eventos que emite."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: f54e157654fb15d2f7ff48ddc666c6c8803c75a2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Supervisión de diagnósticos y rendimiento de Reliable ServiceRemoting
El entorno de tiempo de ejecución de Reliable ServiceRemoting emite [contadores de rendimiento](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estos ofrecen información sobre cómo está funcionando Reliable ServiceRemoting y le ayudarán con la solución de problemas y la supervisión de rendimiento.


## <a name="performance-counters"></a>Contadores de rendimiento
El entorno de tiempo de ejecución de Reliable ServiceRemoting define las siguientes categorías de contador de rendimiento:

| Categoría | Descripción |
| --- | --- |
| Servicio de Service Fabric |Contadores específicos para la comunicación remota de servicios de Azure Service Fabric como, por ejemplo, el promedio de tiempo empleado en procesar la solicitud |
| Método del servicio Service Fabric |Contadores específicos de los métodos que implementan los servicios de comunicación remota de Service Fabric. Por ejemplo, la frecuencia con que se invoca un método de servicio. |

Cada una de las categorías anteriores tiene uno o varios contadores.

La aplicación del [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponible de forma predeterminada en el sistema operativo Windows puede usarse para recopilar y ver los datos del contador de rendimiento. [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) es otra opción para recopilar datos del contador de rendimiento y cargarlos en tablas de Azure.

### <a name="performance-counter-instance-names"></a>Nombres de instancias de contador de rendimiento
Un clúster con un gran número de servicios o particiones de ServiceRemoting tendrá una gran cantidad de instancias de contador de rendimiento. Los nombres de las instancias del contador de rendimiento pueden ayudar a identificar la partición y el método de servicio (si procede) específicos con los que está asociada la instancia de contador de rendimiento.

#### <a name="service-fabric-service-category"></a>Categoría del servicio de Service Fabric
En la categoría `Service Fabric Service`, los nombres de instancias de contadores tienen el formato siguiente:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* es la representación de cadena del identificador de partición de Service Fabric con el que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y su representación de cadena se genera mediante el método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con el especificador de formato "D".

*ServiceReplicaOrInstanceId* es la representación de cadena del identificador de réplica o instancia de Service Fabric con el que está asociada la instancia de contador de rendimiento.

*ServiceRuntimeInternalID* es la representación de cadena de un entero de 64 bits que el tiempo de ejecución de los servicios de Fabric genera para su uso interno. Esto se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

A continuación se muestra un ejemplo de un nombre de instancia de un contador que pertenece a la categoría `Service Fabric Service` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

En el ejemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` es la representación de cadena del identificador de partición de Service Fabric, `635650083799324046` es la representación de cadena del identificador de réplica o instancia, y `5008379932` es el identificador de 64 bits que se genera para uso interno del entorno de tiempo de ejecución.

#### <a name="service-fabric-service-method-category"></a>Categoría del método del servicio Service Fabric
En la categoría `Service Fabric Service Method`, los nombres de instancias de contadores tienen el formato siguiente:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* es el nombre del método de servicio con el que está asociada la instancia de contador de rendimiento. El formato del nombre del método se determina según una lógica del tiempo de ejecución de los servicios de Fabric que compensa la legibilidad del nombre con restricciones sobre la longitud máxima de los nombres de instancia de contador de rendimiento en Windows.

*ServiceRuntimeMethodId* es la representación de cadena de un entero de 32 bits que el entorno de tiempo de ejecución de los servicios de Fabric genera para su uso interno. Esto se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

*ServiceFabricPartitionID* es la representación de cadena del identificador de partición de Service Fabric con el que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y su representación de cadena se genera mediante el método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con el especificador de formato "D".

*ServiceReplicaOrInstanceId* es la representación de cadena del identificador de réplica o instancia de Service Fabric con el que está asociada la instancia de contador de rendimiento.

*ServiceRuntimeInternalID* es la representación de cadena de un entero de 64 bits que el tiempo de ejecución de los servicios de Fabric genera para su uso interno. Esto se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

A continuación se muestra un ejemplo de un nombre de instancia de un contador que pertenece a la categoría `Service Fabric Service Method` :

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

En el ejemplo anterior, `ivoicemailboxservice.leavemessageasync` es un nombre de método, `2` es el identificador de 32 bits generado para uso interno del entorno de tiempo de ejecución, `89383d32-e57e-4a9b-a6ad-57c6792aa521` es la representación de cadena del identificador de partición de Service Fabric, `635650083804480486` es la representación de cadena del identificador de réplica o instancia de Service Fabric, y `5008380` es el identificador de 64 bits que se genera para uso interno del entorno de tiempo de ejecución.

## <a name="list-of-performance-counters"></a>Lista de contadores de rendimiento
### <a name="service-method-performance-counters"></a>Contadores de rendimiento del método de servicio

El entorno de tiempo de ejecución del servicio de confianza publica los siguientes contadores de rendimiento relacionados con la ejecución de los métodos de servicio.

| Nombre de la categoría | Nombre del contador | Descripción |
| --- | --- | --- |
| Método del servicio Service Fabric |Invocaciones/seg. |Número de veces que se invoca el método de servicio del actor por segundo |
| Método del servicio Service Fabric |Promedio de milisegundos por invocación |Tiempo necesario para ejecutar el método de servicio en milisegundos |
| Método del servicio Service Fabric |Excepciones generadas/seg. |Número de veces que el método de servicio genera una excepción por segundo |

### <a name="service-request-processing-performance-counters"></a>Contadores de rendimiento del procesamiento de solicitudes de servicio
Cuando un cliente invoca un método a través de un objeto de proxy de servicio, se envía un mensaje de solicitud a través de la red al servicio de comunicación remota. El servicio procesa el mensaje de solicitud y envía una respuesta al cliente. El entorno de tiempo de ejecución de Reliable ServiceRemoting publica los siguientes contadores de rendimiento relacionados con el procesamiento de las solicitudes de servicio.

| Nombre de la categoría | Nombre del contador | Descripción |
| --- | --- | --- |
| Servicio de Service Fabric |Número de solicitudes pendientes |Número de solicitudes que se procesan en el servicio |
| Servicio de Service Fabric |Promedio de milisegundos por solicitud |Tiempo que tarda el servicio (en milisegundos) en procesar una solicitud |
| Servicio de Service Fabric |Promedio de milisegundos para la deserialización de una solicitud |Tiempo necesario (en milisegundos) para deserializar el mensaje de solicitud de servicio cuando se recibe en el servicio |
| Servicio de Service Fabric |Promedio de milisegundos para la deserialización de una respuesta |Tiempo necesario (en milisegundos) para serializar el mensaje de respuesta de servicio en el servicio antes de que la respuesta se envíe al cliente |

## <a name="next-steps"></a>Pasos siguientes
* [Código de ejemplo](https://github.com/Azure/servicefabric-samples)
* [Proveedores de EventSource en PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
