---
title: Ejemplos de Azure Redis Cache | Microsoft Docs
description: "Obtener información acerca de cómo usar Azure Redis Cache"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0b6f89807d3252b750bd5208a7f758a06c9903d6
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-redis-cache-samples"></a>Ejemplos de Azure Redis Cache
Este tema proporciona una lista de ejemplos de Azure Redis Cache y cubre escenarios como conexión a una caché, operaciones de lectura y escritura en una caché y uso de proveedores de Redis Cache de ASP.NET. Algunos de los ejemplos son proyectos que se pueden descargar y algunos otros proporcionan instrucciones paso a paso e incluyen fragmentos de código pero no vínculos a un proyecto que se puede descargar.

## <a name="hello-world-samples"></a>Ejemplos Hello world
Los ejemplos de esta sección muestran los conceptos básicos de conexión a una instancia de Azure Redis Cache y de lectura y escritura de datos en la memoria caché usando una gran variedad de lenguajes y clientes de Redis.

En el ejemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) se muestra cómo realizar diversas operaciones de caché mediante el cliente .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Este ejemplo lo siguiente:

* Uso de distintas opciones de conexión
* Lectura y escritura de objetos hacia y desde la memoria caché mediante operaciones sincrónicas y asincrónicas
* Uso de comandos MGET/MSET de Redis para devolver valores de las claves especificadas
* Realización de operaciones transaccionales de Redis
* Trabajo con listas de Redis y conjuntos ordenados
* Almacenamiento de objetos de .NET con serializadores JsonConvert
* Uso de conjuntos de Redis para implementar el etiquetado
* Trabajar con el Clúster en Redis

Para más información, vea la documentación de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) en GitHub y para ver más escenarios de uso, vea las pruebas unitarias [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests).

[Uso de Azure Redis Cache con Python](cache-python-get-started.md) muestra cómo empezar a usar Azure Redis Cache con Python y el cliente [redis-py](https://github.com/andymccurdy/redis-py).

[Trabajar con objetos .NET en la memoria caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) muestra una forma de serializar objetos .NET de forma que pueda realizar operaciones de escritura y lectura con ellos en una instancia de Azure Redis Cache. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Uso de Caché en Redis como un backplane de escalado horizontal para ASP.NET SignalRen
El ejemplo [Uso de Redis Cache como un backplane de escalado horizontal para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) muestra cómo puede usar Azure Redis Cache como un backplane SignalR. Para obtener más información acerca de backplane, consulte [Escalado horizontal SignalR con Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Ejemplo de consulta de cliente de Caché en Redis
Este ejemplo compara el rendimiento entre el acceso a datos desde una memoria caché y el acceso a datos desde almacenamiento de persistencia. Este ejemplo tiene dos proyectos.

* [Demostración de cómo Caché en Redis puede mejorar el rendimiento almacenando datos en caché](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Inicialización de la base de datos y la memoria caché para la demostración](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado de sesión de ASP.NET y almacenamiento en caché de resultados
El ejemplo [Uso de Azure Redis Cache para almacenar SessionState y OutputCache de ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) muestra cómo usar Azure Redis Cache para almacenar la sesión y la memoria caché de salida usando los proveedores SessionState y OutputCache para Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Administración de Azure Redis Cache con Azure MAML
El ejemplo [Administración de Azure Redis Cache mediante bibliotecas de administración de Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) muestra cómo usar bibliotecas de administración de Azure para administrar (crear, actualizar y eliminar) su memoria caché. 

## <a name="custom-monitoring-sample"></a>Ejemplo de personalización de supervisión
El [ejemplo de acceso a los datos de supervisión de Redis Cache](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) muestra cómo puede obtener acceso a datos de supervisión para su instancia de Azure Redis Cache fuera de Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un clon de estilo Twitter escrito mediante PHP y Redis
El ejemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) es el Hello World de Redis. Es un clon de red social mínima de tipo Twitter escrito mediante Redis y PHP mediante el cliente [Predis](https://github.com/nrk/predis) . El código fuente está diseñado para ser muy sencillo y al mismo tiempo para mostrar diferentes estructuras de datos de Redis.

## <a name="bandwidth-monitor"></a>Supervisión del ancho de banda
El ejemplo [Supervisión del ancho de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite supervisar el ancho de banda utilizado en el cliente. Para medir el ancho de banda, ejecute el ejemplo en el equipo cliente de la caché, realice llamadas a la memoria caché y observe el ancho de banda notificado por el ejemplo de supervisión de ancho de banda.

