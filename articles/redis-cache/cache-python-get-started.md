<properties
	pageTitle="Uso de Caché en Redis de Azure con Python | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Python.


## Requisitos previos

Instale [redis-py](https://github.com/andymccurdy/redis-py).


## Crear una caché de Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperación del nombre de host y las claves de acceso

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Habilitar el extremo no SSL

Algunos clientes de Redis no son compatibles con SSL y, de forma predeterminada, el [puerto no SSL está deshabilitado para las nuevas instancias de Caché en Redis de Azure](cache-configure.md#access-ports). En el momento de redactar este artículo, el cliente de [redis-py](https://github.com/andymccurdy/redis-py) no es compatible con SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Agregar algo a la memoria caché y recuperarlo


	>>> import redis
	>>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
	      port=6380, db=0, password='<key>', ssl=True)
	>>> r.set('foo', 'bar')
	True
	>>> r.get('foo')
	b'bar'


Reemplace `<name>` por el nombre de la memoria caché y `key` por la clave de acceso.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0601_2016-->