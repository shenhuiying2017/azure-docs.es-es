<properties 
	pageTitle="Escalado de Caché en Redis de Azure" 
	description="Obtenga información acerca de cómo ampliar las instancias de Caché en Redis de Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Escalado de Caché en Redis de Azure

>[AZURE.NOTE]Actualmente, la característica de escalado Caché en Redis de Azure está en vista previa.

Caché en Redis de Azure tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché. Si los requisitos de la aplicación cambian después de crear una memoria caché, puede escalar el tamaño de la caché mediante la hoja **cambio de nivel de precios** en el [Portal de Azure](https://portal.azure.com).

>[AZURE.NOTE]Al escalar Caché en Redis de Azure, puede cambiar el tamaño, pero no puede cambiar de una caché Standard a Basic y viceversa.

## Cuándo se debe escalar

Puede utilizar las características de [supervisión](cache-how-to-monitor.md) de Caché en Redis de Azure para supervisar el estado y el rendimiento de las aplicaciones de la memoria caché y para ayudar a determinar si es necesario escalar la memoria caché.

Puede supervisar las métricas siguientes para ayudar a determinar si necesita escalado.

-	Carga de servidor de Redis
-	Uso de la memoria
-	Ancho de banda de red
-	Uso de CPU

Si determina que la memoria caché ya no cumple los requisitos de su aplicación, puede cambiar a un nivel de precios de caché mayor o menor que sea adecuado para su aplicación. Para obtener más información acerca de cómo determinar qué nivel de precios de caché, consulte [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Escalado de una caché
Para escalar la memoria caché, [vaya a la memoria caché](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration) en el [Portal de Azure](https://portal.azure.com) y haga clic en la parte **Nivel Standard** o **nivel Basic** de la hoja **Caché en Redis**.

![Nivel de precios][redis-cache-pricing-tier-part]

Seleccione el nivel deseado de precios desde la hoja **Nivel de precios** y haga clic en **Seleccionar**.

![Nivel de precios][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Las memoria cachés no pueden cambiar los planes de Basic a Standard o viceversa; tampoco se puede reducir a una caché de 250 MB desde uno de los tamaños más grandes. Se puede escalar de una memoria caché de 250 MB a un tamaño mayor, pero no podrá volver al nivel de precios de 250 MB. Si necesita cambiar de Basic a Standard o reducir al tamaño de 250 MB, debe crear una nueva memoria caché.

Mientras la memoria caché se escala al nuevo nivel de precios, se muestra un estado **Escalado** en la hoja **Caché en Redis**.

![Escalado][redis-cache-scaling]

Cuando se completa el escalado, el estado cambia de **Escalado** a **En ejecución**.

>[AZURE.IMPORTANT]Durante las operaciones de escalado, las cachés Basic están desconectadas y se pierden todos los datos de la memoria caché. Una vez finalizada la operación de escalado, la caché Basic vuelve a conectarse, sin datos. Las cachés Standard permanecen en línea durante una operación de escalado y normalmente no se pierde ningún dato al escalar una memoria caché Standard a un tamaño mayor. Si se realiza un escalado de una caché Standard a un tamaño menor, algunos datos pueden perderse si el nuevo tamaño es menor que la cantidad de datos almacenados en caché. Si se pierden datos al reducir, las claves se expulsan mediante el directiva de expulsión [allkeys lru](http://redis.io/topics/lru-cache). Tenga en cuenta que mientras las memorias caché Standard tienen un contrato de nivel de servicio del 99,9% de disponibilidad, no hay ningún contrato de nivel de servicio para la pérdida de datos.

## Automatización de una operación de escalado

Además del escalado de la instancia de Caché en Redis de Azure en el Portal de Azure, se puede escalar mediante las [Bibliotecas de administración de Microsoft Azure (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Para escalar la memoria caché, llame al método `IRedisOperations.CreateOrUpdate` y pase el nuevo tamaño de `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/es-es/library/azure/dn790557.aspx#bk_portal
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Para obtener más información, consulte el ejemplo [Administrar Caché en Redis mediante MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## Preguntas frecuentes de escalado

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Caché en Redis de Azure.

## Después de escalar, ¿tengo que cambiar el nombre de la memoria caché o las teclas de acceso?

No, el nombre de la memoria caché y las claves no se cambian durante una operación de escalado.

## Funcionamiento del escalado

Cuando se escala una memoria caché **Basic**, se cierra y se aprovisiona una nueva caché con el nuevo tamaño. Durante este tiempo, la caché no está disponible y se pierden todos los datos en la memoria caché.

Cuando se escala una memoria caché **Standard**, una de las réplicas se apaga y se vuelve a aprovisionar para el nuevo tamaño y los datos se transfieren a través de ella; a continuación, la otra réplica realiza una conmutación por error antes de volverse a aprovisionar, similar al proceso que se produce durante un error en uno de los nodos de la memoria caché.

## ¿Se pierden los datos de mi memoria caché durante el escalado?

Cuando se escala una memoria caché **Basic**, se pierden todos los datos y la memoria caché no está disponible durante la operación de escalado.

Cuando se escala una memoria caché **Standard** se escala a un tamaño mayor, normalmente todos los datos se conservan. Al reducir una memoria caché **Standard** a un tamaño menor, los datos se pueden perder según la cantidad de datos que se encuentra en la caché en relación con el nuevo tamaño cuando se escala. Si se pierden datos al reducir, las claves se expulsan mediante el directiva de expulsión [allkeys-lru](http://redis.io/topics/lru-cache). Tenga en cuenta que mientras las memorias caché Standard tienen un contrato de nivel de servicio del 99,9% de disponibilidad, no hay ningún contrato de nivel de servicio para la pérdida de datos.

## La caché estará disponible durante el escalado

Las memorias caché **Standard** permanecen disponibles durante la operación de escalado.

Las memorias caché **Basic** están desconectadas durante la operación de escalado.

## Operaciones que no son compatibles

No puede cambiar de una caché **Basic** a una **Standard** o viceversa durante una operación de escalado.

Se puede escalar de una memoria caché **C0** (250 MB) a un tamaño mayor, pero no puede escalar de un tamaño mayor a una memoria caché **C0**.

Si se produce un error en una operación de escalado, el servicio intentará revertir la operación y la memoria caché se restablecerá al tamaño original.

## ¿Cuánto tarda el escalado?

El escalado tarda aproximadamente 20 minutos, según la cantidad de datos que haya en la memoria caché.

## ¿Cómo puedo saber si el escalado ha terminado?

En el portal puede ver la operación de escalado en curso. Cuando se completa el escalado, el estado de la memoria caché cambia de **En ejecución**.

## ¿Por qué esta característica está en vista previa?

Estamos lanzando esta característica para obtener comentarios. Nos basaremos en los comentarios y pronto se lanzará esta característica para Disponibilidad General.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO3-->