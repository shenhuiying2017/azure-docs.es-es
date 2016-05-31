<properties
	pageTitle="Uso de Caché en Redis de Azure con Node.js | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Node.js y node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/24/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Caché en Redis de Azure le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Node.js. Para obtener otro ejemplo de uso de Caché en Redis de Azure con Node.js, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Requisitos previos

Instale [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node\_redis](https://github.com/mranney/node_redis), pero puede usar cualquier cliente de Node.js enumerado en [http://redis.io/clients](http://redis.io/clients).

## Crear una caché de Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperación del nombre de host y las claves de acceso

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Habilitar el extremo no SSL

Algunos clientes de Redis no son compatibles con SSL y, de forma predeterminada, el [puerto no SSL está deshabilitado para las nuevas instancias de Caché en Redis de Azure](cache-configure.md#access-ports). En el momento de redactar este artículo, el cliente de [node\_redis](https://github.com/mranney/node_redis) no es compatible con SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Agregar algo a la memoria caché y recuperarlo

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

<!---HONumber=AcomDC_0525_2016-->