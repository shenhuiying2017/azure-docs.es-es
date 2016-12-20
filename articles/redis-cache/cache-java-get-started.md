---
title: Uso de Azure Redis Cache con Java | Microsoft Docs
description: "Introducción a Caché en Redis de Azure usando Java"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/24/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 408026a8e75272cea92ad62e3a75aabaadf98351
ms.openlocfilehash: 00c4d2e31391297955ecba891e919bda65ddaf8e


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Uso de Caché en Redis de Azure con Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Caché en Redis de Azure le proporciona acceso a una caché en Redis dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure mediante Java.

## <a name="prerequisites"></a>Requisitos previos
[Jedis](https://github.com/xetorthio/jedis) - Cliente de Java para Redis

Este tutorial usa Jedis, pero puede usar cualquier cliente de Java enumerado en [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché de Redis en Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperación del nombre de host y las claves de acceso
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-non-ssl-endpoint"></a>Habilitar el extremo no SSL
Algunos clientes de Redis no son compatibles con SSL y, de forma predeterminada, el [puerto no SSL está deshabilitado para las nuevas instancias de Caché en Redis de Azure](cache-configure.md#access-ports). En el momento de redactar este artículo, el cliente de [Jedis](https://github.com/xetorthio/jedis) no es compatible con SSL. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Agregar algo a la memoria caché y recuperarlo
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Pasos siguientes
* [Habilite los diagnósticos de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché.
* Lea la [documentación de Redis](http://redis.io/documentation)oficial.




<!--HONumber=Nov16_HO5-->


