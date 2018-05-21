---
title: ¿Qué es Azure Redis Cache? | Microsoft Docs
description: Aprenda qué es Azure Redis Cache y cómo se utiliza habitualmente.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 8f477282e49104e9b034e11656ff50c2a67545f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-redis-cache"></a>Qué es Azure Redis Cache

Azure Redis Cache se basa en la popular [caché en Redis](https://redis.io/) de código abierto. Normalmente se usa como una memoria caché para mejorar el rendimiento y la escalabilidad de aquellos sistemas que dependen mucho de almacenes de datos back-end. La mejora del rendimiento se logra mediante la copia de los datos a los que se accede con mayor frecuencia en un almacenamiento rápido ubicado cerca de la aplicación. [Redis Cache](https://redis.io/) coloca este almacenamiento rápido en la memoria en lugar de que una base de datos lo cargue desde el disco.

Azure Redis Cache también puede usarse como almacén de estructura de datos en memoria, base de datos no relacional distribuida y agente de mensajes. El rendimiento de las aplicaciones mejora porque se aprovecha de la baja latencia y alto rendimiento del motor Redis.

Azure Redis Cache proporciona acceso a una instancia de Redis Cache segura y dedicada, administrada por Microsoft, hospedada en Azure y accesible a cualquier aplicación dentro de Azure.

## <a name="why-use-azure-redis-cache"></a>Motivos para usar Azure Redis Cache

Hay muchos patrones comunes en los que se usa Redis Cache para admitir la arquitectura de las aplicaciones o para mejorar el rendimiento de las aplicaciones. Estos son algunos de los más comunes:

| Patrón      | DESCRIPCIÓN                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Dado que una base de datos puede ser grande, no se recomienda cargarla toda ella en la memoria caché. Es habitual usar el patrón [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para cargar elementos de datos en la memoria caché solo cuando sea necesario. Cuando el sistema realiza cambios en los datos de back-end, también puede actualizar la caché, que se distribuye con otros clientes. Además, el sistema puede establecer una fecha de expiración en los elementos de datos o usar una directiva de expulsión para que las actualizaciones de los datos se vuelvan a cargar en la memoria caché.|
| [Almacenamiento en caché de contenido](cache-aspnet-output-cache-provider.md) | La mayoría de las páginas web se generan a partir de plantillas con encabezados, pies de página, barras de herramientas, menús, etc. Realmente no cambian con frecuencia y no se deben generar dinámicamente. El uso de una caché en memoria, como Azure Redis Cache, proporcionará a los servidores web acceso rápido a este tipo de contenido estático, en comparación con almacenes de datos de back-end. Este patrón reduce tanto el tiempo de procesamiento como la carga del servidor necesarias para generar contenido dinámicamente, lo que mejora la capacidad de respuesta de los servidores web y le puede permiten reducir el número de servidores necesarios para controlar cargas. Azure Redis Cache proporciona el proveedor de caché de salida de Redis, que ayuda a admitir este patrón con ASP.NET.|
| [Almacenamiento en caché de sesión de usuario](cache-aspnet-session-state-provider.md) | Este patrón se utiliza normalmente con carros de la compra y otra información de tipo de historial de usuario que una aplicación web puede desear asociar con las cookies del usuario. El almacenamiento de demasiados datos en una cookie puede tener un impacto negativo en el rendimiento, ya que aumenta su tamaño y no hay que olvidar que se pasa y se valida con cada solicitud. Una solución habitual es usar la cookie como clave cuando se consultan datos en una base de datos del back-end. El uso de una caché en memoria, como Azure Redis Cache, para asociar información a un usuario es mucho más rápido que su interacción con una base de datos relacional completa. |
| Almacenamiento en cola de trabajos y mensajes | Cuando las aplicaciones reciben solicitudes, a menudo las operaciones asociadas con la solicitud tardan más tiempo en ejecutarse. Es una práctica común para aplazar las operaciones que más tardan en ejecutarse mediante su inclusión en una cola, que se procesa posteriormente, y posiblemente por otro servidor. Este método de aplazar trabajo se denomina puesta en cola de tareas. Hay muchos componentes de software diseñados para admitir colas de tareas. Redis Cache también se usa para este fin, así como una cola distribuida.|
| Transacciones distribuidas | Es un requisito común que las aplicaciones puedan ejecutar una serie de comandos en un almacén de datos de back-end en una única operación (atómica). El resultado de todos los comandos debe ser satisfactorio, o todos deben revertirse al estado inicial. Redis Cache admite la ejecución de un lote de comandos como una única operación en forma de [transacciones](https://redis.io/topics/transactions). |

## <a name="azure-redis-cache-offerings"></a>Ofertas de Azure Redis Cache

Azure Redis Cache está disponible en los niveles siguientes:

| Nivel: | DESCRIPCIÓN |
|---|---|
Básica | Una caché de nodo individual. Este nivel admite varios tamaños de memoria (250 MB - 53 GB). Es un nivel ideal para el desarrollo o la realización de pruebas, así como para cargas de trabajo no críticas. El nivel Básico no tiene ningún Acuerdo de Nivel de Servicio (SLA) |
| Estándar | Una caché replicada en una configuración de dos nodos, principal y secundario, administrada por Microsoft y con un Acuerdo de Nivel de Servicio de alta disponibilidad (99,9 %). |
| Premium | El nivel Premium es el nivel listo para empresas. Las memorias caché de nivel Premium admiten más características y tienen un rendimiento mayor con latencias más bajas. En el nivel Premium, las memorias caché se implementan en un hardware más potente, lo que proporciona un mejor rendimiento en comparación con los niveles Estándar o Básico. Esta ventaja significa que el rendimiento de una caché del mismo tamaño será mayor en el nivel Premium, en comparación con el nivel Estándar |

> [!TIP]
> Para más información acerca del tamaño, la transferencia y el ancho de banda de las memorias caché de nivel Premium, consulte el artículo [P+F de Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).
>

Una vez creada, la memoria caché se puede escalar a un nivel superior. Sin embargo, no se admite su reducción vertical. Para obtener instrucciones detalladas acerca del escalado, consulte [Escalado de Azure Redis Cache](cache-how-to-scale.md) y [Automatización de una operación de escalado](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Comparación de características

En la página de [precios de Redis Cache](https://azure.microsoft.com/pricing/details/cache/) encontrará una comparación detallada de cada nivel. La tabla siguiente le ayuda a describir algunas de las características que admite cada nivel:

| Descripción de la característica | Premium | Estándar | Básica |
| ------------------- | :-----: | :------: | :---: |
| [Acuerdo de Nivel de Servicio (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistencia de datos de Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Clúster en Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Seguridad a través de las reglas de firewall](cache-configure.md#firewall) |✔|✔|✔|
| [Aislamiento y seguridad mejorados con VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Programación de actualizaciones](cache-administration.md#schedule-updates) |✔|-|-|
| [Replicación geográfica](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Pasos siguientes

* [Inicio rápido de una aplicación web de ASP.NET](cache-web-app-howto.md) Cree una aplicación web de ASP.NET simple que use Azure Redis Cache.
* [Inicio rápido de .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Cree una aplicación .NET que use Azure Redis Cache.
* [Inicio rápido de Node.js](cache-nodejs-get-started.md) Cree una aplicación Node.js simple que use Azure Redis Cache.
* [Inicio rápido de Java](cache-java-get-started.md) Cree una aplicación Java simple que use Azure Redis Cache.
* [Inicio rápido de Python](cache-python-get-started.md) Cree una aplicación Python que use Azure Redis Cache.