---
title: Bases de datos de Azure Government | Microsoft Docs
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Bases de datos de Azure Government
## <a name="sql-database"></a>Base de datos SQL
Consulte <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Security Center para el motor de SQL Database </a> y [ Documentación pública de Azure SQL Database ](../sql-database/index.md) para obtener instrucciones adicionales sobre la configuración de visibilidad de metadatos y los procedimientos recomendados de protección.

### <a name="variations"></a>Variaciones
SQL V12 Database está disponible con carácter general en Azure Government.

La dirección para los servidores de SQL Azure en Azure Government es diferente:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| Base de datos SQL |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>Consideraciones
La siguiente información identifica el límite de Azure Government para Azure SQL:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Todos los datos almacenados y procesados en Microsoft Azure SQL pueden contener datos regulados por Azure Government. Debe utilizar herramientas de base de datos para la transferencia de datos regulados por Azure Government. |Los metadatos de SQL de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento.  No inserte datos regulados o controlados en los siguientes campos: nombres de base de datos, nombres de suscripciones, grupos de recursos, inicios de sesión de administrador, nombres de implementaciones, nombres de recursos o etiquetas de recursos. |

## <a name="azure-redis-cache"></a>Caché en Redis de Azure
Para más información sobre este servicio y cómo usarlo, consulte la [documentación pública de Azure Redis Cache](../redis-cache/index.md).

### <a name="variations"></a>Variaciones
Las direcciones URL para acceder a Azure Redis Cache y administrarlo en Azure Government son diferentes:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| Punto de conexión de caché |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Portal de Azure |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> Todos los scrips y todo el código deben tener en cuenta los puntos de conexión y entornos adecuados. Para más información, consulte [Conexión a la nube de Azure Government](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).
> 
> 

### <a name="considerations"></a>Consideraciones
La información siguiente identifica el límite de Azure Government para Azure Redis Cache:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Todos los datos almacenados y procesados en Azure Redis Cache pueden contener datos regulados por Azure Government. |Los metadatos de Azure Redis Cache no pueden contener datos controlados para exportación. No escriba datos regulados o controlados en los siguientes campos: Nombre de caché, Nombre de VNET, Nombre de suscripción, Grupos de recursos, Etiquetas de recursos y Propiedades de Redis. |

## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government</a>.




<!--HONumber=Nov16_HO3-->


