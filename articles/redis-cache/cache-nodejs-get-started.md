---
title: Uso de Azure Redis Cache con Node.js | Microsoft Docs
description: "Introducción a Azure Redis Cache usando Node.js y node_redis."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: wesmc
ms.openlocfilehash: e8b4818ad2b876ce4d55cf8aa2aa0e0162dea963
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Uso de Azure Redis Cache con Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Azure Redis Cache mediante Node.js. 

## <a name="prerequisites"></a>requisitos previos
Instale [node_redis](https://github.com/mranney/node_redis):

    npm install redis

En este tutorial se usa [node_redis](https://github.com/mranney/node_redis). Para ver ejemplos del uso de otros clientes de Node.js, consulte la documentación individual de los clientes de Node.js, que encontrará en [clientes Redis de Node.js](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché de Redis en Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperación del nombre de host y las claves de acceso
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Conexión a la caché de forma segura mediante SSL
Las últimas compilaciones de [node_redis](https://github.com/mranney/node_redis) permiten conectarse a Azure Redis Cache mediante SSL. En el ejemplo siguiente se muestra cómo conectarse a Azure Redis Cache con el punto de conexión SSL de 6380. Reemplace `<name>` por el nombre de la memoria caché y `<key>` por su clave principal o secundaria, tal como se ha descrito en la sección anterior, [Recuperación del nombre de host y las claves de acceso](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> El puerto no SSL está deshabilitado para instancias nuevas de Azure Redis Cache. Si usa otro cliente incompatible con SSL, consulte [cómo habilitar el puerto no SSL](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Agregar algo a la memoria caché y recuperarlo
En el ejemplo siguiente se muestra cómo conectarse a una instancia de Azure Redis Cache y almacenar y recuperar un elemento de la memoria caché. Para ver otros ejemplos sobre el uso de Redis con el cliente [node_redis](https://github.com/mranney/node_redis), consulte [http://redis.js.org/](http://redis.js.org/).

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


## <a name="next-steps"></a>pasos siguientes
* [Habilite los diagnósticos de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el estado de la memoria caché.
* Lea la [documentación de Redis](http://redis.io/documentation)oficial.

