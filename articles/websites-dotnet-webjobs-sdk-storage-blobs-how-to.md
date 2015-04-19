<properties 
	pageTitle="Cómo trabajar con almacenamiento de blob de Azure mediante el SDK de WebJobs" 
	description="Aprenda a usar el almacenamiento de blobs de Azure con el SDK de WebJobs. Active un proceso cuando aparece un nuevo blob en un contenedor y controle 'poison blobs'." 
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

# Cómo trabajar con almacenamiento de blob de Azure mediante el SDK de WebJobs

En esta guía se proporcionan ejemplos de código de C# que muestran cómo desencadenar un proceso cuando se crea o se actualiza un blob de Azure. Los ejemplos de código usan la [SDK de WebJobs](websites-dotnet-webjobs-sdk.md)versión 1.x.

Para obtener ejemplos de código que muestren cómo crear blobs, consulte [Cómo usar el almacenamiento de la cola de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
		
En la guía se supone que conoce [cómo crear un proyecto de WebJob en Visual Studio con cadenas de conexión que señalen a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md)..

## Tabla de contenido

-   [Cómo activar una función cuando se crea o actualiza un blob](#trigger)
	- Marcador de posición único para el nombre del blob con extensión
	- Marcadores de posición de nombre y extensión del blob independientes
-   [Tipos con los que funciona BlobTrigger](#types)
-   [Obtención del contenido de blobs de texto mediante un enlace a cadena](#string)
-   [Obtención del contenido de blobs serializado mediante ICloudBlobStreamBinder](#icbsb)
-   [Cómo controlar blobs dudosos](#poison)
-   [Algoritmo de sondeo de blobs](#polling)
-   [Confirmaciones de blobs](#receipts)
-   [Temas relacionados que se tratan en el artículo de colas](#queues)
-   [Pasos siguientes](#nextsteps)

## <a id="trigger"></a> Cómo activar una función cuando se crea o actualiza un blob

En esta sección se muestra cómo utilizar el atributo  `BlobTrigger`. 

### Marcador de posición único para el nombre del blob con extensión  

El ejemplo de código siguiente copia blobs de texto que aparecen en el contenedor *input* en el contenedor *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

El constructor de atributo toma un parámetro de cadena que especifica el nombre del contenedor y un marcador de posición para el nombre del blob. En este ejemplo, si un blob con el nombre *Blob1.txt* se crea en el contenedor *input*, la función crea un blob llamado *Blob1.txt* en el contenedor *output*. 

Puede especificar un patrón de nombre con el marcador de posición del nombre del blob, como se muestra en el ejemplo de código siguiente:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Este código copia solo los blobs que tienen nombres que empiecen por "original-". Por ejemplo, *original-Blob1.txt* en el contenedor *input* se copia en *copy-Blob1.txt* en el contenedor *output*.

Si necesita especificar un patrón de nombre para los nombres de blob que tienen llaves en el nombre, duplique las llaves. Por ejemplo, si desea encontrar los blobs del contenedor *images* que tengan nombres similares al siguiente:

		{20140101}-soundfile.mp3

use esto en su diseño:

		images/{{20140101}}-{name}

En el ejemplo, el valor del marcador de posición *name* sería *soundfile.mp3*. 

### Marcadores de posición de nombre y extensión del blob independientes

El ejemplo de código siguiente cambia la extensión de archivo a medida que copia blobs que aparecen en el contenedor *input* en el contenedor *output*. El código registra la extensión del blob *input* y establece la extensión del blob *output* como *.txt*.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a> Tipos que se pueden enlazar a los blobs

Puede usar el atributo `BlobTrigger` en los tipos siguientes:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Otros tipos que deserializa [ICloudBlobStreamBinder](#icbsb) 

Si desea trabajar directamente con la cuenta de almacenamiento de Azure, también puede agregar un parámetro `CloudStorageAccount` a la firma del método.

## <a id="string"></a> Obtención del contenido de blobs de texto mediante un enlace a cadena

Si se esperan blobs de texto, es posible aplicar `BlobTrigger` a un parámetro `string`. El ejemplo de código siguiente enlaza un blob de texto a un parámetro `string` denominado `logMessage`. La función usa ese parámetro para escribir el contenido del blob en el panel del SDK de WebJobs. 
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> Obtención del contenido de blobs serializado mediante ICloudBlobStreamBinder

El ejemplo de código siguiente usa una clase que implementa `ICloudBlobStreamBinder` para habilitar el atributo `BlobTrigger` para enlazar un objeto binario al tipo `WebImage`.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

El código de enlace `WebImage` se proporciona en una clase `WebImageBinder` que deriva de `ICloudBlobStreamBinder`.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a> Cómo controlar blobs dudosos

Cuando se produce un error en una función `BlobTrigger`, el SDK la llama de nuevo, si el error se hubiera producido por un error transitorio. Si fue el contenido del blob el que motivó el error, se producirá un error en la función cada vez que intente procesar el blob. De forma predeterminada, el SDK llama a una función hasta 5 veces con un blob determinado. Si se produce un error en la quinta prueba, el SDK agrega un mensaje a una cola denominada *webjobs-blobtrigger-poison*.

El número máximo de reintentos se puede configurar.. La misma configuración de [MaxDequeueCount](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#configqueue) se usa para el tratamiento de blobs dudosos y de mensajes de cola dudosos. 

El mensaje en cola de blobs dudosos es un objeto JSON que contiene las propiedades siguientes:

* FunctionId (con el formato *{WebJob name}*.Functions.*{Function name}*, por ejemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de versión de blob, por ejemplo: "0x8D1DC6E70A277EF")

En el ejemplo de código siguiente, la función `CopyBlob` tiene código que hace que se genere un error cada vez que se llama a la misma. Cuando el SDK la ha llamado el número máximo de reintentos, se crea un mensaje en la cola de blobs dudosos, y ese mensaje lo procesa la función `LogPoisonBlob`. 

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

El SDK automáticamente deserializa el mensaje JSON. Aquí está la clase `PoisonBlobMessage` : 

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> Algoritmo de sondeo de blobs

El SDK de WebJobs examina todos los contenedores que especifican los atributos `BlobTrigger` al iniciarse la aplicación. En una cuenta de almacenamiento grande, este análisis puede tardar algo de tiempo, por lo que podría pasar un rato hasta que se encuentren nuevos blobs y se ejecuten las funciones `BlobTrigger`.

Para detectar blobs nuevos o modificados tras el inicio de la aplicación, el SDK lee periódicamente los registros de almacenamiento de blobs. Los registros de blob se almacenan en búfer y solo se escriben físicamente aproximadamente cada 10 minutos, por lo que puede haber un gran retraso después de que un blob se cree o se actualice antes de la función `BlobTrigger` correspondiente se ejecute. 

Hay una excepción para los blobs que crea con el atributo `Blob`. Cuando el SDK de WebJobs crea un nuevo blob, pasa el nuevo blob inmediatamente a cualquier función `BlobTrigger` que coincida. Por lo tanto, si tiene una cadena de entradas y salidas de blobs, el SDK puede procesarlas eficientemente. Pero si desea una baja latencia al ejecutar las funciones de procesamiento de blobs que se han creado o actualizado por otros medios, se recomienda usar `QueueTrigger` en lugar de `BlobTrigger`.

### <a id="receipts"></a> Confirmaciones de blobs

El SDK de WebJobs se asegura de que no se llame más de una vez a ninguna función `BlobTrigger` para el mismo blob nuevo o actualizado. Lo consigue al mantener *blob receipts* con el fin de determinar si se ha procesado una versión de blob concreta.

Las confirmaciones de blob se almacenan en un contenedor denominado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure que especifica la cadena de conexión AzureWebJobsStorage. Un recibo de blob tiene la siguiente información:

* La función a la que se llamó para el blob ("*{WebJob name}*.Functions.*{Function name}*", por ejemplo: "WebJob1.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre de blob
* La ETag (un identificador de versión de blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la notificación de blob de ese blob desde el contenedor *azure-webjobs-hosts*.

## <a id="queues"></a>Temas relacionados que se tratan en el artículo de colas

Para obtener información acerca de cómo controlar el procesamiento de blobs activados por un mensaje de cola, o escenarios del SDK de WebJobs que no sean específicos del procesamiento de blobs, consulte [Cómo usar el almacenamiento de la cola de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Los temas que se tratan en este artículo incluyen:

* Funciones de Async
* Varias instancias
* Apagado correcto
* Uso de atributos del SDK de WebJobs en el cuerpo de una función
* Establecimiento de las cadenas de conexión de SDK en el código.
* Establecimiento de los valores de los parámetros del constructor del SDK de WebJobs en el código
* Configuración de `MaxDequeueCount` para el control de blobs dudosos.
* Activación manual de una función
* Escritura de registros

## <a id="nextstepsPasos siguientes"></a>

En esta guía se proporcionan ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con blobs de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=42-->
