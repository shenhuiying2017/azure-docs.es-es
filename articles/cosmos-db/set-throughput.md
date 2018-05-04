---
title: Aprovisionamiento del rendimiento de Azure Cosmos DB | Microsoft Docs
description: Aprenda a configurar el rendimiento aprovisionado para sus contenedores, colecciones, grafos y tablas de Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Configuración y obtención del rendimiento para contenedores de Azure Cosmos DB

Puede configurar el rendimiento de los contenedores de Azure Cosmos DB en Azure Portal o mediante los SDK del cliente. 

En la tabla siguiente se enumeran los rendimientos disponibles para los contenedores:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contenedor de una única partición</strong></p></td>
            <td valign="top"><p><strong>Contenedor con particiones</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento mínimo</p></td>
            <td valign="top"><p>400 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>1000 unidades de solicitud por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento máximo</p></td>
            <td valign="top"><p>10 000 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para establecer el rendimiento mediante Azure Portal

1. Abra [Azure Portal](https://portal.azure.com) en una nueva ventana.
2. En la barra de la izquierda, haga clic en **Azure Cosmos DB** o en **Todos los servicios** en la parte inferior, a continuación, desplácese a **Bases de datos** y, luego, haga clic en **Azure Cosmos DB**.
3. Seleccione la cuenta de Cosmos DB.
4. En la nueva ventana, haga clic en **Explorador de datos** en el menú de navegación.
5. En la nueva ventana, expanda la base de datos y el contenedor y, a continuación, haga clic en **Escala y configuración**.
6. En la nueva ventana, escriba el nuevo valor de rendimiento en el cuadro **Rendimiento** y luego haga clic en **Guardar**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Configuración del rendimiento mediante SQL API para .NET

El siguiente fragmento de código recupera el rendimiento actual y lo cambia a 500 RU/s. Para obtener el ejemplo de código completo, vea el proyecto [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) en GitHub.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Configuración del rendimiento mediante SQL API para Java

El siguiente fragmento de código recupera el rendimiento actual y lo cambia a 500 RU/s. Para obtener un ejemplo de código completo, vea el archivo [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) en GitHub. 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Obtención de rendimiento mediante el comando GetLastRequestStatistics de la API de MongoDB

La API de MongoDB admite un comando personalizado, *getLastRequestStatistics*, para recuperar las cargas de solicitudes de las operaciones especificadas.

Por ejemplo, en el shell de Mongo, ejecute la operación para la que desea comprobar la carga de solicitud.
```
> db.sample.find()
```

Después, ejecute el comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Con esto en mente, un método para calcular la cantidad de rendimiento reservado requerido por la aplicación es registrar el cargo de la unidad de solicitud asociado a la ejecución de las operaciones típicas frente a un elemento representativo usado por la aplicación y, a continuación, calcular el número de operaciones que prevé realizar cada segundo.

> [!NOTE]
> Si dispone de tipos de elemento que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, registre el cargo de unidad de solicitud de operación aplicable asociado a cada *tipo* de elemento típico.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Obtención de rendimiento mediante el uso de las métricas de portal de API de MongoDB

La manera más sencilla de obtener una buena estimación de los cargos por la unidad de solicitud de la base de datos de API de MongoDB es usar las métricas de [Azure Portal](https://portal.azure.com). Con los grafos *Número de solicitudes* y *Cargo de solicitud*, puede obtener una estimación de cuántas unidades de solicitud está consumiendo cada operación y cuántas unidades de solicitud consumen entre sí.

![Métricas del portal de API de MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Superación de los límites de rendimiento reservados en la API de MongoDB
Las aplicaciones que superan el rendimiento aprovisionado para un contenedor tendrá la velocidad limitada hasta que la velocidad de consumo caiga por debajo de la velocidad del rendimiento aprovisionado. Cuando se produce una limitación de velocidad, el back-end finalizará la solicitud de forma preferente con un código de error `16500`: `Too Many Requests`. De forma predeterminada, la API de MongoDB volverá a intentarlo automáticamente hasta 10 veces antes de devolver un código de error `Too Many Requests`. Si recibe numerosos códigos de error `Too Many Requests`, puede plantearse agregar una lógica de reintento en las rutinas de control de error de la aplicación o [mejorar el rendimiento aprovisionado para el contenedor](set-throughput.md).

## <a name="throughput-faq"></a>Preguntas más frecuentes sobre el rendimiento

**¿Se puede configurar el rendimiento a menos de 400 RU/s?**

400 RU/s es el rendimiento mínimo disponible en los contenedores de una sola partición de Cosmos DB (1000 RU/s es el valor mínimo para los contenedores con particiones). Las unidades de solicitud se establecen en intervalos de 100 RU/s pero el rendimiento no se puede establecer en 100 RU/s o en ningún valor inferior a 400 RU/s. Si está buscando un método rentable para desarrollar y probar Cosmos DB, puede usar gratuitamente el [Emulador de Azure Cosmos DB](local-emulator.md), que puede implementar localmente sin costo alguno. 

**¿Cómo se configura el rendimiento mediante la API de MongoDB?**

No hay ninguna extensión de API de MongoDB para configurar el rendimiento. La recomendación es utilizar SQL API, como se muestra en [Configuración del rendimiento mediante SQL API para .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre el aprovisionamiento y cómo pasar a escala planetaria con Cosmos DB, consulte [Partición y escalado en Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png