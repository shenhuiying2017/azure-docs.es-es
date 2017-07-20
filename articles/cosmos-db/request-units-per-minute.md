---
title: 'Azure Cosmos DB: unidades de solicitud por minuto (RU/m) | Microsoft Docs'
description: "Obtenga información sobre cómo reducir el costo mediante el uso de unidades de solicitud por minuto."
services: cosmos-db
documentationcenter: 
author: arnomicrosoft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: acomet
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 0c597fa4afa816f9731edb744ae494b6ef928b9e
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Unidades de solicitud por minuto en Azure Cosmos DB

Azure Cosmos DB se ha diseñado para ayudarle a lograr un rendimiento rápido y predecible. Además, permite escalar sin problemas a medida que la aplicación crece. Puede aprovisionar el rendimiento de un contenedor de Cosmos DB en granularidades de unidades de solicitud por segundo y por minuto (RU/m). El procesamiento aprovisionado en granularidad por minuto se usa para administrar picos inesperados de la carga de trabajo producidos en una granularidad por segundo. 

En este artículo se proporciona información general sobre el funcionamiento del aprovisionamiento de unidad de solicitud por minuto (RU/m). Con el aprovisionamiento de RU/m, se pretende proporcionar un rendimiento predecible en torno a necesidades impredecibles (especialmente si tiene que ejecutar análisis en la parte superior de los datos operativos) y cargas de trabajo con picos. Queremos que nuestros clientes consuman más el rendimiento que aprovisionan, de modo que puedan escalar con mayor rapidez y tranquilidad.

Después de leer este artículo, podrá responder a las siguientes preguntas:

* ¿Cómo funciona una unidad de solicitud por minuto?
* ¿Qué diferencia existe entre unidad de solicitud por minuto y unidad de solicitud por segundo?
* ¿Cómo aprovisionar RU/m?
* ¿En qué escenario consideraré la posibilidad de aprovisionar la unidad de solicitud por minuto?
* ¿Cómo usar las métricas del portal para optimizar el costo y el rendimiento?
* Defina qué tipo de solicitud puede consumir el presupuesto de RU/m.

## <a name="provisioning-request-units-per-minute-rum"></a>Aprovisionamiento de unidades de solicitud por minuto (RU/m)

Al aprovisionar Azure Cosmos DB en la granularidad de unidad de solicitud por segundo (RU/s), cuenta con la garantía de que su solicitud se realizará correctamente con baja latencia si el rendimiento no ha superado la capacidad aprovisionada durante ese segundo. Con RU/m, la granularidad ofrece de forma inmediata la garantía de que su solicitud se realizará correctamente durante ese minuto. En comparación con los sistemas de ampliación, nos aseguramos de que el rendimiento que obtiene sea predecible y pueda planearlo.

El funcionamiento del aprovisionamiento por minuto es sencillo:

