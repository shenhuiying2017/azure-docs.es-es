<properties 
	pageTitle="Creación de particiones y escalado en Azure DocumentDB | Microsoft Azure"      
	description="Obtenga información sobre cómo particionar trabajos en Azure DocumentDB, cómo configurar la creación de particiones y las claves de partición y cómo seleccionar la clave de partición correcta para su aplicación."         
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/05/2016" 
	ms.author="arramac"/>

# Partición y escalado en Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) se ha diseñado para ayudarle a lograr un rendimiento rápido y predecible. Además, permite escalar sin problemas a medida que la aplicación crece. En este artículo se proporciona información general sobre el funcionamiento de las particiones en DocumentDB y se describe cómo se pueden configurar las colecciones de DocumentDB para escalar de forma eficaz las aplicaciones.

Después de leer este artículo, podrá responder a las siguientes preguntas:

- ¿Cómo funcionan las particiones en Azure DocumentDB?
- ¿Cómo se configuran las particiones en DocumentDB?
- ¿Qué son las claves de partición y cómo se elige la clave de partición correcta para una aplicación?

## Creación de particiones en DocumentDB

DocumentDB permite almacenar y consultar documentos JSON sin esquemas con un tiempo de respuesta de milisegundos y a cualquier escala. DocumentDB proporciona **colecciones**, que son unos contenedores para el almacenamiento de datos. Las colecciones son recursos lógicos y pueden abarcar una o varias particiones físicas o servidores. DocumentDB determina el número de particiones en función del tamaño de almacenamiento y el rendimiento aprovisionado de la colección. Todas las particiones de DocumentDB tienen una cantidad fija de almacenamiento con respaldo SSD asociado y se replican para ofrecer una alta disponibilidad. Azure DocumentDB se encarga de administrar las particiones, por lo que no es necesario escribir código ni gestionar las particiones propias. Las colecciones de DocumentDB ofrecen unas funcionalidades de almacenamiento y procesamiento **prácticamente ilimitadas**.

La partición es completamente transparente para la aplicación. DocumentDB admite lecturas y escrituras rápidas, consultas SQL y LINQ, lógica transaccional basada en JavaScript, niveles de coherencia y control de acceso específico a través de llamadas de API de REST a un recurso de colección único. El servicio controla la distribución de datos por las distintas particiones, así como el enrutamiento de las solicitudes de consulta a la partición correcta.

¿Cómo funciona esto? Cuando cree una colección en DocumentDB, observará que hay un valor de configuración de **propiedad de clave de partición** que puede especificar. Se trata de una propiedad (o ruta de acceso) JSON incluida en los documentos que puede usar DocumentDB para distribuir los datos entre los distintos servidores o particiones. DocumentDB creará un valor hash con la clave de partición y usará ese resultado hash para determinar la partición en la que se almacenará el documento JSON. Todos los documentos que tengan la misma clave de partición se almacenarán en la misma partición.

Imaginemos, por ejemplo, una aplicación que almacene datos de empleados y departamentos en DocumentDB. Elijamos `"department"` como la propiedad de clave de partición para escalar horizontalmente los datos por departamento. Los documentos de DocumentDB deben contener obligatoriamente una propiedad `"id"` que será única en cada uno de los documentos que tengan el mismo valor de clave de partición, por ejemplo, `"Marketing`". Todos los documentos almacenados en una colección deben tener una combinación exclusiva de clave de partición e identificador, por ejemplo, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }` y `{ "Department": "Sales", "id": "0001" }`. En otras palabras, la propiedad compuesta de (clave de partición, identificador) es la clave principal de la colección.

### Claves de partición
La elección de la clave de partición es una decisión importante que deberá realizarse en el tiempo de diseño. Debe elegir un nombre de propiedad JSON con una amplia gama de valores y con mayor probabilidad de tener patrones de acceso distribuidos uniformemente. La clave de partición se especifica como una ruta de acceso JSON, por ejemplo, `/department` representa el departamento de propiedad.

