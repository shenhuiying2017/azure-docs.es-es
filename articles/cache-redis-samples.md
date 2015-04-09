<properties 
	pageTitle="Ejemplos de Caché en Redis de Azure" 
	description="Obtener información acerca de cómo usar Caché en Redis de Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="sdanie"/>

# Ejemplos de Caché en Redis de Azure 

Este tema proporciona una lista de ejemplos de Caché en Redis de Azure y cubre escenarios como conexión a una caché, operaciones de lectura y escritura en una caché y uso de proveedores de Caché en Redis. Algunos de los ejemplos son proyectos que se pueden descargar y algunos otros proporcionan instrucciones paso a paso e incluyen fragmentos de código pero no vínculos a un proyecto que se puede descargar.

## Ejemplos Hello world

Los ejemplos de esta sección muestran los conceptos básicos de conexión a una instancia de Caché en Redis de Azure y de lectura y escritura de datos en la memoria caché usando una gran variedad de lenguajes y clientes de Redis.

El ejemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) muestra cómo leer y escribir elementos en una memoria caché usando el cliente .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

[Uso de Caché en Redis de Azure con Node.js](cache-nodejs-get-started.md) le muestra cómo comenzar a usar Caché en Redis de Azure mediante Node.js y el cliente [node_redis](https://github.com/mranney/node_redis).

[Uso de Caché en Redis de Azure con Java](cache-java-get-started.md) le muestra cómo comenzar a usar Caché en Redis de Azure mediante Java y el cliente [Jedis](https://github.com/xetorthio/jedis).

[Uso de Caché en Redis de Azure con Python](cache-python-get-started.md) le muestra cómo comenzar a usar Caché en Redis de Azure mediante Python y el cliente [redis-py](https://github.com/andymccurdy/redis-py).

El [ejemplo PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) muestra cómo comenzar a usar Caché en Redis de Azure con PHP y el cliente [predis](https://github.com/nrk/predis).

[Trabajo con objetos .NET en la memoria caché](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) le muestra una forma de serializar objetos .NET de forma que pueda realizar operaciones de escritura y lectura con ellos en una instancia de Caché en Redis de Azure. 

## Uso de Caché en Redis como un backplane de escalado horizontal para ASP.NET SignalRen

El ejemplo [Uso de Caché en Redis como un backplane de escalado horizontal para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) muestra cómo puede usar Caché en Redis de Azure como un backplane SignalR. Para obtener más información acerca de backplane, vea [Escalado horizontal SignalR con Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Ejemplo de consulta de cliente de Caché en Redis

Este ejemplo compara el rendimiento entre el acceso a datos desde una memoria caché y el acceso a datos desde almacenamiento de persistencia. Este ejemplo tiene dos proyectos.

-	[Demostración de cómo Caché en Redis puede mejorar el rendimiento almacenando datos en caché](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Inicialización de la base de datos y la memoria caché para la demostración](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## Estado de sesión de ASP.NET y almacenamiento en caché de resultados

El ejemplo [Uso de Caché en Redis de Azure para almacenar SessionState y OutputCache de ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) muestra cómo usar Caché en Redis de Azure para almacenar la sesión y la memoria caché de salida usando los proveedores SessionState y OutputCache para Redis.

## Administración de Caché en Redis de Azure con Azure MAML

El ejemplo [Administración de Caché en Redis de Azure usando bibliotecas de administración de Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) muestra cómo usar bibliotecas de administración Azure para administrar (crear, actualizar y eliminar) su memoria caché. 

## Ejemplo de personalización de supervisión

El ejemplo [Acceso a los datos de supervisión de Caché en Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) muestra cómo puede obtener acceso a datos de supervisión para su Caché en Redis de Azure fuera del portal de Azure.



<!--HONumber=49-->