---
title: 'Unidades de solicitud y estimación de rendimiento: Azure Cosmos DB | Microsoft Docs'
description: Obtenga información sobre cómo entender, especificar y estimar los requisitos de la unidad de solicitud en Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 0aa87aeaf852d7309c29c1298e326c101a944904
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de solicitud en Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Con Azure Cosmos DB, no tendrá que alquilar máquinas virtuales, implementar software ni supervisar bases de datos. Los mejores ingenieros de Microsoft operan y supervisan de forma continua Azure Cosmos DB para ofrecer disponibilidad, rendimiento y protección de datos universales. Puede acceder a los datos mediante las API de su elección, por ejemplo, [API SQL](documentdb-introduction.md), [API MongoDB](mongodb-introduction.md), [Table API](table-introduction.md) y grafo a través de la [API Gremlin](graph-introduction.md); todas ellas son compatibles de forma nativa. 

La divisa de Azure Cosmos DB es la **Unidad de solicitud (RU)**. Con las RU, no necesita reservar funcionalidades de lectura o escritura ni aprovisionar CPU, memoria ni IOPS. Azure Cosmos DB admite varias API con distintas operaciones que varían desde lecturas y escrituras sencillas hasta consultas de grafos complejos. Puesto que no todas las solicitudes son iguales, se les asigna una cantidad regularizada de **unidades de solicitud** según el número de procesamientos necesario para prestar servicio a la solicitud. El número de unidades de solicitud para una operación es determinista y puede realizar un seguimiento del número de unidades de solicitud consumidas por cualquier operación de Azure Cosmos DB a través de un encabezado de respuesta. 

