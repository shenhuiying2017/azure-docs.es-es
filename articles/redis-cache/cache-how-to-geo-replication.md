---
title: "Configuración de replicación geográfica para Azure Redis Cache | Microsoft Docs"
description: "Obtenga información sobre cómo replicar las instancias de Azure Redis Cache en distintas regiones geográficas."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Configuración de replicación geográfica para Azure Redis Cache

La replicación geográfica proporciona un mecanismo para vincular dos instancias de Azure Redis Cache de nivel Premium. Una memoria caché se designa como la caché vinculada principal y la otra, como la caché vinculada secundaria. La caché vinculada secundaria pasa a ser de solo lectura, por lo que los datos escritos en la caché principal se replican en la caché vinculada secundaria. Esta funcionalidad se puede usar para replicar una caché en varias regiones de Azure. En este artículo se proporciona una guía para configurar la replicación geográfica para las instancias de Azure Redis Cache de nivel Premium.

## <a name="geo-replication-prerequisites"></a>Requisitos previos de la replicación geográfica

Para configurar la replicación geográfica entre dos cachés, se deben cumplir los siguientes requisitos:

- Ambas debe ser cachés de [nivel Premium](cache-premium-tier-intro.md).
- Ambas cachés deben estar en la misma suscripción de Azure.
- La caché vinculada secundaria debe tener el mismo plan de tarifa o un plan de tarifa mayor que la caché vinculada principal.
- Si la caché vinculada principal tiene habilitada la agrupación en clústeres, la caché vinculada secundaria debe tener habilitada la agrupación en clústeres con el mismo número de particiones que la caché vinculada principal.
- Ambas cachés deben estar creadas y en ejecución.
- La persistencia no debe estar habilitada en ninguna de las cachés.
- Se admite la replicación geográfica entre cachés de la misma VNET. También se admite la replicación geográfica entre cachés en distintas VNET, siempre que ambas VNET estén configurada de manera que los recursos en ellas puedan tener contacto entre sí a través de conexiones TCP.

Una vez que se configura la replicación geográfica, se aplican las siguientes restricciones al par de cachés vinculadas:

