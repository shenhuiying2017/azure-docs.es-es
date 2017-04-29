---
title: "Unidades de solicitud y estimación de rendimiento: Azure DocumentDB | Microsoft Docs"
description: "Obtenga información acerca de cómo entender, especificar y estimar los requisitos de la unidad de solicitud en DocumentDB."
services: documentdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6185c703e9148c71d9995b92540b8ea72fba5cc0
ms.lasthandoff: 04/18/2017


---
# <a name="request-units-in-documentdb"></a>Unidades de solicitud en DocumentDB
Ya disponible: la [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner)de DocumentDB. Obtenga más información en [Estimación de las necesidades de rendimiento](documentdb-request-units.md#estimating-throughput-needs).

![Calculadora de rendimiento][5]

## <a name="introduction"></a>Introducción
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) es un servicio de bases de datos NoSQL escalable y totalmente administrado para documentos JSON. Con DocumentDB, no tendrá que alquilar máquinas virtuales, implementar software ni supervisar bases de datos. Los ingenieros de Microsoft operan y supervisan de forma continua DocumentDB para ofrecer disponibilidad, rendimiento y protección de datos universales. Los datos de DocumentDB se almacenan en colecciones que son contenedores elásticos, altamente disponibles. En lugar de pensar y administrar recursos de hardware como CPU, memoria e IOPS para una colección, puede reservar el rendimiento en términos de solicitudes por segundo. DocumentDB administrará automáticamente el aprovisionamiento, la realización transparente de particiones y el escalado de la colección a fin de prestar servicio para el número aprovisionado de solicitudes. 

DocumentDB admite varias API de ejecuciones de lecturas, escrituras, consultas y procedimientos almacenados. Puesto que no todas las solicitudes son iguales, se les asigna una cantidad normalizada de **unidades de solicitud** según la cantidad de procesamiento requerida para prestar servicio a la solicitud. El número de unidades de solicitud para una operación es determinista y puede realizar un seguimiento del número de unidades de solicitud consumidas por cualquier operación de DocumentDB a través de un encabezado de respuesta.

Cada colección de DocumentDB se puede reservar según el rendimiento. Este también se puede expresar en términos de unidades de solicitud. Este se expresa en bloques de 100 unidades de solicitud por segundo, desde cientos hasta millones de unidades de solicitud por segundo. El rendimiento aprovisionado se puede ajustar durante la vida de una colección para adaptarse a las cambiantes necesidades de procesamiento y a los patrones de acceso de la aplicación. 

Después de leer este artículo, podrá responder a las preguntas siguientes:  

* ¿Qué son las unidades de solicitud y los cargos de solicitud?
* ¿Cómo se puede especificar la capacidad de unidad de solicitud para una colección?
* ¿Cómo puedo estimar mis necesidades de unidad de solicitud de la aplicación?
* ¿Qué ocurre si supero la capacidad de la unidad de solicitud para una colección?

## <a name="request-units-and-request-charges"></a>Unidades de solicitud y cargos de solicitud
DocumentDB y la API de MongoDB ofrecen un rendimiento predecible y rápido mediante la *reserva* de recursos para satisfacer las necesidades de rendimiento de la aplicación.  Como la carga de aplicaciones y los patrones de acceso cambian con el tiempo, DocumentDB permite aumentar o disminuir fácilmente el rendimiento reservado disponible para la aplicación.

Con DocumentDB, el rendimiento reservado se especifica en términos de procesamiento de unidades de solicitud por segundo.  Puede pensar en unidades de solicitud como divisa de rendimiento, donde se *reserva* una cantidad de unidades de solicitud garantizadas para la aplicación por segundo.  Cada operación de DocumentDB: escritura de un documento, realización de una consulta, actualización de un documento, consume CPU, memoria y E/S por segundo.  Es decir, cada operación implica un *cargo de solicitud*, que se expresa en *unidades de solicitud*.  La descripción de los factores que afectan a los cargos de unidades de solicitud, junto con los requisitos de rendimiento de la aplicación, le permite ejecutar la aplicación de la forma más rentable posible. 