En la tabla siguiente se muestran ejemplos de definiciones de clave de partición y los valores JSON correspondientes a cada una de ellas.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Ruta de acceso de clave de partición</strong></p></td>
            <td valign="top"><p><strong>Descripción</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc.department, donde doc es el documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc.properties.name, donde doc es el documento (propiedad anidada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc.id (el identificador y la clave de partición son la misma propiedad).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nombre de departamento"</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc["nombre de departamento"], donde doc es el documento.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La sintaxis de la ruta de acceso de la clave de partición es similar a la especificación de rutas de acceso para la indexación de rutas de acceso de directivas con la importante diferencia de que la ruta de acceso corresponde a la propiedad en lugar de al valor, es decir, no hay ningún carácter comodín al final. ¿Por ejemplo, especificaría /department/? para indexar los valores bajo departament, pero especificaría /department como la definición de clave de partición. La ruta de acceso de la clave de partición está indexada de forma implícita y no se puede excluir de la indexación mediante invalidaciones de directiva de indexación.

Veamos cómo afecta al rendimiento de la aplicación la elección de la clave de partición.

### Creación de particiones y procesamiento aprovisionado
DocumentDB se ha diseñado para ofrecer un rendimiento predecible. Cuando crea una colección, reserva procesamiento en términos de **[unidades de solicitud ](documentdb-request-units.md) (RU) por segundo**. A cada solicitud se le asigna una carga de unidad de solicitud proporcional a la cantidad de recursos del sistema, como la CPU y la E/S consumidas por la operación. Una lectura de un documento de 1 kB con coherencia de sesión consume 1 unidad de solicitud. Una lectura es 1 RU independientemente del número de elementos almacenados o del número de solicitudes que se ejecutan de manera simultánea. Los documentos más grandes exigen unidades de solicitud mayores en función del tamaño. Si se conoce el tamaño de las entidades y el número de lecturas que soportará la aplicación, se puede aprovisionar la cantidad exacta de procesamiento requerido para las necesidades de lectura de la aplicación.

Cuando DocumentDB almacena los documentos, los distribuye uniformemente entre las particiones según el valor de clave de partición. El procesamiento también se distribuye uniformemente entre las particiones disponibles, es decir, el procesamiento por partición = (procesamiento total por colección)/(número de particiones).

> [AZURE.TIP] Para lograr el procesamiento total de la colección, debe elegirse una clave de partición que permita distribuir uniformemente las solicitudes entre una serie de valores de clave de partición definidos.

## Colecciones de partición única y con varias particiones
DocumentDB admite la creación de colecciones de una sola partición y con varias particiones.

- Las **colecciones con particiones** pueden abarcar varias particiones y admitir una gran cantidad de almacenamiento y procesamiento. Debe especificarse una clave de partición para la colección.
- Las **colecciones de partición única** tienen un precio menor y pueden realizar consultas y transacciones en todos los datos de la colección. No obstante, son más limitadas en cuanto a la escalabilidad y al almacenamiento. Para estas colecciones no es necesario especificar una clave de partición. 

![Colecciones particionadas en DocumentDB][2]

Las colecciones de partición única son una buena opción en escenarios que no necesitan grandes volúmenes de almacenamiento o procesamiento. Obsérvese que las colecciones de partición única tienen los límites de escalabilidad y almacenamiento propios de una sola partición, es decir, hasta 10 GB de almacenamiento y hasta 10 000 unidades de solicitud por segundo.

Las colecciones particionadas pueden admitir una gran cantidad de almacenamiento y procesamiento. Sin embargo, las ofertas predeterminadas están configuradas para almacenar hasta 250 GB y escalar verticalmente hasta 250 000 unidades de solicitud por segundo. Si necesita mayor almacenamiento o procesamiento por colección para su cuenta, póngase en contacto con el [soporte técnico de Azure](documentdb-increase-limits.md) para solicitarlo.

En la tabla siguiente se enumeran las diferencias entre trabajar con colecciones de partición única y colecciones particionadas:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Colección de partición única</strong></p></td>
            <td valign="top"><p><strong>Colección particionada</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Partition Key</p></td>
            <td valign="top"><p>None</p></td>
            <td valign="top"><p>Obligatorio</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clave principal del documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>clave compuesta &lt;clave de partición> e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Almacenamiento mínimo</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Almacenamiento máximo</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Sin límite (250&#160;GB de forma predeterminada)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento mínimo</p></td>
            <td valign="top"><p>400 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>10&#160;000 unidades de solicitud por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Procesamiento máximo</p></td>
            <td valign="top"><p>10&#160;000 unidades de solicitud por segundo</p></td>
            <td valign="top"><p>Sin límite (250&#160;000 unidades de solicitud por segundo de forma predeterminada)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versiones de API</p></td>
            <td valign="top"><p>Todo</p></td>
            <td valign="top"><p>API 16-12-2015 y versiones más recientes</p></td>
        </tr>
    </tbody>
</table>

## Trabajar con los SDK

A partir de la [versión de la API de REST de 16-12-2015](https://msdn.microsoft.com/library/azure/dn781481.aspx), Azure DocumentDB admite la creación automática de particiones. Para poder crear colecciones particionadas, es necesario descargar la versión del SDK 1.6.0 (o posteriores) en una de las plataformas admitidas del SDK (.NET, Node.js, Java o Python).

### Creación de colecciones con particiones

En el ejemplo siguiente se muestra un fragmento de código .NET mediante el que se crea una colección que almacena los datos de telemetría de dispositivos con un procesamiento de 20 000 unidades de solicitud por segundo. El SDK establece el valor OfferThroughput (que a su vez establece el encabezado de solicitud `x-ms-offer-throughput` en la API de REST). En este caso, la clave de partición será `/deviceId`. La elección de la clave de partición se guarda junto con el resto de los metadatos de la colección, como nombre y la directiva de indexación.

Para este ejemplo elegimos `deviceId` por dos motivos: en primer lugar, sabemos que al haber un gran número de dispositivos, las escrituras pueden distribuirse entre las particiones de manera uniforme, lo que nos permite escalar la base de datos para introducir grandes volúmenes de datos; en segundo lugar, muchas de las solicitudes, como la obtención de la última lectura correspondiente a un dispositivo, se limitan a un único deviceId y se pueden recuperar desde una única partición.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Para crear colecciones con particiones, debe especificar un valor de rendimiento superior a 10 000 unidades de solicitud por segundo. Puesto que el procesamiento se da en múltiplos de 100, este debe ser 10 100 o superior.

Este método realiza una llamada API de REST a DocumentDB, tras lo cual el servicio proporciona un número de particiones que está determinado en función del procesamiento que se solicite. Puede cambiar el procesamiento de una colección a medida que evolucionen sus necesidades de rendimiento. Consulte [Niveles de rendimiento](documentdb-performance-levels.md) para más detalles.

### Lectura y escritura de documentos

Ahora, insertemos los datos en DocumentDB. La clase de ejemplo siguiente contiene una lectura de dispositivo y una llamada a CreateDocumentAsync para insertar una nueva lectura de dispositivo en una colección.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


A continuación leeremos el documento por su clave de partición e identificador, lo actualizaremos y, como paso final, lo eliminaremos por la clave de partición e identificador. Tenga en cuenta que las lecturas incluyen un valor PartitionKey (correspondiente al encabezado de solicitud `x-ms-documentdb-partitionkey` de la API de REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new object[] { "XMS-0001" }});

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new object[] { "XMS-0001" } });



### Consulta de colecciones con particiones

Al consultar datos en las colecciones particionadas, DocumentDB enruta automáticamente la consulta a las particiones correspondientes a los valores de clave de partición especificados en el filtro (en caso de que los haya). Por ejemplo, esta consulta se enruta únicamente a la partición cuya clave es "XMS-0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    	UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

La consulta siguiente no tiene filtro en la clave de partición (DeviceId) y por ello se despliega por todas las particiones, en las que se ejecuta según el índice de la partición. Tenga en cuenta que debe especificar EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` en la API de REST) para que el SDK ejecute una consulta en todas las particiones.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### Ejecución de procedimientos almacenados

También puede ejecutar transacciones atómicas en relación con documentos con el mismo identificador de dispositivo, por ejemplo, si desea mantener los agregados o el estado más reciente de un dispositivo en un único documento.

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        "XMS-001-FE24C",
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") });

En la siguiente sección, veremos cómo puede moverse a colecciones con particiones desde colecciones de partición única.

<a name="migrating-from-single-partition"></a>
### Migración desde colecciones de partición única a colecciones con varias particiones
Cuando una aplicación que usa colecciones con partición única necesita mayor procesamiento (>10 000 RU/s) o almacenamiento de datos más grande (>10 GB), puede utilizar la [Herramienta de migración de datos de DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) para migrar los datos desde la colección de partición única a una colección con varias particiones.

Además, dado que las claves de partición se pueden especificar solo durante la creación de la colección, debe exportar y volver a importar los datos mediante la [Herramienta de migración de datos de DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) para crear una colección con particiones.

Para migrar desde una colección de partición única a una colección con varias particiones

1. Exporte los datos desde la colección de partición única a JSON. Consulte [Exportación a archivos JSON](documentdb-import-data.md#export-to-json-file) para más detalles.
2. Importe los datos a una colección con particiones creada con una definición de clave de partición y un procesamiento de más de 10 000 unidades de solicitud por segundo, como se muestra en el ejemplo siguiente. Consulte [Importación a DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) para obtener detalles adicionales.

![Migración de datos a una colección con particiones en DocumentDB][3]

>[AZURE.TIP] Para conseguir tiempos de importación más rápidos, considere la posibilidad de aumentar el número de solicitudes paralelas a 100, como mínimo, para aprovechar el mayor procesamiento disponible para colecciones con particiones.

Ahora que hemos completado los aspectos básicos, echemos un vistazo a algunas consideraciones de diseño que deben tenerse en cuenta a la hora de trabajar con claves de partición de datos en DocumentDB.

## Diseño de la creación de particiones
La elección de la clave de partición es una decisión importante que deberá realizarse en el tiempo de diseño. En esta sección se describen algunos de los compromisos que implica la selección de una clave de partición para la colección.

### Clave de partición como límite de transacción
La elección de una clave de partición debe equilibrar la necesidad de habilitar el uso de transacciones frente a la necesidad de distribuir las entidades en varias particiones para garantizar que la solución sea escalable. En un extremo, se pueden almacenar todas las entidades en una sola partición, pero esto puede limitar la escalabilidad de su solución. En el otro extremo, se puede almacenar un documento por clave de partición, que podría ser altamente escalable, pero podría impedir el uso de transacciones entre documentos a través de desencadenadores y procedimientos almacenados. Una clave de partición idónea es la que permite usar consultas eficaces y que tiene suficientes particiones para asegurarse de que la solución sea escalable.

### Evitar cuellos de botella de rendimiento y almacenamiento 
También es importante elegir una propiedad que permita distribuir las escrituras entre una serie de valores distintos. Las solicitudes dirigidas a la misma clave de partición no pueden superar el procesamiento de una sola partición y se limitarán. Por tanto, es importante elegir una clave de partición que no dé como resultado **"zonas activas"** dentro de la aplicación. Además, el tamaño de almacenamiento total de documentos que tengan la misma clave de partición no puede superar los 10 GB.

### Ejemplos de claves de partición correctas
Estos son algunos ejemplos de cómo seleccionar la clave de partición de la aplicación:

* Si implementa un back-end de perfil de usuario, el identificador de usuario es una buena elección para la clave de partición.
* Si almacena datos IoT, como el estado del dispositivo, un identificador de dispositivo es una buena elección para la clave de partición.
* Si usa DocumentDB para registrar datos de serie temporal, el nombre de host o el identificador de proceso es una buena elección para la clave de partición.
* Si tiene una arquitectura multiempresa, el identificador del inquilino es una buena elección para la clave de partición.

Tenga en cuenta que, en algunos casos, como IoT y los perfiles de usuario mencionados anteriormente, la clave de partición podría ser igual que su identificador (clave de documento). En otros, como en los datos de serie temporal, la clave de partición será diferente del identificador.

### Creación de particiones, registro y datos de serie temporal
Uno de los casos de uso más común de DocumentDB es el registro y telemetría. Es importante elegir una buena clave de partición, ya que es posible que necesite leer y escribir grandes volúmenes de datos. La opción dependerá de las tasas de lectura y escritura y de los tipos de consultas que espera ejecutar. Estas son algunas sugerencias sobre cómo elegir una buena clave de partición.

- Si el caso de uso implica una pequeña tasa de escrituras que se acumulan durante un largo período de tiempo, y necesita consultar por intervalos de marcas de tiempo y otros filtros, el uso de un resumen de la marca de tiempo (por ejemplo, la fecha) como clave de partición es un buen enfoque. Esto le permite consultar todos los datos para una fecha desde una sola partición. 
- Si la carga de trabajo tiene muchas operaciones de escritura, lo que normalmente es más común, debe usar una clave de partición que no se base en la marca de tiempo para que DocumentDB pueda distribuir escrituras uniformemente en varias particiones. En este caso, un nombre de host, identificador de proceso, identificador de actividad u otra propiedad con cardinalidad elevada, es una buena elección. 
- Un tercer enfoque es un híbrido donde tenga varias colecciones, una para cada día o mes, y la clave de partición sea una propiedad pormenorizada como nombre de host. Esto tiene la ventaja de que se pueden establecer distintos niveles de rendimiento en función de la ventana de tiempo; por ejemplo, la colección para el mes actual se aprovisiona con un mayor procesamiento ya que sirve lecturas y escrituras, mientras que los meses anteriores se aprovisionan con menor procesamiento ya que solo sirven lecturas.

### Creación de particiones y arquitectura multiempresa
Si se desea implementar una aplicación multiempresa con DocumentDB, hay dos modelos principales para hacerlo: una clave de una partición por inquilino y una colección por inquilino. Estas son las ventajas y desventajas de cada uno:

* Una clave de partición por inquilino: en este modelo, los inquilinos se colocan en una sola colección. Por su parte, las consultas e inserciones de documentos dentro de un único inquilino pueden realizarse en una sola partición. También se puede implementar la lógica transaccional en todos los documentos de un inquilino. Puesto que varios inquilinos comparten una colección, se pueden ahorrar costos de almacenamiento y procesamiento mediante la agrupación de los recursos para los inquilinos en una sola colección, en lugar de aprovisionar capacidad de aumento adicional para cada inquilino. El inconveniente es que no es posible aislar el procesamiento por inquilino. Así, los incrementos de rendimiento/procesamiento se aplican a toda la colección, frente a los incrementos destinados a los inquilinos.
* Una colección por inquilino: cada inquilino tiene su propia colección. En este modelo es posible reservar procesamiento por inquilino. Con el nuevo consumo de DocumentDB basado en el modelo de precios, este modelo es más rentable en aplicaciones multiempresa con un número pequeño de inquilinos.

También puede usar un enfoque de combinación o niveles que coloca los inquilinos pequeños y migra los inquilinos más grandes a su propia colección.

## Pasos siguientes
En este artículo hemos descrito el funcionamiento de las particiones en Azure DocumentDB, cómo crear colecciones particionadas y cómo elegir una buena clave de partición para la aplicación.

-   Introducción a la codificación con [SDK](documentdb-sdk-dotnet.md) o la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Información sobre el [procesamiento aprovisionado en DocumentDB](documentdb-performance-levels.md)
-   Si desea personalizar la forma en que la aplicación realiza particiones, puede conectar su propia implementación de particiones del lado cliente. Consulte la [compatibilidad con la creación de particiones del lado cliente](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png

 

<!---HONumber=AcomDC_0406_2016-->