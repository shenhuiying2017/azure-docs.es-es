<properties 
	pageTitle="Cómo trabajar con almacenamiento de tablas de Azure mediante el SDK de WebJobs" 
	description="Aprenda cómo usar el almacenamiento de tablas de Azure con el SDK de WebJobs. Cree tablas, agregue entidades a tablas y lea las tablas existentes." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Cómo trabajar con almacenamiento de tablas de Azure mediante el SDK de WebJobs

Esta guía proporciona ejemplos de código en C# que muestran cómo leer y escribir tablas de almacenamiento de Azure con la versión 1.x del [SDK de WebJobs](websites-dotnet-webjobs-sdk.md).

En la guía se supone que conoce [cómo crear un proyecto de WebJob en Visual Studio con cadenas de conexión que señalen a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md)..
		
Algunos de los fragmentos de código muestran el atributo `Table` usado en las funciones a las que [se llama manualmente](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual), es decir, no mediante uno de los atributos del desencadenador. 

## Tabla de contenido

-   [Cómo agregar entidades a una tabla](#ingress)
-   [Supervisión en tiempo real](#monitor)
-   [Cómo leer varias entidades de una tabla](#multiple)
-   [Cómo leer una única entidad de una tabla](#readone)
-   [Cómo usar la API de almacenamiento de .NET para trabajar directamente con una tabla](#readone)
-   [Temas relacionados que se tratan en el artículo acerca de las colas](#queues)
-   [Pasos siguientes](#nextsteps)

## <a id="ingress"></a>Cómo agregar entidades a una tabla

Para agregar entidades a una tabla, use el atributo `Table` con un parámetro `ICollector<T>` o `IAsyncCollector<T>`, donde `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table. 

The following code sample adds `Person` (entidades) a una tabla denominada *Ingress*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

Normalmente el tipo se usa con `ICollector` deriva de `TableEntity` o implementa `ITableEntity`, pero no es necesario. Cualquiera de las clases `Person` siguientes funciona con el código que se muestra en el método `Ingress` anterior.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Si desea trabajar directamente con la API de almacenamiento de Azure, puede agregar un parámetro `CloudStorageAccount` a la firma del método.

## <a id="monitor"></a> Supervisión en tiempo real

Puesto que las funciones de entrada de datos a menudo procesan grandes volúmenes de datos, el panel del SDK de WebJobs proporciona datos de supervisión en tiempo real. La sección **Registro de invocación** indica si la función aún se está ejecutando.

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

La página **Detalles de invocación** informa del progreso de la función (número de entidades escritas) mientras se está ejecutando y ofrece la oportunidad de anularla. 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Cuando finaliza la función, la página **Detalles de invocación** informa del número de filas escritas.

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Cómo leer varias entidades de una tabla

Para leer una tabla, use el atributo `Table` con un parámetro `IQueryable<T>` donde el tipo `T` derives from `TableEntity` o implementa `ITableEntity`.

El ejemplo de código siguiente lee y registra todas las filas de la tabla `Ingress`:
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a> Cómo leer una única entidad de una tabla

Hay un constructor de atributo `Table` con dos parámetros adicionales que le permiten especificar la clave de partición y clave de fila cuando desea enlazar a una entidad de tabla única.

El ejemplo de código siguiente lee una fila de tabla para una entidad `Person` en función de los valores recibidos de la clave de partición y la clave de fila en un mensaje de cola:  

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


La clase `Person` de este ejemplo no tiene que implementar `ITableEntity`.

## <a id="storageapi"></a> Cómo usar la API de almacenamiento de .NET para trabajar directamente con una tabla

También puede usar el atributo `Table` con un objeto `CloudTable` para una mayor flexibilidad al trabajar con una tabla.

El siguiente ejemplo de código utiliza un objeto `CloudTable` para agregar una entidad única a la tabla *Ingress*. 
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Para obtener más información sobre cómo usar el objeto `CloudTable`, consulte [Uso del almacenamiento de tablas en .NET](storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>Temas relacionados que se tratan en el artículo acerca de las colas

Para obtener información acerca de cómo controlar el procesamiento de tablas activado por un mensaje de cola, o escenarios del SDK de WebJobs que no sean específicos del procesamiento de tablas, consulte [Cómo trabajar con almacenamiento de cola de Azure mediante el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Los temas que se tratan en este artículo incluyen lo siguiente:

* Funciones de Async
* Varias instancias
* Apagado correcto
* Uso de atributos del SDK de WebJobs en el cuerpo de una función
* Establecimiento de las cadenas de conexión de SDK en el código
* Establecimiento de los valores de los parámetros del constructor del SDK de WebJobs en el código
* Activación manual de una función
* Escritura de registros

## <a id="nextstepsPasos siguientes"></a>

En esta guía se proporcionan ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con tablas de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=42-->