Se recomienda ver una introducción en el vídeo siguiente, donde Aravind Ramachandran explica las unidades de solicitud y un rendimiento predecible con DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-documentdb"></a>Especificación de la capacidad de unidad de solicitud en DocumentDB
Al crear una colección de DocumentDB, especifique el número de unidades de solicitud por segundo (RU por segundo) que desee reservar para la colección. Según el rendimiento aprovisionado, DocumentDB asigna las particiones físicas para hospedar la colección y divide y reequilibra los datos entre las particiones a medida que va creciendo.

DocumentDB requiere que se especifique una clave de partición cuando se aprovisiona una colección con 10 000 unidades de solicitud o un valor superior. También se requiere una clave de partición para escalar el rendimiento de la colección a más de 10 000 unidades de solicitud en el futuro. Por lo tanto, se recomienda encarecidamente configurar una [clave de partición](documentdb-partition-data.md) al crear una colección, sea cual sea su rendimiento inicial. Como es posible que se tengan que dividir los datos entre varias particiones, es necesario elegir una clave de partición que tenga una cardinalidad alta (de cientos a millones de valores distintos) para que DocumentDB pueda escalar la colección y las solicitudes uniformemente. 

> [!NOTE]
> Una clave de partición es un límite lógico, no uno físico. Por lo tanto, no es necesario limitar el número de los valores de clave de partición distintos. De hecho, es mejor tener más valores distintos de clave de partición que tener menos, ya que DocumentDB dispondrá de más opciones de equilibrio de carga.

Este es un fragmento de código para crear una colección con 3000 unidades de solicitud por segundo con el SDK de .NET:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

