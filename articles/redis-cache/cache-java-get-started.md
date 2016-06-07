<properties
   pageTitle="Uso de Caché en Redis de Azure con Java | Microsoft Azure"
	description="Introducción a Caché en Redis de Azure usando Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure con Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Caché en Redis de Azure le proporciona acceso a una caché en Redis dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Java.

## Requisitos previos

[Jedis](https://github.com/xetorthio/jedis) - Cliente de Java para Redis

Este tutorial usa Jedis, pero puede usar cualquier cliente de Java enumerado en [http://redis.io/clients](http://redis.io/clients).

## Crear una caché de Redis en Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperación del nombre de host y las claves de acceso

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Habilitar el extremo no SSL

Algunos clientes de Redis no son compatibles con SSL y, de forma predeterminada, el [puerto no SSL está deshabilitado para las nuevas instancias de Caché en Redis de Azure](cache-configure.md#access-ports). En el momento de redactar este artículo, el cliente de [Jedis](https://github.com/xetorthio/jedis) no es compatible con SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## Agregar algo a la memoria caché y recuperarlo

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


## Pasos siguientes

- [Habilite los diagnósticos de caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation) oficial.

<!---HONumber=AcomDC_0601_2016-->