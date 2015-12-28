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
	ms.date="12/11/2015" 
	ms.author="sdanie"/>

# Escalado de Caché en Redis de Azure

>[AZURE.NOTE]Actualmente, la característica de escalado Caché en Redis de Azure está en vista previa. Durante el período de vista previa, no se puede escalar a una caché de nivel premium o desde ella, pero puede cambiar el plan de tarifa dentro de una caché premium.

Caché en Redis de Azure tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché. Si los requisitos de la aplicación cambian después de crear una memoria caché, puede escalar el tamaño de la caché mediante la hoja **Cambio de nivel de precios** del [Portal de Azure](https://portal.azure.com).

## Cuándo se debe escalar

Puede utilizar las características de [supervisión](cache-how-to-monitor.md) de Caché en Redis de Azure para supervisar el estado y el rendimiento de las aplicaciones de la memoria caché y para ayudar a determinar si es necesario escalar la memoria caché.

Puede supervisar las métricas siguientes para ayudar a determinar si necesita escalado.

-	Carga de servidor de Redis
-	Uso de la memoria
-	Ancho de banda de red
-	Uso de CPU

Si determina que la memoria caché ya no cumple los requisitos de su aplicación, puede cambiar a un nivel de precios de caché mayor o menor que sea adecuado para su aplicación. Para obtener más información acerca de cómo determinar qué nivel de precios de caché, consulte [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Escalado de una caché
Para escalar la memoria caché, [vaya a la memoria caché](cache-configure.md#configure-redis-cache-settings) en el [Portal de Azure](https://portal.azure.com) y haga clic en **Configuración**, **Plan de tarifa**.

También puede hacer clic en la parte **Nivel estándar** o **Nivel básico** de la hoja **Caché en Redis**.

![Nivel de precios][redis-cache-pricing-tier-part]

Seleccione el nivel deseado de precios desde la hoja **Nivel de precios** y haga clic en **Seleccionar**.

![Nivel de precios][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Puede escalar a un nivel de precios diferente con las siguientes restricciones.
>
>-	No puede escalar a una memoria caché de nivel **Premium** o desde esta.
>-	No puede escalar de una memoria caché **Standard** a una **Basic**.
>-	Puede escalar desde una memoria caché **Basic** a una memoria caché **Standard**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado posterior hasta el tamaño deseado.
>-	No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)**.

Mientras la memoria caché se escala al nuevo nivel de precios, se muestra un estado **Escalado** en la hoja **Caché en Redis**.

![Escalado][redis-cache-scaling]

Cuando se completa el escalado, el estado cambia de **Escalado** a **En ejecución**.

## Automatización de una operación de escalado

Además del escalado de la instancia de Caché en Redis de Azure en el Portal de Azure, puede escalar mediante las [Bibliotecas de administración de Microsoft Azure (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Para escalar la memoria caché, llame al método `IRedisOperations.CreateOrUpdate` y pase el nuevo tamaño de `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
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

## ¿Puedo realizar operaciones de escalado en una memoria caché Premium?

-	No puede escalar a un plan de tarifa de caché **Premium** desde planes de tarifa **Básico** o **Estándar**.
-	No puede escalar desde una caché **Premium** a un plan de tarifa **Básico** o **Estándar**.
-	Puede escalar de un plan de tarifa de caché **Premium** a otro.
-	Si ha habilitado la agrupación en clústeres cuando creó su caché **Premium**, puede escalar el número de particiones hacia arriba o hacia abajo.

Para más información, vea [Cómo configurar la agrupación en clústeres de Redis para una Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).

## Después de escalar, ¿tengo que cambiar el nombre de la memoria caché o las teclas de acceso?

No, el nombre de la memoria caché y las claves no se cambian durante una operación de escalado.

## Funcionamiento del escalado

Cuando se escala una memoria caché **Basic** a un tamaño diferente, se cierra y se aprovisiona una nueva caché con el nuevo tamaño. Durante este tiempo, la caché no está disponible y se pierden todos los datos en la memoria caché.

Cuando se escala una memoria caché **Basic** a una memoria caché **Standard**, se aprovisiona una caché de réplica y los datos se copian desde la caché principal a la caché de réplica. La memoria caché permanece disponible durante el proceso de escalado.

Cuando se escala una memoria caché **Standard** a un tamaño diferente, se apaga una de las réplicas y se vuelve a aprovisionar para el nuevo tamaño y los datos se transfieren a través de ella; a continuación, la otra réplica realiza una conmutación por error antes de volverse a aprovisionar, similar al proceso que se produce durante un error en uno de los nodos de la memoria caché.

## ¿Se pierden los datos de mi memoria caché durante el escalado?

Cuando se escala una memoria caché **Basic** a un nuevo tamaño, se pierden todos los datos y la memoria caché no está disponible durante la operación de escalado.

Cuando se escala una memoria caché **Basic** a una memoria caché **Standard**, normalmente se conservan los datos de la memoria caché.

Cuando se escala una memoria caché **Standard** a un tamaño mayor, normalmente se conservan todos los datos. Al reducir una memoria caché **Standard** a un tamaño menor, los datos se pueden perder según la cantidad de datos que se encuentra en la caché en relación con el nuevo tamaño cuando se escala. Si se pierden datos al reducir, las claves se expulsan mediante el directiva de expulsión [allkeys-lru](http://redis.io/topics/lru-cache).

Tenga en cuenta que mientras las memorias caché Standard y Premium tienen un contrato de nivel de servicio del 99,9% de disponibilidad, no hay ningún contrato de nivel de servicio para la pérdida de datos.

## La caché estará disponible durante el escalado

Las memorias caché **Standard** permanecen disponibles durante la operación de escalado.

Las memorias caché **Basic** están sin conexión durante las operaciones de escalado a un tamaño distinto, pero siguen estando disponibles cuando se escala desde **Basic** a **Standard**.

## Operaciones que no son compatibles

No puede escalar a una memoria caché de nivel **Premium** o desde esta.

No puede cambiar de una memoria caché **Standard** a una **Basic**.

Puede escalar desde una memoria caché **Basic** a una memoria caché **Standard**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado posterior hasta el tamaño deseado.

Se puede escalar de una memoria caché **C0** (250 MB) a un tamaño mayor, pero no puede escalar de un tamaño mayor a una memoria caché **C0**.

Si se produce un error en una operación de escalado, el servicio intentará revertir la operación y la memoria caché se restablecerá al tamaño original.

## ¿Cuánto tarda el escalado?

El escalado tarda aproximadamente 20 minutos, según la cantidad de datos que haya en la memoria caché.

## ¿Cómo puedo saber si el escalado ha terminado?

En el Portal de Azure puede ver la operación de escalado en curso. Cuando se completa el escalado, el estado de la memoria caché cambia de **En ejecución**.

## ¿Por qué esta característica está en vista previa?

Estamos lanzando esta característica para obtener comentarios. Nos basaremos en los comentarios y pronto se lanzará esta característica para Disponibilidad General.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=AcomDC_1217_2015-->