<properties
	pageTitle="Uso de Caché en Redis de Azure con Node.js | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Node.js y node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Caché en Redis de Azure le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure mediante Node.js. Para obtener otro ejemplo de uso de Caché en Redis de Azure con Node.js, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Requisitos previos

Instale [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

En este tutorial se usa [node\_redis](https://github.com/mranney/node_redis). Para ver ejemplos del uso de otros clientes de Node.js, consulte la documentación individual de los clientes de Node.js, que encontrará en [clientes Redis de Node.js](http://redis.io/clients#nodejs).

## Crear una caché de Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperación del nombre de host y las claves de acceso

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Conexión a la caché de forma segura mediante SSL

Las últimas compilaciones de [node\_redis](https://github.com/mranney/node_redis) permiten la conexión a Caché en Redis de Azure mediante SSL. En el ejemplo siguiente se muestra cómo conectarse a Caché en Redis de Azure con el punto de conexión SSL de 6380. Reemplace `<name>` por el nombre de la memoria caché y `<key>` por su clave principal o secundaria, tal como se ha descrito en la sección anterior, [Recuperación del nombre de host y las claves de acceso](#retrieve-the-host-name-and-access-keys).

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Agregar algo a la memoria caché y recuperarlo

En el ejemplo siguiente se muestra cómo conectarse a una instancia de Caché en Redis de Azure y almacenar y recuperar un elemento de la memoria caché. Para ver más ejemplos del uso de Redis con el cliente [node\_redis](https://github.com/mranney/node_redis), consulte [http://redis.js.org/](http://redis.js.org/).

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

Salida:

	OK
	value


## Pasos siguientes

- [Habilite los diagnósticos de caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el estado de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation) oficial.

<!---HONumber=AcomDC_0817_2016-->