- La caché vinculada secundaria es de solo lectura, porque puede leer de ella, pero no puede escribir datos. 
- Se quitarán los datos existentes en la caché vinculada secundaria antes de la vinculación. Sin embargo, si la replicación geográfica se quita posteriormente, los datos replicados permanecen en la caché vinculada secundaria.
- No puede iniciar una [operación de escalado](cache-how-to-scale.md) en ninguna de las caché ni [cambiar el número de particiones](cache-how-to-premium-clustering.md) si la caché tiene habilitada la agrupación en clústeres.
- No puede habilitar la persistencia en ninguna de las cachés.
- Puede usar la [exportación](cache-how-to-import-export-data.md#export) con cualquiera de las cachés, pero solo puede [importar](cache-how-to-import-export-data.md#import) en la caché vinculada principal.
- No se puede eliminar la memoria caché vinculada, ni el grupo de recursos que las contiene, hasta que se quite el vínculo de replicación geográfica. Para más información, vea [¿Por qué no se pudo realizar la operación al intentar eliminar la memoria caché vinculada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Si ambas cachés están en regiones distintas, se aplicarán costos de salida de la red a los datos replicados entre las regiones a la caché vinculada secundaria. Para más información, consulte el artículo sobre [cuánto cuesta replicar datos entre regiones de Azure](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions).
- No hay conmutación automática por error a la caché vinculada secundaria si la caché principal (y su réplica) deja de funcionar. Para realizar conmutación por error de aplicaciones cliente, debería quitar manualmente el vínculo de replicación geográfica y dirigir las aplicaciones cliente a la caché que anteriormente era la caché vinculada secundaria. Para más información, consulte [cómo funciona la conmutación por error a la caché vinculada secundaria](#how-does-failing-over-to-the-secondary-linked-cache-work).

## <a name="add-a-geo-replication-link"></a>Agregar un vínculo de replicación geográfica

1. Para vincular dos cachés Premium para realizar la replicación geográfica, haga clic en **Replicación geográfica** en el menú Recurso de la caché pensada como la caché vinculada principal y, luego, haga clic en **Add cache replication link** (Agregar vínculo de replicación de caché) en la hoja **Replicación geográfica**.

    ![Agregar vínculo](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Haga clic en el nombre de la caché secundaria deseada en la lista **Compatible caches** (Cachés compatibles). Si la caché deseada no aparece en la lista, compruebe que se cumplen los [requisitos previos de replicación geográfica](#geo-replication-prerequisites) para la caché secundaria deseada. Para filtrar las cachés por región, haga clic en la región deseada en el mapa para mostrar solo esas cachés en la lista de **cachés disponibles**.

    ![Cachés compatibles de replicación geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    También puede iniciar el proceso de vinculación o ver detalles sobre la caché secundaria mediante el menú contextual.

    ![Menú contextual de replicación geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Haga clic en **Vincular** para vincular las dos cachés y comenzar el proceso de replicación.

    ![Vincular cachés](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Puede ver el progreso del proceso de replicación en la hoja **Replicación geográfica**.

    ![Estado de la vinculación](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    También puede consultar el estado de la vinculación en la hoja de **información general** para ambas cachés, la principal y la secundaria.

    ![Estado de cachés](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Una vez que se completa el proceso de replicación, el **estado del vínculo** cambia a **Correcto**.

    ![Estado de cachés](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Durante el proceso de vinculación, la caché vinculada principal sigue disponible para su uso, pero la caché vinculada secundaria no está disponible hasta que se complete el proceso de vinculación.

## <a name="remove-a-geo-replication-link"></a>Quitar un vínculo de replicación geográfica

1. Para quitar el vínculo entre dos cachés y detener la replicación geográfica, haga clic en **Unlink caches** (Desvincular cachés) en la hoja **Replicación geográfica**.
    
    ![Desvincular cachés](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Una vez que se completa el proceso de desvinculación, la caché secundaria queda disponible tanto para lecturas como para escrituras.

>[!NOTE]
>Cuando se quita el vínculo de replicación geográfica, los datos replicados de la caché vinculada principal se mantienen en la caché secundaria.
>
>

## <a name="geo-replication-faq"></a>Preguntas más frecuentes sobre la replicación geográfica

- [¿Puedo usar la replicación geográfica con una caché de nivel Estándar o Básico?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [¿La caché está disponible para usarla durante los procesos de vinculación o desvinculación?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [¿Puedo vincular más de dos cachés?](#can-i-link-more-than-two-caches-together)
- [¿Puedo vincular dos cachés de distintas suscripciones de Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [¿Puedo vincular dos cachés de tamaños distintos?](#can-i-link-two-caches-with-different-sizes)
- [¿Puedo usar la replicación geográfica con agrupación en clústeres habilitada?](#can-i-use-geo-replication-with-clustering-enabled)
- [¿Puedo usar la replicación geográfica con cachés en una VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [¿Qué es la programación de replicación para la replicación geográfica de Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [¿Cuánto tiempo tarda la replicación geográfica?](#how-long-does-geo-replication-replication-take)
- [¿Se garantiza el punto de recuperación de replicación?](#is-the-replication-recovery-point-guaranteed)
- [¿Puedo usar PowerShell o la CLI de Azure para administrar la replicación geográfica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [¿Cuánto cuesta replicar datos entre regiones de Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [¿Por qué no se pudo realizar la operación al intentar eliminar la memoria caché vinculada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [¿Qué región se debe usar para la caché vinculada secundaria?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [¿Cómo funciona la conmutación por error a la caché vinculada secundaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>¿Puedo usar la replicación geográfica con una caché de nivel Estándar o Básico?

No, la replicación geográfica solo está disponible para las cachés de nivel Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>¿La caché está disponible para usarla durante los procesos de vinculación o desvinculación?

- Cuando se vinculan dos cachés para realizar la replicación geográfica, la caché vinculada principal sigue disponible para su uso, pero la caché vinculada secundaria no está disponible hasta que se complete el proceso de vinculación.
- Cuando se quita el vínculo de replicación geográfica entre dos cachés, ambas siguen disponibles para su uso.

### <a name="can-i-link-more-than-two-caches-together"></a>¿Puedo vincular más de dos cachés?

No, solo puede vincular dos cachés cuando se usa la replicación geográfica.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>¿Puedo vincular dos cachés de distintas suscripciones de Azure?

No, ambas cachés deben estar en la misma suscripción de Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>¿Puedo vincular dos cachés de tamaños distintos?

Sí, siempre que la caché vinculada secundaria sea mayor que la caché vinculada principal.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>¿Puedo usar la replicación geográfica con agrupación en clústeres habilitada?

Sí, siempre que ambas cachés tengan la misma cantidad de particiones.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>¿Puedo usar la replicación geográfica con cachés en una VNET?

Sí, se admite la replicación geográfica de cachés en VNET. 

- Se admite la replicación geográfica entre cachés de la misma VNET.
- También se admite la replicación geográfica entre cachés en distintas VNET, siempre que ambas VNET estén configurada de manera que los recursos en ellas puedan tener contacto entre sí a través de conexiones TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>¿Qué es la programación de replicación para la replicación geográfica de Redis?

La replicación no se realiza en una programación específica, es continua y asincrónica, es decir todas las escrituras realizadas en el servidor principal se replican asincrónicamente al instante en el servidor secundario.

### <a name="how-long-does-geo-replication-replication-take"></a>¿Cuánto tiempo tarda la replicación geográfica?

La replicación es incremental, asincrónica y continua, y el tiempo empleado no suele ser muy diferente de la latencia entre regiones. En determinadas circunstancias y en ciertos momentos, el servidor secundario puede requerir la realización de una sincronización completa de los datos del servidor principal. El tiempo de replicación en este caso es depende del número de factores, tales como carga en la memoria caché principal, el ancho de banda disponible en la máquina de la memoria caché o la latencia entre regiones, entre otros. Por ejemplo, en función de algunas pruebas, hemos encontrado que el tiempo de replicación para un par con replicación geográfica de 53 GB completa en las regiones Este de EE. UU. y Oeste de EE. UU. puede ser cualquier valor entre 5 y 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>¿Se garantiza el punto de recuperación de replicación?

Actualmente, para las memorias caché en modo de replicación geográfica, la funcionalidad de importación y exportación y de persistencia está deshabilitada. Por tanto, en caso de una conmutación por error iniciada por un cliente o en casos donde un vínculo de replicación se ha interrumpido entre el par de replicación geográfica, el servidor secundario conservará los datos en memoria que ha sincronizado desde el servidor principal hasta un momento dado. No hay ninguna garantía de punto de recuperación proporcionada en estas situaciones.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>¿Puedo usar PowerShell o la CLI de Azure para administrar la replicación geográfica?

En este momento, solo puede administrar la replicación geográfica mediante Azure Portal.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>¿Cuánto cuesta replicar datos entre regiones de Azure?

Cuando usa la replicación geográfica, los datos de la caché vinculada principal se replican a la caché vinculada secundaria. Si las dos cachés vinculadas están en la misma región de Azure, no hay ningún cargo por la transferencia de datos. Si las dos cachés vinculadas se encuentran en regiones de Azure distintas, el cargo por transferencia de datos de replicación geográfica es el costo de ancho de banda por replicar esos datos a la otra región de Azure. Para más información, consulte [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>¿Por qué no se pudo realizar la operación al intentar eliminar la memoria caché vinculada?

Cuando hay dos cachés vinculadas entre sí, no se puede eliminar ninguna de ellas ni el grupo de recursos que las contiene hasta que se quite el vínculo de replicación geográfica. Si intenta eliminar el grupo de recursos que contiene una o las dos cachés vinculadas, se eliminan los otros recursos del grupo de recursos, pero el grupo de recursos permanece en el estado `deleting` y las cachés en el grupo de recursos permanecen en el estado `running`. Para completar la eliminación del grupo de recursos y las cachés vinculadas dentro de él, rompa el vínculo de replicación geográfica, como se describe en [Quitar un vínculo de replicación geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>¿Qué región se debe usar para la caché vinculada secundaria?

En general, se recomienda que la caché esté en la misma región de Azure que la aplicación que accede a ella. Si la aplicación tiene una región principal y de reserva, las cachés principal y secundaria deben existir en esas mismas regiones. Para más información sobre regiones emparejadas, consulte el artículo sobre [procedimientos recomendados: regiones emparejadas de Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>¿Cómo funciona la conmutación por error a la caché vinculada secundaria?

En la versión inicial de la replicación geográfica, Azure Redis Cache no admite la conmutación automática por error entre regiones de Azure. La replicación geográfica se usa principalmente en un escenario de recuperación ante desastres. En un escenario de recuperación ante desastres, los clientes deben abordar toda la pila de aplicaciones en una región de copia de seguridad de manera coordinada, en lugar de permitir que cada componente de la aplicación decida por sí mismo cuándo cambiar a sus copias de seguridad. Esto resulta especialmente relevante para Redis. Uno de los beneficios clave de Redis es que se trata de un almacén de latencia muy baja. Si la instancia de Redis que una aplicación usa realiza la conmutación por error a una región de Azure distinta pero el nivel de proceso no, el tiempo del recorrido de ida y vuelta tendría un evidente impacto en el rendimiento. Por este motivo, nos gustaría evitar que Redis realice automáticamente la conmutación por error debido a problemas transitorios de disponibilidad.

Actualmente, para iniciar la conmutación por error, es necesario quitar el vínculo de replicación geográfica en Azure Portal y, luego, cambiar el punto de conexión del cliente Redis de la caché vinculada principal a la caché secundaria (anteriormente vinculada). Cuando se elimina la asociación de ambas cachés, la réplica se vuelve a convertir en una caché normal de lectura y escritura y acepta solicitudes directamente de los clientes Redis.


## <a name="next-steps"></a>pasos siguientes

Más información sobre el [nivel Premium de Azure Redis Cache](cache-premium-tier-intro.md).

