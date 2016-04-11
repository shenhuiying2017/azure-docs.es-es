<properties 
	pageTitle="Unidades de solicitud en DocumentDB | Microsoft Azure" 
	description="Obtenga información acerca de cómo entender, especificar y estimar los requisitos de la unidad de solicitud en DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="stbaro"/>

#Unidades de solicitud en DocumentDB

Este artículo proporciona información general sobre las unidades de solicitud en [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Después de leer este artículo, podrá responder a las preguntas siguientes:

-	¿Qué son las unidades de solicitud y los cargos de solicitud?
-	¿Cómo se puede especificar la capacidad de unidad de solicitud para una colección?
-	¿Cómo puedo estimar mis necesidades de unidad de solicitud de la aplicación?
-	¿Qué ocurre si supero la capacidad de la unidad de solicitud para una colección?


##Unidades de solicitud y cargos de solicitud
DocumentDB ofrece un rendimiento predecible y rápido mediante la *reserva* de recursos para satisfacer las necesidades de rendimiento de la aplicación. Como la carga de aplicaciones y los patrones de acceso cambian con el tiempo, DocumentDB permite aumentar o disminuir fácilmente el rendimiento reservado disponible para la aplicación.

Con DocumentDB, el rendimiento reservado se especifica en términos de procesamiento de unidades de solicitud por segundo. Puede pensar en unidades de solicitud como moneda de rendimiento, donde se *reserva* una cantidad de unidades de solicitud garantizadas para la aplicación por segundo. Cada operación de DocumentDB: escritura de un documento, realización de una consulta, actualización de un documento, consume CPU, memoria y E/S por segundo. Es decir, cada operación implica un *cargo de solicitud*, que se expresa en *unidades de solicitud*. La descripción de los factores que afectan a los cargos de unidades de solicitud, junto con los requisitos de rendimiento de la aplicación, le permite ejecutar la aplicación de la forma más rentable posible.

##Especificación de la capacidad de unidad de solicitud
Al crear una colección de DocumentDB, especifique el número de unidades de solicitud por segundo (RU) que desee reservar para la colección. Una vez creada la colección, la asignación completa de RU especificadas está reservada para el uso de la colección. Se garantiza que cada colección tiene características de rendimiento dedicadas y aisladas.

Es importante tener en cuenta que DocumentDB funciona en un modelo de reserva; es decir, se le cobrará la cantidad de rendimiento *reservada* para la colección, independientemente del rendimiento que se *utilice* activamente. Sin embargo, tenga en cuenta que a medida que los patrones de uso, datos y carga de la aplicación cambian, puede escalar verticalmente y reducir verticalmente de forma sencilla la cantidad de RU reservada mediante el SDK de DocumentDB o con el [Portal de Azure](https://portal.azure.com). Para obtener más información sobre el escalado vertical y la reducción vertical, consulte [Niveles de rendimiento de DocumentDB](documentdb-performance-levels.md).

##Consideraciones de la unidad de solicitud
Al estimar el número de unidades de solicitud que se reservan para la colección de DocumentDB, es importante tener en cuenta las siguientes variables:

- **Tamaño del documento**. Cuando aumenta el tamaño del documento también aumentan las unidades utilizadas para leer o escribir los datos.
- **Recuento de propiedades del documento**. Suponiendo que la indexación predeterminada de todas las propiedades, las unidades usadas para escribir un documento aumentarán conforme aumenta el recuento de propiedades.
- **Coherencia de datos**. Al usar los niveles de coherencia de datos Alta y De uso vinculado, se usarán unidades adicionales para leer documentos.
- **Propiedades indexadas**. Propiedades indexadas: Una directiva de índice en cada colección determina qué propiedades se indexan de forma predeterminada. Puede reducir el consumo de unidades de solicitud limitando el número de las propiedades indexadas o habilitando la indexación diferida.
- **Indexación de documentos**. De forma predeterminada, todos los documentos se indexan automáticamente, por tanto consumirá menos unidades de solicitud si no desea indexar algunos de los documentos.
- **Patrones de consultas**. La complejidad de una consulta afecta a la cantidad de unidades de solicitud consumidas para una operación. El número de predicados, la naturaleza de los predicados, las proyecciones, el número de UDF y el tamaño del conjunto de datos de origen influyen en el costo de operaciones de consulta.
- **Uso de script**. Las consultas, procedimientos almacenados y desencadenadores consumen unidades de solicitud según la complejidad de las operaciones que se están llevando a cabo. Cuando desarrolla su aplicación, inspeccione el encabezado request charge para entender mejor cómo consumen las operaciones la capacidad de unidad de solicitud.

##Estimación de necesidades de rendimiento
Una unidad de solicitud es una medida normalizada del costo de procesamiento de solicitudes. Una única unidad de solicitud representa la capacidad de procesamiento necesaria para leer (mediante una vinculación automática o Id.) un solo documento JSON de 1 KB que consta de 10 valores de propiedad únicos (excluidas las propiedades del sistema). Una solicitud para crear (insertar), reemplazar o eliminar el mismo documento consumirá más procesamiento del servicio y, por tanto, más unidades de solicitud.

> [AZURE.NOTE] La línea de base de 1 unidad de solicitud de un documento de 1 KB corresponde a una operación GET sencilla mediante una vinculación automática o un Id. del documento.

Cada respuesta del servicio de DocumentDB incluye un encabezado personalizado (x-ms-request-charge) que contiene las unidades de solicitud usadas por la solicitud. Este encabezado también es accesible a través de los SDK de DocumentDB. En el SDK de .NET, RequestCharge es una propiedad del objeto ResourceResponse. Para las consultas, el Explorador de consultas de DocumentDB en el Portal de Azure proporciona la información de carga de solicitud para las consultas ejecutadas.

![Comprobación de los cargos de RU en el Explorador de consultas][1]

Con esto en mente, un método para calcular la cantidad de rendimiento reservado requerido por la aplicación es registrar el cargo de la unidad de solicitud asociado a la ejecución de las operaciones típicas frente a un documento representativo utilizado por la aplicación y, a continuación, calcular el número de operaciones que prevé realizar cada segundo. Asegúrese de medir e incluir las consultas típicas y el uso del script de DocumentDB también.

>[AZURE.NOTE]Si dispone de tipos de documento que variarán considerablemente en cuanto a tamaño y el número de propiedades indizadas, registre el cargo de unidad de solicitud de operación aplicable asociado a cada *tipo* de documento típico.

Por ejemplo:

1. Registre el cargo de la unidad de solicitud de creación (inserción) de un documento típico. 
2. Registre el cargo de la unidad de solicitud de lectura de un documento típico.
3. Registre el cargo de la unidad de solicitud de actualización de un documento típico.
3. Registre el cargo de la unidad de solicitud de consultas de documentos comunes y típicas.
4. Registre el cargo de unidad de solicitud de los scripts personalizados (procedimientos almacenados, desencadenadores y funciones definidas por el usuario) usados por la aplicación
5. Calcule las unidades de solicitud necesarias según el número estimado de operaciones que se prevé ejecutar cada segundo.

##Un ejemplo de estimación de la unidad de solicitud
Considere el siguiente documento de ~1 KB:

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

>[AZURE.NOTE]Los documentos se han minimizado en DocumentDB, por lo que el tamaño calculado del sistema del documento anterior es ligeramente menor de 1 KB.


La siguiente tabla muestra los cargos de unidad de solicitud aproximados para las operaciones típicas de este documento (el cargo de la unidad de solicitud aproximado supone que el nivel de coherencia de la cuenta está establecido en "Sesión" y que todos los documentos se indexan automáticamente):

Operación|Cargo de la unidad de solicitud 
---|---
Creación de un documento|~15 RU 
Lectura de un documento|~1 RU
Documento de consulta por Id.|~2,5 RU

Además, esta tabla muestra los cargos de unidad de solicitud para las consultas típicas usadas en la aplicación:

Consultar|Cargo de la unidad de solicitud|N.º de documentos devueltos
---|---|--- 
Selección de alimentos por Id.|~2,5 RU|1 
Selección de alimentos por fabricante|~7 RU|7
Selección por grupo de alimentos y clasificación por peso|~70 RU|100
Selección de los 10 alimentos más importantes en un grupo de alimentos|~10 RU|10

>[AZURE.NOTE]Los cargos de RU varían según el número de documentos devueltos.

Con esta información, podemos hacer una estimación de los requisitos de RU para esta aplicación dado el número de operaciones y consultas que esperamos por segundo:

Operación o consulta|Número estimado por segundo|RU necesarias 
---|---|--- 
Creación de un documento|10|150 
Lectura de un documento|100|100 
Selección de alimentos por fabricante|25|175 
Selección por grupo de alimentos|10|700 
Selección de los 10 principales|15|150 en total|155|1275

En este caso, se espera un requisito de rendimiento medio de 1,275 RU/s. Redondeando hasta los 100 más cercanos, se pueden proporciona 1300 RU/s para esta colección de la aplicación.

##Superación de los límites de rendimiento reservados
La retirada del consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Para las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para una colección, las solicitudes a esa colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado. Cuando se produzca una limitación, el servidor finalizará de forma preventiva la solicitud con RequestRateTooLarge (código de estado HTTP 429) y devolverá el encabezado x-ms-retry-after-ms que indicará la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar realizar la solicitud.

	HTTP Status 429
	Status Line: RequestRateTooLarge
	x-ms-retry-after-ms :100

Si utiliza las consultas de LINQ y de SDK de cliente para .NET, no tendrá que tratar con esta excepción la mayoría del tiempo, ya que la versión actual del SDK de cliente .NET detecta implícitamente esta respuesta, respeta el encabezado retry-after especificado por el servidor y vuelve a intentar la solicitud. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

Si tiene más de un cliente de manera acumulativa funcionando por encima de la tasa de solicitud, el comportamiento de reintento predeterminado puede no ser suficiente y el cliente producirá una DocumentClientException con el código de estado 429 en la aplicación. En casos como este, puede controlar el comportamiento de reintento y la lógica en el error de la aplicación administrando rutinas o mejorando el rendimiento reservado para la colección.

##Pasos siguientes

Para obtener más información sobre el rendimiento con DocumentDB, consulte estos recursos:
 
- [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Administración de la capacidad de DocumentDB](documentdb-manage.md) 
- [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
- [Niveles de rendimiento en DocumentDB](documentdb-partition-data.md)

Para obtener más información acerca de DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/) de Azure DocumentDB.

[1]: ./media/documentdb-request-units/queryexplorer.png

<!---HONumber=AcomDC_0330_2016-->