---
title: Niveles de rendimiento en DocumentDB | Microsoft Docs
description: "Obtenga información sobre cómo los niveles de rendimiento de DocumentDB le permiten reservar capacidad de proceso para cada colección."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: b9902de45477bb7970da6c8f2234775bdb6edba8
ms.openlocfilehash: 65f19191bbb736d3b7fbdd94d73f2308ee6dea83


---
# <a name="performance-levels-in-documentdb"></a>Niveles de rendimiento en DocumentDB

> [!IMPORTANT] 
> Los niveles de rendimiento S1, S2 y S3 descritos en este artículo se han retirado y ya no están disponibles para las nuevas colecciones de DocumentDB.
>

Este artículo proporciona información general de los niveles de rendimiento S1, S2 y S3, y describe cómo se migrarán las colecciones que usan estos niveles de rendimiento a colecciones de partición única el 1 de agosto de 2017. Después de leer este artículo, podrá responder a las preguntas siguientes:

- [¿Por qué se han retirado los niveles de rendimiento S1, S2 y S3?](#why-retired)
- [¿Cómo se comparan las colecciones de partición única y con particiones con los niveles de rendimiento S1, S2 y S3?](#compare)
- [¿Qué tengo que hacer para garantizar el acceso ininterrumpido a mis datos?](#uninterrupted-access)
- [¿Cómo cambiará mi colección después de la migración?](#collection-change)
- [¿Cómo cambiará mi facturación después migrar a colecciones de partición única?](#billing-change)
- [¿Qué ocurre si necesito más de 10 GB de almacenamiento?](#more-storage-needed)
- [¿Puedo cambiar entre los niveles de rendimiento S1, S2 y S3 antes del 1 de agosto de 2017?](#change-before)
- [¿Cómo puedo saber cuándo ha migrado mi colección?](#when-migrated)
- [¿Cómo migro de los niveles de rendimiento S1, S2 y S3 a las colecciones de partición única por mí mismo?](#migrate-diy)
- [¿Cómo me afecta si soy un cliente de EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>¿Por qué se han retirado los niveles de rendimiento S1, S2 y S3?

Los niveles de rendimiento S1, S2 y S3 no ofrecen la flexibilidad que proporcionan las colecciones de partición única de DocumentDB. Con los niveles de rendimiento S1, S2 y S3, la capacidad de rendimiento y de almacenamiento están preestablecidas. Ahora DocumentDB ofrece la capacidad para personalizar su rendimiento y almacenamiento, lo que ofrece mucha más flexibilidad para el escalado a medida que cambien sus necesidades.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>¿Cómo se comparan las colecciones de partición única y con particiones con los niveles de rendimiento S1, S2 y S3?

En la tabla siguiente se comparan las opciones de rendimiento y almacenamiento disponibles en las colecciones de partición única, las colecciones con particiones y los niveles de rendimiento S1, S2 y S3. Este es un ejemplo para la región Este de EE. UU. - 2:

|   |Colección con particiones|Colección de partición única|S1|S2|S3|
|---|---|---|---|---|---|
|Rendimiento máximo|Ilimitado|10 000 RU/s|250 RU/s|1000 RU/s|2500 RU/s|
|Rendimiento mínimo|2500 RU/s|400 RU/s|250 RU/s|1000 RU/s|2500 RU/s|
|Almacenamiento máximo|Ilimitado|10 GB|10 GB|10 GB|10 GB|
|Precio|Rendimiento: 6 USD / 100 RU/s<br><br>Almacenamiento:&0;,25 USD/GB|Rendimiento: 6 USD / 100 RU/s<br><br>Almacenamiento:&0;,25 USD/GB|25 USD|50 USD|100 USD|

¿Es un cliente de EA? Si es así, consulte [¿Cómo me afecta si soy un cliente de EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>¿Qué tengo que hacer para garantizar el acceso ininterrumpido a mis datos?

Nada, DocumentDB se ocupa de la migración por usted. Si tiene una colección de S1, S2 o S3, se migrará a una colección de partición única el 31 de julio de 2017. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>¿Cómo cambiará mi colección después de la migración?

Si tiene una colección de S1, se migrará a una colección de partición única con un rendimiento de 400 RU/s. 400 RU/s es el rendimiento más bajo disponible con colecciones de partición única. Sin embargo, el coste de 400 RU/s en la colección de partición única es aproximadamente el mismo que con la colección de S1 y 250 RU/s: por lo que no va a pagar por las 150 RU/s extra disponibles.

Si tiene una colección de S2, se migrará a una colección de partición única con 1000 RU/s. No verá ningún cambio en su nivel de rendimiento.

Si tiene una colección de S3, se migrará a una colección de partición única con 2 500 RU/s. No verá ningún cambio en su nivel de rendimiento.

En todos estos casos, después de migrar la colección, podrá personalizar el nivel de rendimiento o escalar y reducir verticalmente según sea necesario para proporcionar un acceso de baja latencia a los usuarios. Para cambiar el nivel de rendimiento después de migrar la colección, abra su cuenta de DocumentDB en Azure Portal, haga clic en Escala, elija la colección y ajuste el nivel de rendimiento, como se muestra en la siguiente captura de pantalla:

![Escalado del rendimiento en Azure Portal](./media/documentdb-performance-levels/azure-documentdb-portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>¿Cómo cambiará mi facturación después migrar a colecciones de partición única?

Supongamos que tiene 10 colecciones S1 en la región Este de EE. UU. y 1 GB de almacenamiento para cada una, y las migra a 10 recopilaciones de partición única a 400 RU/seg. (el nivel mínimo). Su factura tendrá el siguiente aspecto si mantiene las 10 colecciones de partición única un mes completo:

![Cómo se comparan los precios de S1 para 10 colecciones con los precios de 10 colecciones que usan una colección de partición única](./media/documentdb-performance-levels/documentdb-s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>¿Qué ocurre si necesito más de 10 GB de almacenamiento?

Independientemente de si tiene una colección con un nivel de rendimiento S1, S2 o S3, o si tiene una colección de partición única, todos con 10 GB de almacenamiento disponibles, puede utilizar la herramienta de migración de datos de DocumentDB para migrar los datos a una colección con particiones, con un almacenamiento prácticamente ilimitado. Para más información acerca de las ventajas de una colección con particiones, consulte el tema sobre [particiones y escalado en Azure DocumentDB](documentdb-partition-data.md). Para más información sobre cómo migrar su colección de partición única o S1, S2, S3 a una colección con particiones, consulte [Migración desde colecciones de partición única a colecciones con varias particiones](documentdb-partition-data.md#migrating-from-single-partition). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-august-1-2017"></a>¿Puedo cambiar entre los niveles de rendimiento S1, S2 y S3 antes del 1 de agosto de 2017?

Solo las cuentas existentes con los niveles S1, S2 y S3 podrán cambiar y modificar los niveles de rendimiento con el portal o mediante programación. A partir del 1 de agosto de 2017, los niveles de rendimiento S1, S2 y S3 ya no estarán disponibles. Si cambia de S1, S3 o S3 a una colección de partición única, no puede volver a los niveles de rendimiento S1, S2 o S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>¿Cómo puedo saber cuándo ha migrado mi colección?

La migración se producirá el 31 de julio de 2017. Si tiene una colección que usa los niveles de rendimiento S1, S2 o S3, el equipo de DocumentDB se comunicará con usted por correo electrónico antes de realizar la migración. Una vez completada el 1 de agosto de 2017, Azure Portal indicará que su colección utiliza el plan de tarifa Estándar.

![Procedimientos para confirmar que la colección se ha migrado al plan de tarifa Estándar](./media/documentdb-performance-levels/documentdb-portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>¿Cómo migro de los niveles de rendimiento S1, S2 y S3 a las colecciones de partición única por mí mismo?

Puede migrar de los niveles de rendimiento S1, S2 y S3 a colecciones de partición única con Azure Portal o mediante programación. Puede hacerlo por sí mismo antes del 1 de agosto para beneficiarse de las opciones de rendimiento flexible disponibles con colecciones de partición única, o bien migraremos las colecciones por usted el 31 de julio de 2017.

**Para migrar a las colecciones de partición única con Azure Portal**

1. En [**Azure Portal**](https://portal.azure.com), haga clic en **NoSQL (DocumentDB)** y luego seleccione la cuenta de DocumentDB que quiere modificar. 
 
    Si **NoSQL (DocumentDB)** no está en la barra de accesos, haga clic en >, vaya a **Bases de datos**, seleccione **NoSQL (DocumentDB)** y luego seleccione la cuenta de DocumentDB.  

2. En el menú de recursos, en **Colecciones**, haga clic en **Escala**, seleccione la colección que desea modificar en la lista desplegable y luego haga clic en **Plan de tarifa**. Las cuentas con un rendimiento predefinido tienen un plan de tarifa de S1, S2 o S3.  En la hoja **Elegir su nivel de precios**, haga clic en **Estándar** para cambiar la capacidad de proceso definida por el usuario y luego haga clic en **Seleccionar** para guardar el cambio.

    ![Captura de pantalla de la hoja Configuración que muestra dónde cambiar el valor de rendimiento](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. En la hoja **Escala**, el **plan de tarifa** cambia a **Estándar** y el cuadro **Procesamiento (RU/s)** se muestra con un valor predeterminado de 400. Establezca el rendimiento entre 400 y 10 000 [unidades de solicitud](documentdb-request-units.md)/segundo (RU/s). La opción **Estimated Monthly Bill** (Factura mensual estimada) en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual. 

    >[!IMPORTANT] 
    > Cuando haya guardado los cambios y cambiado al plan de tarifa Estándar, no podrá revertir a los niveles de rendimiento S1, S2 o S3.

4. Haga clic en **Guardar** para guardar los cambios.

    Si cree que necesita más rendimiento (superior a 10 000 RU/s) o espacio de almacenamiento (mayor que 10 GB), puede crear una colección con particiones. Para migrar una colección de partición única a una colección con particiones, consulte [Migración desde colecciones de partición única a colecciones con varias particiones](documentdb-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > El cambiar de S1, S2 o S3 a Estándar puede tardar hasta 2 minutos.
    > 
    > 

**Para migrar a colecciones de partición única con el SDK de .NET**

Otra opción para cambiar los niveles de rendimiento de las colecciones es a través de nuestros SDK. En esta sección solo se trata el cambio del nivel de rendimiento de una colección mediante el [SDK de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), pero el proceso es similar para otros [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si no está familiarizado con el SDK. de NET, visite nuestro [tutorial de inicio](documentdb-get-started.md).

A continuación se muestra un fragmento de código para cambiar el rendimiento de la colección a 5 000 unidades de solicitud por segundo:
    
```C#
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

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre los precios y la administración de datos con Azure DocumentDB, consulte estos recursos:

1.  [Partición de datos en DocumentDB](documentdb-partition-data.md). Información sobre la diferencia entre las colecciones de partición única y las colecciones con particiones, así como sugerencias sobre cómo implementar una estrategia de particiones para escalar sin problemas.
2.  [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/). Aprenda sobre los costes de rendimiento del aprovisionamiento y el consumo de almacenamiento.
3.  [Unidades de solicitud](documentdb-request-units.md). Información sobre el consumo de rendimiento para diferentes tipos de operaciones; por ejemplo, lectura, escritura o consulta.
4.  [Modelado de datos en DocumentDB](documentdb-modeling-data.md). Aprenda a modelar los datos para DocumentDB.


<!--HONumber=Feb17_HO2-->


