---
title: Niveles de rendimiento retirados de Azure Cosmos DB | Microsoft Docs
description: "Obtenga información acerca de los niveles de rendimiento S1, S2 y S3 que estaban disponibles anteriormente en Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4cab6297e45dc7a14826d7bb10cf22a8a300355f
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Retirada de los niveles de rendimiento S1, S2 y S3

> [!IMPORTANT] 
> Los niveles de rendimiento S1, S2 y S3 descritos en este artículo se han retirado y ya no están disponibles para las nuevas cuentas de Azure Cosmos DB.
>

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

En este artículo se proporciona información general de los niveles de rendimiento S1, S2 y S3, y se describe cómo se migrarán las colecciones que usan estos niveles de rendimiento a colecciones de partición única a finales de 2017. Después de leer este artículo, podrá responder a las preguntas siguientes:

- [¿Por qué se han retirado los niveles de rendimiento S1, S2 y S3?](#why-retired)
- [¿Cómo se comparan las colecciones de partición única y con particiones con los niveles de rendimiento S1, S2 y S3?](#compare)
- [¿Qué tengo que hacer para garantizar el acceso ininterrumpido a mis datos?](#uninterrupted-access)
- [¿Cómo cambiará mi colección después de la migración?](#collection-change)
- [¿Cómo cambiará mi facturación después migrar a colecciones de partición única?](#billing-change)
- [¿Qué ocurre si necesito más de 10 GB de almacenamiento?](#more-storage-needed)
- [¿Puedo cambiar entre los niveles de rendimiento S1, S2 y S3 antes de la migración planeada?](#change-before)
- [¿Cómo puedo saber cuándo ha migrado mi colección?](#when-migrated)
- [¿Cómo migro de los niveles de rendimiento S1, S2 y S3 a las colecciones de partición única por mí mismo?](#migrate-diy)
- [¿Cómo me afecta si soy un cliente de EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>¿Por qué se han retirado los niveles de rendimiento S1, S2 y S3?

Los niveles de rendimiento S1, S2 y S3 no ofrecen la flexibilidad que proporciona la oferta estándar de Azure Cosmos DB. Con los niveles de rendimiento S1, S2 y S3, tanto la capacidad de rendimiento como la de almacenamiento están preestablecidas y no ofrecen elasticidad. Ahora Azure Cosmos DB ofrece la capacidad para personalizar su rendimiento y almacenamiento, lo que ofrece mucha más flexibilidad para el escalado a medida que cambien sus necesidades.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>¿Cómo se comparan las colecciones de partición única y con particiones con los niveles de rendimiento S1, S2 y S3?

En la tabla siguiente se comparan las opciones de rendimiento y almacenamiento disponibles en las colecciones de partición única, las colecciones con particiones y los niveles de rendimiento S1, S2 y S3. Este es un ejemplo para la región Este de EE. UU. - 2:

|   |Colección con particiones|Colección de partición única|S1|S2|S3|
|---|---|---|---|---|---|
|Rendimiento máximo|Ilimitado|10 000 RU/s|250 RU/s|1000 RU/s|2500 RU/s|
|Rendimiento mínimo|2500 RU/s|400 RU/s|250 RU/s|1000 RU/s|2500 RU/s|
|Almacenamiento máximo|Ilimitado|10 GB|10 GB|10 GB|10 GB|
|Precio (mensual)|Rendimiento: 6 USD / 100 RU/s<br><br>Almacenamiento: 0,25 USD/GB|Rendimiento: 6 USD / 100 RU/s<br><br>Almacenamiento: 0,25 USD/GB|25 USD|50 USD|100 USD|

¿Es un cliente de EA? Si es así, consulte [¿Cómo me afecta si soy un cliente de EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>¿Qué tengo que hacer para garantizar el acceso ininterrumpido a mis datos?

Nada, Cosmos DB se ocupa de la migración por usted. Si tiene una colección de S1, S2 o S3, se migrará a una colección de partición única a finales de 2017. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>¿Cómo cambiará mi colección después de la migración?

Si tiene una colección de S1, se migrará a una colección de partición única con un rendimiento de 400 RU/s. 400 RU/s es el rendimiento más bajo disponible con colecciones de partición única. Sin embargo, el costo de 400 RU/s en la colección de partición única es aproximadamente el mismo que con la colección de S1 y 250 RU/s, por lo que no pagará las 150 RU/s extra disponibles.

Si tiene una colección de S2, se migrará a una colección de partición única con 1000 RU/s. No verá ningún cambio en su nivel de rendimiento.

Si tiene una colección de S3, se migrará a una colección de partición única con 2 500 RU/s. No verá ningún cambio en su nivel de rendimiento.

En todos estos casos, después de migrar la colección, podrá personalizar el nivel de rendimiento o escalar y reducir verticalmente según sea necesario para proporcionar un acceso de baja latencia a los usuarios. Para cambiar el nivel de rendimiento después de migrar la colección, abra su cuenta de Cosmos DB en Azure Portal, haga clic en Escala, elija la colección y ajuste el nivel de rendimiento, como se muestra en la siguiente captura de pantalla:

![Escalado del rendimiento en Azure Portal](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>¿Cómo cambiará mi facturación después migrar a colecciones de partición única?

Supongamos que tiene 10 colecciones S1 en la región Este de EE. UU. y 1 GB de almacenamiento para cada una, y las migra a 10 recopilaciones de partición única a 400 RU/seg. (el nivel mínimo). Su factura tendrá el siguiente aspecto si mantiene las 10 colecciones de partición única un mes completo:

![Cómo se comparan los precios de S1 para 10 colecciones con los precios de 10 colecciones que usan una colección de partición única](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>¿Qué ocurre si necesito más de 10 GB de almacenamiento?

Independientemente de si tiene una colección con un nivel de rendimiento S1, S2 o S3, o si tiene una colección de partición única, todos con 10 GB de almacenamiento disponibles, puede utilizar la herramienta de migración de datos de Cosmos DB para migrar los datos a una colección con particiones, con un almacenamiento prácticamente ilimitado. Para más información acerca de las ventajas de una colección con particiones, consulte el tema sobre [particiones y escalado en Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>¿Puedo cambiar entre los niveles de rendimiento S1, S2 y S3 antes de la migración planeada?

Solo las cuentas existentes con los niveles S1, S2 y S3 podrán cambiar y modificar los niveles de rendimiento con el portal o mediante programación. Si cambia de S1, S3 o S3 a una colección de partición única, no puede volver a los niveles de rendimiento S1, S2 o S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>¿Cómo puedo saber cuándo ha migrado mi colección?

La migración se producirá a finales de 2017. Si tiene una colección que usa los niveles de rendimiento S1, S2 o S3, el equipo de Cosmos DB se comunicará con usted por correo electrónico antes de realizar la migración. Una vez completada, Azure Portal indicará que su colección utiliza el plan de tarifa Estándar.

![Procedimientos para confirmar que la colección se ha migrado al plan de tarifa Estándar](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>¿Cómo migro de los niveles de rendimiento S1, S2 y S3 a las colecciones de partición única por mí mismo?

Puede migrar de los niveles de rendimiento S1, S2 y S3 a colecciones de partición única con Azure Portal o mediante programación. Puede hacerlo por sí mismo antes de la migración planeada para beneficiarse de las opciones de rendimiento flexible disponibles con colecciones de partición única, o bien se migrarán las colecciones por usted a finales de 2017.

**Para migrar a las colecciones de partición única con Azure Portal**

1. En [**Azure Portal**](https://portal.azure.com), haga clic en **Azure Cosmos DB** y seleccione la cuenta de Cosmos DB que se va a modificar. 
 
    Si **Azure Cosmos DB** no está en la barra de accesos, haga clic en >, vaya a **Bases de datos**, seleccione **Azure Cosmos DB** y luego seleccione la cuenta.  

2. En el menú de recursos, en **Contenedores**, haga clic en **Escala**, seleccione la colección que desea modificar en la lista desplegable y luego haga clic en **Plan de tarifa**. Las cuentas con un rendimiento predefinido tienen un plan de tarifa de S1, S2 o S3.  En la página **Elegir un plan de tarifa**, haga clic en **Estándar** para cambiar la capacidad de rendimiento definida por el usuario y en **Seleccionar** para guardar el cambio.

    ![Captura de pantalla de la página Configuración que muestra dónde cambiar el valor de rendimiento](./media/performance-levels/change-performance-set-thoughput.png)

3. En la página **Escala**, el **plan de tarifa** cambia a **Estándar** y el cuadro **Rendimiento (unidades de solicitud por segundo)** se muestra con un valor predeterminado de 400. Establezca el rendimiento entre 400 y 10 000 [unidades de solicitud](request-units.md)/segundo (RU/s). La opción **Factura mensual estimada** en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual. 

    >[!IMPORTANT] 
    > Cuando haya guardado los cambios y cambiado al plan de tarifa Estándar, no podrá revertir a los niveles de rendimiento S1, S2 o S3.

4. Haga clic en **Guardar** para guardar los cambios.

    Si cree que necesita más rendimiento (más de 10 000 RU/s) o espacio de almacenamiento (más de 10 GB), puede crear una colección con particiones. Para migrar una colección de partición única a una colección con particiones, consulte [Migración desde colecciones de partición única a colecciones con varias particiones](sql-api-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > El cambio de S1, S2 o S3 a Estándar puede tardar hasta dos minutos.
    > 
    > 

**Para migrar a colecciones de partición única con el SDK de .NET**

Otra opción para cambiar los niveles de rendimiento de las colecciones es a través de los SDK de Azure Cosmos DB. En esta sección solo se trata el cambio del nivel de rendimiento de una colección mediante la [API de .NET de SQL](sql-api-sdk-dotnet.md), pero el proceso es similar para los demás SDK.

A continuación se muestra un fragmento de código para cambiar el rendimiento de la colección a 5 000 unidades de solicitud por segundo:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver más ejemplos y obtener más información sobre nuestros métodos de oferta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>¿Cómo me afecta si soy un cliente de EA?

El precio para los clientes de EA estará protegido hasta el final de su contrato actual.

## <a name="next-steps"></a>pasos siguientes
Para obtener más información sobre los precios y la administración de datos con Azure Cosmos DB, consulte estos recursos:

1.  [Partición de datos en Cosmos DB](sql-api-partition-data.md). Información sobre la diferencia entre un contenedor de partición única y contenedores con particiones, así como sugerencias sobre cómo implementar una estrategia de particiones para escalar sin problemas.
2.  [Precios de Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Aprenda sobre los costes de rendimiento del aprovisionamiento y el consumo de almacenamiento.
3.  [Unidades de solicitud](request-units.md). Información sobre el consumo de rendimiento para diferentes tipos de operaciones; por ejemplo, lectura, escritura o consulta.
