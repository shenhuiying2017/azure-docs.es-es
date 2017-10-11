---
title: Escalado de Azure Redis Cache | Microsoft Docs
description: "Obtenga información acerca de cómo ampliar las instancias de Azure Redis Cache"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: sdanie
ms.openlocfilehash: 91b3580491a1e3504a3891b66606a9bd18c0638f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-scale-azure-redis-cache"></a>Escalado de Azure Redis Cache
Azure Redis Cache tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché. Después de crear una memoria caché, puede ajustar su tamaño y el plan de tarifa si cambian los requisitos de la aplicación. En este artículo se muestra cómo escalar la memoria caché en Azure Portal o con herramientas tales como Azure PowerShell y la CLI de Azure.

## <a name="when-to-scale"></a>Cuándo se debe escalar
Puede utilizar las características de [supervisión](cache-how-to-monitor.md) de Azure Redis Cache para supervisar el estado y el rendimiento de la memoria caché y para ayudar a determinar si es necesario escalarla. 

Puede supervisar las métricas siguientes para ayudar a determinar si necesita escalado.

* Carga de servidor de Redis
* Uso de la memoria
* Ancho de banda de red
* Uso de CPU

Si determina que la memoria caché ya no cumple los requisitos de su aplicación, puede cambiar a un plan de tarifa de caché mayor o menor que sea adecuado para su aplicación. Para obtener más información acerca de cómo determinar qué nivel de precios de caché, consulte [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Escalado de una caché
Para escalar la memoria caché, [vaya a la caché](cache-configure.md#configure-redis-cache-settings) en [Azure Portal](https://portal.azure.com) y haga clic en **Escalar** en el menú **Recurso**.

![Escala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Seleccione el plan de tarifa deseado en la hoja **Seleccionar plan de tarifa** y haga clic en **Seleccionar**.

![Nivel de precios][redis-cache-pricing-tier-blade]


Puede escalar a un plan de tarifa diferente con las siguientes restricciones:

* No se puede escalar desde un plan de tarifa superior a un plan de tarifa inferior.
  * No puede cambiar de una memoria caché **Premium** a una memoria caché **Estándar** o **Básica**.
  * No puede cambiar de una memoria caché **Estándar** a una memoria caché **Básica**.
* Puede cambiar de una memoria caché **Básica** a una memoria caché **Estándar**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado posterior hasta el tamaño deseado.
* No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. Debe escalar desde **Básica** a **Estándar** en una operación de escalado y, después, desde **Estándar** a **Premium** en una operación de escalado posterior.
* No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)** .
 
Durante la operación de escalado de la memoria caché al nuevo plan de tarifa, se muestra el estado **Escalando** en la hoja **Redis Cache**.

![Escalado][redis-cache-scaling]

Cuando se completa el escalado, el estado cambia de **Escalado** a **En ejecución**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatización de una operación de escalado
Además de escalar las instancias de caché en Azure Portal, puede escalar mediante los cmdlets de PowerShell, la CLI de Azure y las Bibliotecas de administración de Microsoft Azure (MAML). 

* [Escalado mediante PowerShell](#scale-using-powershell)
* [Escalado con la CLI de Azure](#scale-using-azure-cli)
* [Escalado mediante MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Escalado mediante PowerShell
Las instancias de Azure Redis Cache se pueden escalar con PowerShell con el cmdlet [AzureRmRedisCache Set](https://msdn.microsoft.com/library/azure/mt634518.aspx) cuando se modifican las propiedades `Size`, `Sku` o `ShardCount`. En el ejemplo siguiente se muestra cómo escalar una memoria caché denominada `myCache` a una caché de 2,5 GB. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obtener más información sobre cómo escalar con PowerShell, consulte [Escalado de una caché de Redis con Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Escalado con la CLI de Azure
Para escalar las instancias de Azure Redis Cache con la CLI de Azure, llame al comando `azure rediscache set` y pase los cambios de configuración que desee que incluyen un nuevo tamaño, sku o tamaño de clúster, dependiendo de la operación de escalado deseada.

Para obtener más información sobre el escalado con la CLI de Azure, consulte [Cambio de la configuración de una caché en Redis existente](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Escalado mediante MAML
Para escalar las instancias de Azure Redis Cache mediante [Microsoft Azure Management Libraries (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), llame al método `IRedisOperations.CreateOrUpdate` y pase el nuevo tamaño para `RedisProperties.SKU.Capacity`.

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

Para obtener más información, consulte el ejemplo [Administrar Caché en Redis mediante MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>Preguntas frecuentes de escalado
La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Azure Redis Cache.

* [¿Puedo realizar operaciones de escalado en una memoria caché Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Después de escalar, ¿tengo que cambiar el nombre de la memoria caché o las teclas de acceso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [¿Cómo funciona el escalado?](#how-does-scaling-work)
* [¿Se pierden los datos de mi memoria caché durante el escalado?](#will-i-lose-data-from-my-cache-during-scaling)
* [¿Mi configuración de bases de datos personalizada se ve afectada durante el escalado?](#is-my-custom-databases-setting-affected-during-scaling)
* [¿La caché estará disponible durante el escalado?](#will-my-cache-be-available-during-scaling)
* [Operaciones que no son compatibles](#operations-that-are-not-supported)
* [¿Cuánto tarda el escalado?](#how-long-does-scaling-take)
* [¿Cómo puedo saber si el escalado ha terminado?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>¿Puedo realizar operaciones de escalado en una memoria caché Premium?
* No puede escalar desde una caché **Premium** a un plan de tarifa **Básico** o **Estándar**.
* Puede escalar desde un plan de tarifa de caché **Premium** a otro.
* No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. La primera operación debe ser escalar primero desde **Básica** a **Estándar** y, después, desde **Estándar** a **Premium** en una operación de escalado posterior.
* Si ha habilitado la agrupación en clústeres cuando creó su caché **Premium**, puede [cambiar el tamaño de clúster](cache-how-to-premium-clustering.md#cluster-size). Si su caché se creó sin habilitar la agrupación en clústeres, no puede configurar la agrupación en clústeres después.
  
  Para obtener más información, consulte [Cómo configurar la agrupación en clústeres para una instancia Premium de Azure Redis Cache](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Después de escalar, ¿tengo que cambiar el nombre de la memoria caché o las teclas de acceso?
No, el nombre de la memoria caché y las claves no se cambian durante una operación de escalado.

### <a name="how-does-scaling-work"></a>¿Cómo funciona el escalado?
* Cuando se escala una memoria caché **Basic** a un tamaño diferente, se cierra y se aprovisiona una nueva caché con el nuevo tamaño. Durante este tiempo, la caché no está disponible y se pierden todos los datos en la memoria caché.
* Cuando se escala una memoria caché del plan **Básico** al plan **Estándar**, se aprovisiona una caché de réplica y los datos se copian desde la caché principal a la de réplica. La memoria caché permanece disponible durante el proceso de escalado.
* Cuando se escala una memoria caché del plan **Estándar** a un tamaño diferente o a una del plan **Premium**, se cierra una de las réplicas y se vuelve a aprovisionar para el nuevo tamaño y los datos se transfieren a través de ella y, después, la otra realiza una conmutación por error antes de volverse a aprovisionar, un proceso que es similar al que se produce durante un error en uno de los nodos de la memoria caché.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>¿Se pierden los datos de mi memoria caché durante el escalado?
* Cuando se escala una memoria caché **Básica** a un nuevo tamaño, se pierden todos los datos y la memoria caché no está disponible durante la operación de escalado.
* Cuando se escala una memoria caché del plan **Básico** al plan **Estándar**, normalmente se conservan los datos de la memoria caché.
* Cuando se escala una memoria caché del plan **Estándar** a un tamaño o plan superior, o cuando una del plan **Premium** se escala a un tamaño superior, normalmente se conservan todos los datos. Si una memoria caché del plan **Estándar** o **Premium** se reduce verticalmente, la posibilidad de que se pierdan los datos depende de la cantidad que haya en la caché, en relación con el nuevo tamaño cuando se realice el escalado. Si se pierden datos al reducir, las claves se expulsan mediante el directiva de expulsión [allkeys-lru](http://redis.io/topics/lru-cache) . 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>¿Mi configuración de bases de datos personalizada se ve afectada durante el escalado?
Algunos planes de tarifa tienen diferentes [límites de bases de datos](cache-configure.md#databases), por lo que es preciso tener en cuenta varios factores al reducir verticalmente si se ha configurado un valor personalizado para el parámetro `databases` al crear la memoria caché.

* Cuando se escala a un plan de tarifa con un límite de `databases` menor que el nivel actual:
  * Si utiliza el número predeterminado de `databases`, que es 16 para todos los planes de tarifa, no se pierden datos.
  * Si utiliza un número personalizado de `databases` que está dentro de los límites del plan al que va a escalar, se mantiene la configuración de `databases` y no se pierden datos.
  * Si utiliza un número personalizado de `databases` que supera los límites del nuevo plan, el parámetro `databases` se reduce a los límites del nuevo plan y se pierden todos los datos de las bases de datos quitadas.
* Cuando se escala a un plan de tarifa con el mismo límite de `databases` o mayor que el plan actual, la configuración de `databases` se mantiene y no se pierden datos.

Tenga en cuenta que mientras las memorias caché Standard y Premium tienen un contrato de nivel de servicio del 99,9% de disponibilidad, no hay ningún contrato de nivel de servicio para la pérdida de datos.

### <a name="will-my-cache-be-available-during-scaling"></a>¿La caché estará disponible durante el escalado?
* Las memorias caché de los planes **Estándar** y **Premium** permanecen disponibles durante la operación de escalado.
* Las memorias caché del plan **Básico** están sin conexión durante las operaciones de escalado a un otro tamaño, pero siguen estando disponibles cuando se escala desde el plan **Básico** al **Estándar**.

### <a name="operations-that-are-not-supported"></a>Operaciones que no son compatibles
* No se puede escalar desde un plan de tarifa superior a un plan de tarifa inferior.
  * No puede cambiar de una memoria caché **Premium** a una memoria caché **Estándar** o **Básica**.
  * No puede cambiar de una memoria caché **Estándar** a una memoria caché **Básica**.
* Puede cambiar de una memoria caché **Básica** a una memoria caché **Estándar**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado posterior hasta el tamaño deseado.
* No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. La primera operación debe ser escalar primero desde **Básica** a **Estándar** y, después, desde **Estándar** a **Premium** en una operación de escalado posterior.
* No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)** .

Si se produce un error en una operación de escalado, el servicio intentará revertir la operación y la memoria caché se restablecerá al tamaño original.

### <a name="how-long-does-scaling-take"></a>¿Cuánto tarda el escalado?
El escalado tarda aproximadamente 20 minutos, según la cantidad de datos que haya en la memoria caché.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>¿Cómo puedo saber si el escalado ha terminado?
En Azure Portal puede ver la operación de escalado en curso. Cuando se completa el escalado, el estado de la memoria caché cambia de **En ejecución**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



