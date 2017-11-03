---
title: "Recuperación ante desastres con localización geográfica de Azure Event Hubs | Microsoft Docs"
description: "Cómo usar regiones geográficas para conmutar por error y llevar a cabo una recuperación ante desastres en Azure Event Hubs"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Recuperación ante desastres con localización geográfica de Azure Event Hubs (versión preliminar)

Cuando los centros de datos regionales experimentan un tiempo de inactividad, es crucial que el procesamiento de datos siga funcionando en otra región o centro de datos. De esta forma, la *recuperación ante desastres con localización geográfica* y la *replicación geográfica* son características importantes para cualquier empresa. Azure Event Hubs admite tanto la recuperación ante desastres con localización geográfica como la replicación geográfica, en el nivel de espacio de nombres. 

La característica de recuperación ante desastres con localización geográfica de Azure Event Hubs es una solución de recuperación ante desastres. Los conceptos y el flujo de trabajo descritos en este artículo se aplican a situaciones catastróficas y no a interrupciones transitorias o temporales.

Para obtener una explicación detallada de la recuperación ante desastres en Microsoft Azure, consulte [este artículo](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminología

**Emparejamiento**: el espacio de nombres principal se conoce como *activo* y recibe los mensajes. El espacio de nombres de conmutación por error es *pasivo* y no recibe mensajes. Los metadatos entre ambos están sincronizados, por lo que ambos pueden aceptar sin problemas mensajes sin ningún cambio de código de la aplicación. Establecer la configuración de recuperación ante desastres entre la región activa y la región pasiva se conoce como *emparejamiento* de las regiones.

**Alias**: un nombre para una configuración de recuperación ante desastres que ha configurado. El alias proporciona una sola cadena de conexión estable de nombre de dominio completo (FQDN). Las aplicaciones usan esta cadena de conexión de alias para conectarse a un espacio de nombres.

**Metadatos**: hace referencia a nombres de concentradores de eventos, grupos de consumidores, particiones, unidades de rendimiento, entidades y propiedades que están asociados con el espacio de nombres.

## <a name="enable-geo-disaster-recovery"></a>Habilitación de la recuperación ante desastres con localización geográfica

Puede habilitar la recuperación de desastres con localización geográfica de Event Hubs en 3 pasos: 

1. Crear un emparejamiento de replicación geográfica, que crea una cadena de conexión de alias y proporciona replicación de metadatos en vivo. 
2. Actualizar las cadenas de conexión de cliente existentes con el alias que se creó en el paso 1.
3. Iniciar la conmutación por error: el emparejamiento de replicación geográfica se interrumpe y el alias apunta al espacio de nombres secundario como su nuevo espacio de nombres principal.

En la siguiente ilustración se muestra este flujo de trabajo:

![Flujo de emparejamiento de replicación geográfica][1] 

### <a name="step-1-create-a-geo-pairing"></a>Paso 1: Creación de un emparejamiento de replicación geográfica

Para crear un emparejamiento entre dos regiones, necesita un espacio de nombres principal y un espacio de nombres secundario. A continuación, cree un alias para formar el par de replicación geográfica. Una vez que los espacios de nombres se emparejan con un alias, los metadatos se replican periódicamente en ambos espacios de nombres. 

El código siguiente muestra cómo hacerlo:

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>Paso 2: Actualización de las cadenas de conexión de cliente existentes

Una vez completado el emparejamiento de la replicación geográfica, las cadenas de conexión que apuntan al espacio de nombres principal deben actualizarse para que apunten a la cadena de conexión del alias. Puede obtener las cadenas de conexión como se muestra en el ejemplo siguiente:

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>Paso 3: Inicio de una conmutación por error

Si se produce un desastre o si decide iniciar una conmutación por error al espacio de nombres secundario, los metadatos y los datos comienzan a fluir al espacio de nombres secundario. Dado que las aplicaciones utilizan las cadenas de conexión de alias, ninguna otra acción es necesaria, ya que automáticamente comienzan a leer y escribir en los centros de eventos en el espacio de nombres secundario. 

El siguiente código muestra cómo desencadenar una conmutación por error:

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

Una vez que se completa la conmutación por error y necesita los datos presentes en el espacio de nombres principal, para extraer los datos debe utilizar una cadena de conexión explícita a los concentradores de eventos en el espacio de nombres principal.

### <a name="other-operations-optional"></a>Otras operaciones (opcionales)

También puede interrumpir el emparejamiento geográfico o eliminar un alias, como se muestra en el código siguiente. Tenga en cuenta que para eliminar una cadena de conexión de alias, debe interrumpir primero el emparejamiento geográfico:

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>Consideraciones para la versión preliminar pública

Tenga en cuenta las siguientes consideraciones para esta versión:

1. La funcionalidad de recuperación ante desastres con localización geográfica solo está disponible en las regiones Centro y norte de EE. UU. y Centro y sur de EE. UU. 
2. La característica solo se admite para los espacios de nombres de nueva creación.
3. Para la versión preliminar, solo está habilitada la replicación de metadatos. No se replican los datos reales.
4. Con la versión preliminar, no hay ningún costo por habilitar la característica. Sin embargo, los espacios de nombres principal y secundario incurrirán en un costo adicional por las unidades de rendimiento reservadas.

## <a name="next-steps"></a>Pasos siguientes

* El [ejemplo en GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) le guía a través de un flujo de trabajo simple que crea un emparejamiento geográfico e inicia una conmutación por error para un escenario de recuperación ante desastres.
* La [referencia de la API de REST](/rest/api/eventhub/disasterrecoveryconfigs) describe las API para llevar a cabo la configuración de recuperación de desastres con localización geográfica.

Para obtener más información acerca de Event Hubs, visite los vínculos siguientes:

* Empiece por un [tutorial de Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)
* [Aplicaciones de ejemplo que usan Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