DocumentDB funciona con un modelo de reserva del rendimiento. Es decir, se le cobrará por la cantidad de rendimiento *reservada* para la colección, independientemente de la que *use* activamente. A medida que los patrones de uso, datos y carga de la aplicación cambian, puede escalar y reducir verticalmente de forma sencilla la cantidad de unidades de solicitud reservadas mediante los SDK de DocumentDB o con [Azure Portal](https://portal.azure.com).

Cada colección está asignada a un recurso `Offer` de DocumentDB que contiene metadatos sobre el rendimiento aprovisionado de la colección. Puede cambiar el rendimiento asignado buscando el recurso de oferta correspondiente para una colección y, a continuación, actualizándolo con el nuevo valor de rendimiento. A continuación se muestra un fragmento de código para cambiar el rendimiento de la colección a 5000 unidades de solicitud por segundo mediante el SDK de .NET:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

No se producirá ningún cambio en la disponibilidad de la colección cuando cambie el rendimiento. Por lo general, el nuevo rendimiento reservado es efectivo en cuestión de segundos después de su aplicación.

## <a name="specifying-request-unit-capacity-in-api-for-mongodb"></a>Especificación de la capacidad de unidad de solicitud en la API de MongoDB
La API de MongoDB permite especificar el número de unidades de solicitud por segundo (RU por segundo) que desee reservar para la colección.

La API de MongoDB funciona con el mismo modelo de reserva basado en el rendimiento que DocumentDB. Es decir, se le cobrará por la cantidad de rendimiento *reservada* para la colección, independientemente de la que *use* activamente. A medida que los patrones de uso, datos y carga de la aplicación cambian, puede escalar y reducir verticalmente de forma sencilla la cantidad de unidades de solicitud reservadas mediante [Azure Portal](https://portal.azure.com).

No se producirá ningún cambio en la disponibilidad de la colección cuando cambie el rendimiento. Por lo general, el nuevo rendimiento reservado es efectivo en cuestión de segundos después de su aplicación.

## <a name="request-unit-considerations"></a>Consideraciones de la unidad de solicitud
Al estimar el número de unidades de solicitud que se reservan para la colección de DocumentDB, es importante tener en cuenta las siguientes variables:

* **Tamaño del documento**. Cuando aumenta el tamaño del documento también aumentan las unidades utilizadas para leer o escribir los datos.
* **Recuento de propiedades del documento**. Suponiendo que la indexación predeterminada de todas las propiedades, las unidades usadas para escribir un documento aumentarán conforme aumenta el recuento de propiedades.
* **Coherencia de datos**. Al usar los niveles de coherencia de datos Alta y De uso vinculado, se usarán unidades adicionales para leer documentos.
* **Propiedades indexadas**. Propiedades indexadas: Una directiva de índice en cada colección determina qué propiedades se indexan de forma predeterminada. Puede reducir el consumo de unidades de solicitud limitando el número de las propiedades indexadas o habilitando la indexación diferida.
* **Indexación de documentos**. De forma predeterminada, todos los documentos se indexan automáticamente, por tanto consumirá menos unidades de solicitud si no desea indexar algunos de los documentos.
* **Patrones de consultas**. La complejidad de una consulta afecta a la cantidad de unidades de solicitud consumidas para una operación. El número de predicados, la naturaleza de los predicados, las proyecciones, el número de UDF y el tamaño del conjunto de datos de origen influyen en el costo de operaciones de consulta.
* **Uso de script**.  Las consultas, procedimientos almacenados y desencadenadores consumen unidades de solicitud según la complejidad de las operaciones que se están llevando a cabo. Cuando desarrolla su aplicación, inspeccione el encabezado request charge para entender mejor cómo consumen las operaciones la capacidad de unidad de solicitud.

## <a name="estimating-throughput-needs"></a>Estimación de necesidades de rendimiento
Una unidad de solicitud es una medida normalizada del costo de procesamiento de solicitudes. Una única unidad de solicitud representa la capacidad de procesamiento necesaria para leer (mediante una vinculación automática o Id.) un solo documento JSON de 1 KB que consta de 10 valores de propiedad únicos (excluidas las propiedades del sistema). Una solicitud para crear (insertar), reemplazar o eliminar el mismo documento consumirá más procesamiento del servicio y, por tanto, más unidades de solicitud.   

> [!NOTE]
> La línea de base de 1 unidad de solicitud de un documento de 1 KB corresponde a una operación GET sencilla mediante una vinculación automática o un Id. del documento.
> 
> 

Por ejemplo, esta es una tabla que muestra el número de unidades de solicitud que se deben aprovisionar en tres tamaños diferentes de documento (1 KB, 4 KB y 64 KB) y en dos niveles de rendimiento diferentes (500 lecturas/segundo + 100 escrituras/segundo y 500 lecturas/segundo + 500 escrituras/segundo). Se configuró la coherencia de datos en la sesión y la directiva de indexación se estableció en None.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamaño del documento</strong></p></td>
            <td valign="top"><p><strong>Lecturas/segundo</strong></p></td>
            <td valign="top"><p><strong>Escrituras/segundo</strong></p></td>
            <td valign="top"><p><strong>Unidades de solicitud</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29 000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Uso de la calculadora de unidades de solicitud
Para ayudar a los clientes a optimizar sus estimaciones de rendimiento, existe una [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner) basada en web que le ayuda a calcular los requisitos de unidades de solicitud para las operaciones habituales, entre las cuales se incluye:

* Creación (escritura) de documentos
* Lectura de documentos
* Eliminación de documentos
* Actualizaciones de documentos

La herramienta también permite calcular las necesidades de almacenamiento de datos en función de los documentos de ejemplo que proporcione.

El uso de la herramienta es simple:

1. Cargue uno o más documentos JSON representativos.
   
    ![Cargar documentos en la calculadora de unidades de solicitud][2]
2. Para calcular los requisitos de almacenamiento de datos, escriba el número total de documentos que espera almacenar.
3. Escriba el número de operaciones de creación, lectura, actualización y eliminación de documentos que necesita (por segundo). Para calcular los cargos en materia de unidad de solicitud de las operaciones de actualización de documentos, cargue una copia del documento de ejemplo del paso 1 anterior que incluya actualizaciones de campo típicas.  Por ejemplo, si las actualizaciones de documentos suelen modifican dos propiedades denominadas "lastLogin" y "userVisits", bastará con copiar el documento de ejemplo, actualizar los valores de esas dos propiedades y cargar el documento copiado.
   
    ![Especificar los requisitos de rendimiento en la calculadora de unidades de solicitud][3]
4. Haga clic en calcular y examine los resultados.
   
    ![Resultados de la calculadora de unidades de solicitud][4]

> [!NOTE]
> Si dispone de tipos de documentos que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, cargue un ejemplo de cada *tipo* de documento típico en la herramienta y calcule los resultados.
> 
> 

### <a name="use-the-documentdb-request-charge-response-header"></a>Uso del encabezado de respuesta de cargo de solicitud de DocumentDB
Cada respuesta del servicio de DocumentDB incluye un encabezado personalizado (`x-ms-request-charge`) que contiene las unidades de solicitud consumidas por la solicitud. Este encabezado también es accesible a través de los SDK de DocumentDB. En el SDK de .NET, RequestCharge es una propiedad del objeto ResourceResponse.  Para las consultas, el Explorador de consultas de DocumentDB en el Portal de Azure proporciona la información de carga de solicitud para las consultas ejecutadas.

![Comprobación de los cargos de RU en el Explorador de consultas][1]

Con esto en mente, un método para calcular la cantidad de rendimiento reservado requerido por la aplicación es registrar el cargo de la unidad de solicitud asociado a la ejecución de las operaciones típicas frente a un documento representativo utilizado por la aplicación y, a continuación, calcular el número de operaciones que prevé realizar cada segundo.  Asegúrese de medir e incluir las consultas típicas y el uso del script de DocumentDB también.

> [!NOTE]
> Si dispone de tipos de documento que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, registre el cargo de unidad de solicitud de operación aplicable asociado a cada *tipo* de documento típico.
> 
> 

Por ejemplo:

1. Registre el cargo de la unidad de solicitud de creación (inserción) de un documento típico. 
2. Registre el cargo de la unidad de solicitud de lectura de un documento típico.
3. Registre el cargo de la unidad de solicitud de actualización de un documento típico.
4. Registre el cargo de la unidad de solicitud de consultas de documentos comunes y típicas.
5. Registre el cargo de unidad de solicitud de los scripts personalizados (procedimientos almacenados, desencadenadores y funciones definidas por el usuario) usados por la aplicación
6. Calcule las unidades de solicitud necesarias según el número estimado de operaciones que se prevé ejecutar cada segundo.

### <a id="GetLastRequestStatistics"></a>Uso del comando GetLastRequestStatistics de la API de MongoDB
La API de MongoDB admite un comando personalizado, *getLastRequestStatistics*, para recuperar la carga de solicitud de operaciones especificadas.

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

Con esto en mente, un método para calcular la cantidad de rendimiento reservado requerido por la aplicación es registrar el cargo de la unidad de solicitud asociado a la ejecución de las operaciones típicas frente a un documento representativo utilizado por la aplicación y, a continuación, calcular el número de operaciones que prevé realizar cada segundo.

> [!NOTE]
> Si dispone de tipos de documento que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, registre el cargo de unidad de solicitud de operación aplicable asociado a cada *tipo* de documento típico.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>Uso de las métricas del Portal de la API de MongoDB
La manera más sencilla de obtener una buena estimación de los cargos en materia de unidad de solicitud de la API de MongoDB es usar las métricas [Azure Portal](https://portal.azure.com). Con los gráficos *Número de solicitudes* y *Cargo de solicitud*, puede obtener una estimación de cuántas unidades de solicitud está consumiendo cada operación y cuántas unidades de solicitud consumen entre sí.

![Métricas del Portal de la API de MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>Un ejemplo de estimación de la unidad de solicitud
Considere el siguiente documento de ~1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Los documentos se han minimizado en DocumentDB, por lo que el tamaño calculado del sistema del documento anterior es ligeramente menor de 1 KB.
> 
> 

La siguiente tabla muestra los cargos de unidad de solicitud aproximados para las operaciones típicas de este documento (el cargo de la unidad de solicitud aproximado supone que el nivel de coherencia de la cuenta está establecido en "Sesión" y que todos los documentos se indexan automáticamente):

| Operación | Cargo de la unidad de solicitud |
| --- | --- |
| Creación de un documento |~15 RU |
| Lectura de un documento |~1 RU |
| Documento de consulta por Id. |~2,5 RU |

Además, esta tabla muestra los cargos de unidad de solicitud para las consultas típicas usadas en la aplicación:

| Consultar | Cargo de la unidad de solicitud | # N.º de documentos devueltos |
| --- | --- | --- |
| Selección de alimentos por Id. |~2,5 RU |1 |
| Selección de alimentos por fabricante |~7 RU |7 |
| Selección por grupo de alimentos y clasificación por peso |~70 RU |100 |
| Selección de los 10 alimentos más importantes en un grupo de alimentos |~10 RU |10 |

> [!NOTE]
> Los cargos de RU varían según el número de documentos devueltos.
> 
> 

Con esta información, podemos hacer una estimación de los requisitos de RU para esta aplicación dado el número de operaciones y consultas que esperamos por segundo:

| Operación o consulta | Número estimado por segundo | RU necesarias |
| --- | --- | --- |
| Creación de un documento |10 |150 |
| Lectura de un documento |100 |100 |
| Selección de alimentos por fabricante |25 |175 |
| Selección por grupo de alimentos |10 |700 |
| Selección de los 10 principales |15 |150 en total |

En este caso, se espera un requisito de rendimiento medio de 1,275 RU/s.  Redondeando hasta los 100 más cercanos, se pueden proporciona 1300 RU/s para esta colección de la aplicación.

## <a id="RequestRateTooLarge"></a> Superación de los límites de rendimiento reservados en DocumentDB
La retirada del consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Para las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para una colección, las solicitudes a esa colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado. Cuando se produzca una limitación, el servidor finalizará de forma preventiva la solicitud con RequestRateTooLargeException (código de estado HTTP 429) y devolverá el encabezado x-ms-retry-after-ms que indicará la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar realizar la solicitud.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si utiliza las consultas de LINQ y de SDK de cliente para .NET, no tendrá que tratar con esta excepción la mayoría del tiempo, ya que la versión actual del SDK de cliente .NET detecta implícitamente esta respuesta, respeta el encabezado retry-after especificado por el servidor y vuelve a intentar la solicitud. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

Si tiene más de un cliente de manera acumulativa funcionando por encima de la tasa de solicitud, el comportamiento de reintento predeterminado puede no ser suficiente y el cliente producirá una DocumentClientException con el código de estado 429 en la aplicación. En casos como este, puede controlar el comportamiento de reintento y la lógica en el error de la aplicación administrando rutinas o mejorando el rendimiento reservado para la colección.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Superación de los límites de rendimiento reservados en la API de MongoDB
Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para una colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado. Cuando se produce una limitación, el back-end finalizará la solicitud de forma preferente con un código de error*16500*: *Demasiadas solicitudes*. De forma predeterminada, la API de MongoDB volverá a intentarlo automáticamente hasta 10 veces antes de devolver un código de error *Demasiadas solicitudes*. Si recibe numerosos códigos de error *Demasiadas solicitudes*, puede plantearse agregar un comportamiento de reintento en las rutinas de control de error de la aplicación o [mejorar el rendimiento reservado de la colección](documentdb-set-throughput.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el rendimiento con bases de datos de Azure DocumentDB, consulte estos recursos:

* [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
* [Niveles de rendimiento en DocumentDB](documentdb-partition-data.md)

Para obtener más información acerca de DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/)de Azure DocumentDB. 

Para empezar a utilizar pruebas de escala y rendimiento con DocumentDB, consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
[6]: ./media/documentdb-request-units/api-for-mongodb-metrics.png