* RU/m se factura cada hora y aparte de RU/s. Para obtener más información, visite la [página de precios](https://aka.ms/acdbpricing) de Azure Cosmos DB.
* RU/m se puede habilitar en el nivel de colección. Esto es posible a través de los SDK (Node.js, Java o .NET) o del portal (también se incluyen las cargas de trabajo de API de MongoDB)
* Al habilitarse RU/m, por cada 100 RU/s aprovisionadas, también obtiene 1000 RU/m aprovisionadas (la proporción es 10 veces mayor)
* En un segundo determinado, una unidad de solicitud consume su aprovisionamiento de RU/m solo si ha superado su aprovisionamiento por segundo durante ese segundo
* Una vez finalizado el período de 60 segundos (UTC), el aprovisionamiento por minuto se ha rellenado
* RU/m se puede habilitar solo para colecciones con un aprovisionamiento máximo de 5000 RU/s por partición. Si escala sus necesidades de rendimiento y tiene ese alto nivel de aprovisionamiento por partición, recibirá un mensaje de advertencia

A continuación vemos un ejemplo concreto, en el cual un cliente puede aprovisionar 10 000 RU/s con 100 000 RU/m, ahorrando un 73 % del costo frente al aprovisionamiento por pico (en 50 000 RU/seg.) a través de un período de 90 segundos de una colección que tiene 10 000 RU/s y 100 000 RU/m aprovisionados:

* Primer segundo: el presupuesto de RU/m se establece en 100 000
* Tercer segundo: durante ese segundo, el consumo de la unidad de solicitud era de 11 010 RU, 1010 RU por encima del aprovisionamiento de RU/s. Por consiguiente, se deducen 1010 RU del presupuesto de RU/m. 98 990 RU están disponibles durante los próximos 57 segundos en el presupuesto de RU/m
* Vigésimo noveno segundo: durante ese segundo, tuvo lugar un pico grande (más de cuatro veces superior al aprovisionamiento por segundo), mientras que el consumo de la unidad de solicitud era de 46 920 RU. Se deducen 36 920 RU del presupuesto de RU/m que cayó de 92 323 RU (vigésimo octavo segundo) a 55 403 RU (vigésimo noveno segundo)
* Sexagésimo primer segundo: el presupuesto de RU/m se establece de nuevo en 100 000 RU.
 
![Gráfico donde se muestra el consumo y el aprovisionamiento de Azure Cosmos DB](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>Especificación de la capacidad de unidad de solicitud con RU/m

Al crear una colección de Azure Cosmos DB, especifique el número de unidades de solicitud por segundo (RU por segundo) que desee reservar para la colección. También puede decidir si desea agregar RU por minuto. Esto se puede hacer a través del portal o el SDK. 

### <a name="through-the-portal"></a>A través del portal

Para habilitar o deshabilitar RU por minuto, basta con hacer un solo clic al aprovisionar una colección. 

 ![Captura de pantalla donde se muestra cómo establecer RU/m en Azure Portal](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>A través del SDK
En primer lugar, es importante tener en cuenta que RU/m solo está disponible para los siguientes SDK:

* .NET 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

Este es un fragmento de código para crear una colección con 3000 unidades de solicitud por segundo y 30 000 unidades de solicitud por minuto con el SDK de .NET:

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

A continuación se muestra un fragmento de código para cambiar el rendimiento de la colección a 5000 unidades de solicitud por segundo sin aprovisionar RU por minuto mediante el SDK de .NET:

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>Escenarios de buen ajuste

En esta sección, proporcionamos información general de escenarios de buen ajuste para habilitar unidades de solicitud por minuto.

**Entorno de desarrollo y pruebas:** buen ajuste. Durante la fase de desarrollo, si prueba la aplicación con diversas cargas de trabajo, RU/m puede proporcionar la flexibilidad en esta fase. El [emulador](local-emulator.md), por otra parte, es una herramienta gratuita excelente para probar Azure Cosmos DB. Sin embargo, si desea empezar en un entorno en la nube, dispondrá de una gran flexibilidad con RU/m para sus necesidades de rendimiento ad hoc. Pasará más tiempo desarrollando que preocupándose de las necesidades de rendimiento al principio. Recomendamos empezar con el aprovisionamiento de RU/s mínimo y habilitar RU/m.

**Necesidades de granularidad de minuto con picos impredecibles:** buen ajuste. Ahorro: 25-75 %. Hemos observado una gran mejora con respecto a RU/m, estando en dicho grupo la mayoría de los escenarios de producción. Si tiene una carga de trabajo de IoT que se ha disparado varias veces en un minuto o consultas que se ejecutan cuando el sistema realiza inserción masiva al mismo tiempo, necesitará capacidad adicional para administrar las necesidades de los picos. Recomendamos optimizar sus necesidades de recursos aplicando nuestro enfoque paso a paso a continuación.

 ![Gráfico donde se muestra el consumo de la solicitud en una granularidad de cinco minutos](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *Ilustración: prueba comparativa de consumo de RU*

**Tranquilidad:** buen ajuste. Ahorro: 10-20 %. A veces, solo desea una mayor tranquilidad y no preocuparse tanto de los posibles picos, así como de la limitación. Esta característica es la adecuada para usted. En ese caso, recomendamos habilitar RU/m y, de un modo ligeramente menos intenso, su aprovisionamiento por segundo. Este caso es distinto del anterior, ya que usted no intentará optimizar su aprovisionamiento agresivamente. Esto es más como una mentalidad de “limitación cero” que adopta.

Operaciones críticas con necesidades ad hoc: en ocasiones, recomendamos permitir únicamente a las operaciones críticas tener acceso al presupuesto de RU/m para que las operaciones ad hoc o menos importantes no lo consuman. Esto puede definirse fácilmente en la sección siguiente.

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>Uso de las métricas del portal para optimizar el costo y el rendimiento

**En las próximas semanas, desarrollaremos más el contenido en torno a la supervisión del consumo de minutos de RU para optimizar sus necesidades de rendimiento.**

A través de las métricas del portal, puede ver cuántos de los segundos de RU normales consume en comparación con los minutos de RU. La supervisión de estas métricas debe ayudarle a optimizar su aprovisionamiento. 

Recomendamos un enfoque paso a paso sobre cómo usar RU/m para su beneficio. En cada paso, debe disponer de información general del consumo de RU que representa un ciclo completo de su carga de trabajo (podrían ser horas, días o incluso semanas) y obtener información sobre el uso de aquello que aprovisiona.

El principio que subyace a este enfoque es hacer que su aprovisionamiento de rendimiento esté lo más cerca posible de un punto de aprovisionamiento que coincida con sus criterios de rendimiento mostrados a continuación. 

![Gráfico donde se muestra el consumo de la solicitud en una granularidad de cinco minutos](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
Para entender el punto de aprovisionamiento óptimo de su carga de trabajo, debe entender lo siguiente:

* Patrones de consumo: ¿picos inexistentes, poco frecuentes o sostenidos? ¿Picos pequeños (doble de media), medios o grandes (más de 10 veces de media)?
* Porcentaje de solicitudes limitadas: ¿se siente cómodo en caso de tener un poco de limitación? Si es así, ¿en qué medida? 

Una vez que haya identificado cuáles son sus objetivos, podrá estar más cerca del aprovisionamiento óptimo.

Para ayudarle, queremos facilitar orientación general sobre cómo optimizar su aprovisionamiento en función de su consumo de RU/m. Dicha orientación no se aplica a todos los tipos de cargas de trabajo, pero se basa en el conocimiento de la vista previa privada. Puede que cambiemos estas líneas base a medida que obtenemos más información:

|RU/m % uso|Grado de uso de RU/m|Medidas recomendadas para el aprovisionamiento|
|---|---|---|
|0-1%|En uso|Reducir RU/s para consumir más RU/m|
|1-10%|Uso correcto|Mantener el mismo nivel de aprovisionamiento|
|Por encima del 10%|Uso excesivo|Aumentar RU/s para depender en menor grado de RU/m|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>Selección de las operaciones que pueden consumir el presupuesto de RU/m

En el nivel de solicitud, también puede habilitar o deshabilitar el presupuesto de RU/m para atender la solicitud con independencia del tipo de operación.  Si se consume el presupuesto de RU/seg. aprovisionado normal y la solicitud no puede consumir el presupuesto de RU/m, esta solicitud se limitará. De forma predeterminada, el presupuesto de RU/m atiende todas las solicitudes si se activa el presupuesto de rendimiento de RU/m. 

A continuación se muestra un fragmento de código para deshabilitar el presupuesto de RU/m mediante la API de DocumentDB para operaciones de CRUD y de consulta.

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo hemos descrito el funcionamiento de las particiones en Azure Cosmos DB, cómo crear colecciones particionadas y cómo elegir una buena clave de partición para la aplicación.

* Realice pruebas de escala y de rendimiento con Azure Cosmos DB. Consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md) para ver ejemplos.
* Introducción a la codificación con los [SDK](documentdb-sdk-dotnet.md) o la [API de REST](/rest/api/documentdb/).
* Información sobre el [procesamiento aprovisionado](request-units.md) en Azure Cosmos DB 