Para proporcionar un rendimiento predecible, debe reservar el rendimiento en unidades de 100 RU/segundo. Puede [calcular las necesidades de rendimiento](request-units.md#estimating-throughput-needs) mediante el uso la [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner) de Azure Cosmos DB.

![Calculadora de rendimiento][5]

Después de leer este artículo, podrá responder a las preguntas siguientes:  

* ¿Qué son las unidades de solicitud y los cargos de solicitud en Azure Cosmos DB?
* ¿Cómo se puede especificar la capacidad de unidad de solicitud para un contenedor o un conjunto de contenedores en Azure Cosmos DB?
* ¿Cómo puedo estimar mis necesidades de unidad de solicitud de la aplicación?
* ¿Qué ocurre si supero la capacidad de unidad de solicitud para un contenedor o un conjunto de contenedores en Azure Cosmos DB?

Como Azure Cosmos DB es una base de datos de varios modelos, es importante tener en cuenta que este artículo es aplicable a todos los modelos de datos y las API de Azure Cosmos DB. En este artículo usa términos genéricos como *contenedor* y *elemento* para hacer referencia genéricamente a una colección, grafo o tabla y un documento, nodo o entidad, respectivamente.

## <a name="request-units-and-request-charges"></a>Unidades de solicitud y cargos de solicitud
Azure Cosmos DB ofrece un rendimiento predecible y rápido mediante la *reserva* de recursos para satisfacer las necesidades de rendimiento de la aplicación.  Como la carga de aplicaciones y los patrones de acceso cambian con el tiempo, Azure Cosmos DB permite aumentar o disminuir fácilmente el rendimiento reservado disponible para la aplicación.

Con Azure Cosmos DB, el rendimiento reservado se especifica en términos de procesamiento de unidades de solicitud por segundo. Puede pensar en unidades de solicitud como divisa de rendimiento, donde se *reserva* una cantidad de unidades de solicitud garantizadas para la aplicación por segundo.  Cada operación de Azure Cosmos DB: escritura de un documento, realización de una consulta, actualización de un documento, consume CPU, memoria y E/S por segundo.  Es decir, cada operación implica un *cargo de solicitud*, que se expresa en *unidades de solicitud*.  La descripción de los factores que afectan a los cargos de unidades de solicitud, junto con los requisitos de rendimiento de la aplicación, le permite ejecutar la aplicación de la forma más rentable posible. El Explorador de datos de Azure Portal también es una excelente herramienta para probar el centro de una consulta.

Recomendamos comenzar viendo el siguiente vídeo, donde Andrew Liu, administrador del programa de Azure Cosmos DB, explica las unidades de solicitud.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Aislamiento del rendimiento en las bases de datos distribuidas globalmente

Cuando la base de datos se ha replicado en más de una región, Azure Cosmos DB proporciona aislamiento del rendimiento para asegurarse de que el uso de la unidad de solicitud (RU) en una región no afecta a su uso en otra región. Por ejemplo, si escribe datos en una región y lee datos de otra región, las RU usadas para realizar la operación de escritura en la región *A* no se quitan de las RU usadas para la operación de lectura en la región *B*. Las RU no se dividen entre las regiones en las que ha implementado. Cada región en la que se replica la base de datos tiene la cantidad total de RU aprovisionadas. Para más información, consulte [Cómo se distribuyen datos globalmente con Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Consideraciones de la unidad de solicitud
Al estimar el número de unidades de solicitud que se aprovisionan, es importante tener en cuenta las siguientes variables:

* **Tamaño del elemento**. Cuando aumenta el tamaño, también aumenta el número de unidades de solicitud que se usan para leer o escribir los datos.
* **Recuento de propiedades del elemento**. Suponiendo que la indexación predeterminada de todas las propiedades, las unidades usadas para escribir un documento/nodo/entidad aumentarán conforme aumenta el recuento de propiedades.
* **Coherencia de datos**. Al usar los modelos de coherencia de datos Alta y Obsolescencia limitada, se usarán unidades de solicitud adicionales para leer elementos.
* **Propiedades indexadas**. Una directiva de índice en cada contenedor determina qué propiedades se indexan de forma predeterminada. Puede reducir el consumo de unidades de solicitud para operaciones de escritura limitando el número de las propiedades indexadas o habilitando la indexación diferida.
* **Indexación de documentos**. De forma predeterminada, cada elemento se indexa automáticamente. Consumirá menos unidades de solicitud si decide no indexar algunos elementos.
* **Patrones de consultas**. La complejidad de una consulta afecta a la cantidad de unidades de solicitud consumidas para una operación. El número de resultados de consulta, el número de predicados, la naturaleza de los predicados, las proyecciones, el número de UDF y el tamaño de los datos de origen influyen en el costo de las operaciones de consulta.
* **Uso de script**.  Las consultas, procedimientos almacenados y desencadenadores consumen unidades de solicitud según la complejidad de las operaciones que se están llevando a cabo. Cuando desarrolla su aplicación, inspeccione el encabezado request charge para entender mejor cómo consumen las operaciones la capacidad de unidad de solicitud.

## <a name="estimating-throughput-needs"></a>Estimación de necesidades de rendimiento
Una unidad de solicitud es una medida normalizada del costo de procesamiento de solicitudes. Una única unidad de solicitud representa la capacidad de procesamiento necesaria para leer (mediante una vinculación automática o Id.) un solo elemento de 1 KB que consta de diez valores de propiedad únicos (excluidas las propiedades del sistema). Una solicitud para crear (insertar), reemplazar o eliminar el mismo elemento consumirá más procesamiento del servicio y, por tanto, más unidades de solicitud.   

> [!NOTE]
> La línea de base de una unidad de solicitud de un elemento de 1 KB corresponde a una operación GET sencilla mediante una vinculación automática o un Id. del elemento.
> 
> 

Por ejemplo, esta es una tabla que muestra el número de unidades de solicitud que se deben aprovisionar para elementos con tres tamaños diferentes (1 KB, 4 KB y 64 KB) y en dos niveles de rendimiento diferentes (500 lecturas/segundo + 100 escrituras/segundo y 500 lecturas/segundo + 500 escrituras/segundo). Se configuró la coherencia de datos en *Sesión* y la directiva de indexación se estableció en *Ninguno*.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamaño del elemento</strong></p></td>
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
Para ayudar a los clientes a optimizar sus estimaciones de rendimiento, existe una [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner) basada en Web que ayuda a calcular los requisitos de unidades de solicitud para las operaciones habituales, entre las cuales se incluyen:

* Creaciones (escrituras) de elementos
* Lecturas de elementos
* Eliminaciones de elementos
* Actualizaciones de elementos

La herramienta también permite calcular las necesidades de almacenamiento de datos en función de los elementos de ejemplo que proporcione.

El uso de la herramienta es simple:

1. Cargue uno o más elementos representativos (por ejemplo, un documento JSON de ejemplo).
   
    ![Carga de elementos en la calculadora de unidades de solicitud][2]
2. Para calcular los requisitos de almacenamiento de datos, escriba el número total de elementos (como documentos, filas o vértices) que espera almacenar.
3. Escriba el número de operaciones de creación, lectura, actualización y eliminación que necesita (por segundo). Para calcular los cargos en materia de unidad de solicitud de las operaciones de actualización de elementos, cargue una copia del elemento de ejemplo del paso 1 anterior que incluya actualizaciones de campo típicas.  Por ejemplo, si las actualizaciones de elementos suelen modificar dos propiedades denominadas *lastLogin* y *userVisits*, copie un elemento de ejemplo, actualice los valores de esas dos propiedades y cargue el elemento copiado.
   
    ![Especificar los requisitos de rendimiento en la calculadora de unidades de solicitud][3]
4. Haga clic en calcular y examine los resultados.
   
    ![Resultados de la calculadora de unidades de solicitud][4]

> [!NOTE]
> Si dispone de tipos de elementos que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, cargue un ejemplo de cada *tipo* de elemento típico en la herramienta y calcule los resultados.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Uso del encabezado de respuesta de cargo de solicitud de Azure Cosmos DB
Cada respuesta del servicio de Azure Cosmos DB incluye un encabezado personalizado (`x-ms-request-charge`) que contiene las unidades de solicitud consumidas por una solicitud determinada. Este encabezado también es accesible a través de los SDK de Azure Cosmos DB. En el SDK de .NET, `RequestCharge` es una propiedad del objeto `ResourceResponse`.  Para las consultas, el Explorador de datos de Azure Cosmos DB en Azure Portal proporciona la información de carga de solicitud para las consultas ejecutadas.

Con esto en mente, un método para calcular la cantidad de rendimiento reservado requerido por la aplicación es registrar el cargo de la unidad de solicitud asociado a la ejecución de las operaciones típicas frente a un elemento representativo usado por la aplicación y, a continuación, calcular el número de operaciones que prevé realizar cada segundo.  Asegúrese de medir e incluir las consultas típicas y el uso del script de Azure Cosmos DB también.

> [!NOTE]
> Si dispone de tipos de elementos que varían considerablemente en cuanto a tamaño y número de propiedades indexadas, registre el cargo de unidad de solicitud de operación aplicable asociado a cada *tipo* de elemento típico.
> 
> 

Por ejemplo: 

1. Registre el cargo de la unidad de solicitud de creación (inserción) de un elemento típico. 
2. Registre el cargo de la unidad de solicitud de lectura de un elemento típico.
3. Registre el cargo de la unidad de solicitud de actualización de un elemento típico.
4. Registre el cargo de la unidad de solicitud de consultas de elementos comunes y típicas.
5. Registre el cargo de unidad de solicitud de los scripts personalizados (procedimientos almacenados, desencadenadores y funciones definidas por el usuario) usados por la aplicación
6. Calcule las unidades de solicitud necesarias según el número estimado de operaciones que se prevé ejecutar cada segundo.

## <a name="a-request-unit-estimate-example"></a>Un ejemplo de estimación de la unidad de solicitud
Considere el siguiente documento de ~1 KB:

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
> Los documentos se han minimizado en Azure Cosmos DB, por lo que el tamaño calculado del sistema del documento anterior es ligeramente inferior a 1 KB.
> 
> 

La siguiente tabla muestra los cargos de unidad de solicitud aproximados para las operaciones típicas de este elemento (el cargo de la unidad de solicitud aproximado supone que el nivel de coherencia de la cuenta está establecido en *Sesión* y que todos los elementos se indexan automáticamente):

| Operación | Cargo de la unidad de solicitud |
| --- | --- |
| Crear elemento |~15 RU |
| Lectura de elemento |~1 RU |
| Consulta de elemento por identificador |~2,5 RU |

Además, esta tabla muestra los cargos de unidad de solicitud para las consultas típicas usadas en la aplicación:

| Consultar | Cargo de la unidad de solicitud | Número de elementos devueltos |
| --- | --- | --- |
| Selección de alimentos por Id. |~2,5 RU |1 |
| Selección de alimentos por fabricante |~7 RU |7 |
| Selección por grupo de alimentos y clasificación por peso |~70 RU |100 |
| Selección de los 10 alimentos más importantes en un grupo de alimentos |~10 RU |10 |

> [!NOTE]
> Los cargos de RU variarán según el número de elementos devueltos.
> 
> 

Con esta información, puede hacer una estimación de los requisitos de RU para esta aplicación dado el número de operaciones y consultas que espera por segundo:

| Operación o consulta | Número estimado por segundo | RU necesarias |
| --- | --- | --- |
| Crear elemento |10 |150 |
| Lectura de elemento |100 |100 |
| Selección de alimentos por fabricante |25 |175 |
| Selección por grupo de alimentos |10 |700 |
| Selección de los 10 principales |15 |150 en total |

En este caso, se espera un requisito de rendimiento medio de 1,275 RU/s.  Redondeando hasta los 100 más cercanos, se pueden aprovisionar 1300 RU/s para el contenedor (o conjunto de contenedores) de esta aplicación.

## <a id="RequestRateTooLarge"></a> Superación de los límites de rendimiento reservados en Azure Cosmos DB
La retirada del consumo de la unidad de solicitud se evalúa como frecuencia por segundo. En el caso de las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada, la frecuencia de las solicitudes se limitará hasta que caiga por debajo del nivel de rendimiento aprovisionado. Cuando se limita la frecuencia de una solicitud, el servidor finaliza de forma preventiva la solicitud con `RequestRateTooLargeException` (código de estado HTTP 429) y devuelve el encabezado `x-ms-retry-after-ms` que indica la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar realizar la solicitud.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si utiliza las consultas de LINQ y de SDK de cliente para .NET, no tendrá que tratar con esta excepción la mayoría del tiempo, ya que la versión actual del SDK de cliente .NET detecta implícitamente esta respuesta, respeta el encabezado retry-after especificado por el servidor y vuelve a intentar la solicitud automáticamente. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

Si tiene más de un cliente de manera acumulativa funcionando por encima de la frecuencia de solicitud, el comportamiento de reintento predeterminado puede no ser suficiente y el cliente producirá un `DocumentClientException` con el código de estado 429 en la aplicación. En casos como este, es posible que desee controlar el comportamiento de reintento y la lógica en las rutinas de control de errores de la aplicación o aumentar el rendimiento aprovisionado en el contenedor (o el conjunto de contenedores).

## <a name="next-steps"></a>Pasos siguientes
 
Para obtener información acerca de cómo establecer y obtener el rendimiento mediante Azure Portal y el SDK, consulte:

* [Configuración y obtención del rendimiento para contenedores de Azure Cosmos DB](set-throughput.md)

Para más información sobre el rendimiento con bases de datos de Azure Cosmos DB, explore estos recursos:

* [Precios de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Creación de particiones en Azure Cosmos DB](partition-data.md)

Para más información sobre Azure Cosmos DB, consulte la [documentación](https://azure.microsoft.com/documentation/services/cosmos-db/) de Azure Cosmos DB. 

Para empezar a usar pruebas de escala y rendimiento con Azure Cosmos DB, consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

