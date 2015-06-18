<properties 
	pageTitle="Cómo consumir un servicio web de aprendizaje automático que se ha publicado desde un experimento de aprendizaje automático | Azure" 
	description="Una vez publicado un servicio de aprendizaje automático, se puede consumir el servicio web RESTFul facilitado como servicio de respuesta de solicitud o como servicio de ejecución por lotes." 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# Cómo consumir un servicio web de aprendizaje automático de Azure publicado

## Introducción

Cuando se publica como un servicio web, los experimentos de Aprendizaje automático de Azure proporcionan una API de REST que puede ser consumida por una amplia gama de dispositivos y plataformas. Esto es porque la sencilla API de REST acepta y responde con mensajes de formato JSON. El portal de Aprendizaje automático de Azure proporciona código que se puede utilizar para llamar al servicio web en R, C# y Python. Pero estos servicios se pueden llamar mediante cualquier lenguaje de programación y desde cualquier dispositivo que satisfaga tres criterios:

* Disponer de una conexión de red
* Disponer de capacidades SSL para ejecutar solicitudes HTTPS
* Disponer de capacidad para analizar JSON (a mano o mediante bibliotecas de apoyo)

Esto significa que los servicios se pueden consumir desde aplicaciones web, aplicaciones móviles, aplicaciones de escritorio personalizadas e incluso desde dentro de Excel.  

Un servicio web de Aprendizaje automático de Azure se puede consumir de dos maneras diferentes, como un servicio de solicitud-respuesta o como un servicio de ejecución por lotes. En cada escenario se proporciona la funcionalidad a través del servicio web RESTFul que se facilita para consumirse una vez que se ha publicado el experimento. Implementando un servicio web de aprendizaje automático en Azure con un punto extremo de servicio web Azure, donde el servicio se escala automáticamente según el uso, puede evitar los costos continuados derivados de los recursos de hardware.

<!-- Cuando se publique este artículo, arregle el vínculo y quite la marca de comentario
Para obtener más información sobre cómo administrar los extremos de servicio web de Aprendizaje automático de Azure mediante la API de REST, consulte **Extremos de servicio web de Aprendizaje automático de Azure**. 
-->

Para obtener información acerca de cómo crear y publicar un servicio web de Aprendizaje automático de Azure, 
consulte [Publicar un servicio web de Aprendizaje automático de Azure][publicar]. Para obtener un tutorial paso a paso acerca de la creación de un experimento de aprendizaje automático y publicarlo, consulte [Desarrollar una solución de predicción mediante Aprendizaje automático de Azure][tutorial].

[publicar]: machine-learning-publish-a-machine-learning-web-service.md
[tutorial]: machine-learning-walkthrough-develop-predictive-solution.md


## Servicio de solicitud-respuesta (RRS)

Un Servicio de solicitud-respuesta (RRS) es un servicio web altamente escalable y de baja latencia utilizado para proporcionar una interfaz para los modelos sin estado que se han creado y publicado desde un experimento Azure Machine Learning Studio.

RRS acepta una sola fila de parámetros de entrada y genera una sola fila como resultado. La fila de resultados puede contener varias columnas.

Un ejemplo de RRS es validar la autenticidad de una aplicación. En este caso, pueden esperarse cientos de millones de instalaciones de una aplicación. Cuando se inicia la aplicación, realiza una llamada al servicio RRS con la entrada correspondiente. A continuación, la aplicación recibe una respuesta de validación desde el servicio que permite o bloquea el funcionamiento de la aplicación.


## Servicio de ejecución de lotes (BES)
 
Un Servicio de ejecución de lotes (BES) es un servicio que maneja una puntuación de gran volumen asincrónica de un lote de registros de datos. La entrada de BES contiene un lote de registros de una variedad de orígenes, como blobs, tablas de Azure, SQL Azure, HDInsight (resultados de una consulta de Hive, por ejemplo) y orígenes de HTTP. La salida de BES contiene los resultados de la puntuación. Los resultados se transfieren a un archivo del almacenamiento de blobs de Azure y los datos del extremo de almacenamiento se devuelven en la respuesta.

Un BES resultaría útil cuando las respuestas no se necesiten inmediatamente, como para una puntuación programada regularmente de individuos o para dispositivo de Internet de las cosas (IOT).

## Ejemplos
Para mostrar cómo funcionan RRS y BES, usamos un ejemplo de servicio web de Azure. Este servicio se usaría en un escenario IOT (Internet de las cosas). Para facilitarlo, nuestro dispositivo solo envía un valor, `cog_speed` y recibe una sola respuesta. 

Hay cuatro elementos de información que son necesarios para llamar al servicio RRS o BES. Esta información está disponible en las páginas de servicio de [Páginas de servicios de Aprendizaje automático de Azure](https://studio.azureml.net) una vez que se ha publicado el experimento. Haga clic en el vínculo de SERVICIOS WEB situado a la izquierda de la pantalla y verá los servicios publicados. Para obtener información acerca de un servicio concreto, hay vínculos de páginas de ayuda de API para RRS y BES.

1.	La **clave de API de servicio**, disponible en la página principal de servicios
2.	El **URI de servicio**, disponible en la página de ayuda de la API del servicio seleccionado
3.	El **cuerpo de la solicitud de API** esperado, disponible en la página de ayuda de la API del servicio seleccionado
4.	El **cuerpo de respuesta de la API esperado**, disponible en la página de ayuda de la API del servicio seleccionado

En los dos ejemplos siguientes, se utiliza el lenguaje C# para ilustrar el código necesario y la plataforma de destino es un escritorio de Windows 8. 

### Ejemplo de RRS
En la página de ayuda de la API, además de la dirección URI, introducirá y emitirá definiciones y ejemplos de código. Se llama a la entrada de la API, para este servicio específicamente, y es la carga de la llamada a la API. 

**Solicitud de ejemplo**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


De forma similar, también se llama a la respuesta de la API, nuevamente para este servicio en concreto.

**Respuesta de ejemplo**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

Hacia la parte inferior de la página, encontrará los ejemplos de código. A continuación se muestra el código de ejemplo para la implementación de C# 
                   
**Código de ejemplo**
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### Ejemplo de BES
En la página de ayuda de la API, además del URI, encontrará información acerca de varias llamadas que están disponibles. A diferencia del servicio RRS, el servicio BES es asincrónico. Esto significa que la API de BES simplemente pone en cola un trabajo que se va a ejecutar. Pero realmente no se ejecuta antes de recibir la respuesta de la API. A continuación se describen las tres cosas que un desarrollador puede hacer con el servicio BES.

1. Enviar un trabajo de ejecución por lotes
1. Obtener el estado o el resultado de un trabajo de ejecución por lotes
1. Eliminar un trabajo de ejecución por lotes  

**1. Enviar un trabajo de ejecución por lotes**

Envíe un trabajo de ejecución por lotes al proporcionar información acerca de dónde se almacenan los datos del lote. En este ejemplo, daremos por hecho que los registros que deseamos puntuar por lotes se encuentran en un archivo blob en una cuenta de almacenamiento.

La respuesta a un trabajo por lotes es un identificador de trabajo, ya que el trabajo se ejecuta de forma asincrónica. Usaremos el identificador de trabajo para obtener el estado del trabajo y los resultados posteriormente.

**Solicitud de ejemplo**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**Respuesta de ejemplo**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**Código de ejemplo**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2. Obtener el estado o el resultado de un trabajo de ejecución por lotes**

Para obtener el resultado de un trabajo, primero debe tener el identificador del trabajo que estaba en la respuesta al envío de trabajos. No hay ninguna entrada real en esta llamada de API. Utiliza un leve cambio en el URI de BES y en el método de solicitud. En lugar de una solicitud POST, utiliza una solicitud GET después del URI definido en la página de ayuda de la API.
 
Sin embargo, se coloca la respuesta.

**Carga de respuesta**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

Es posible que `StatusCode` tenga un valor de 0, 1, 2, 3 o 4 con la semántica siguiente:

* 0	No iniciado
* 1	En ejecución
* 2	Error
* 3	Cancelado
* 4	Terminado

Si no se ha finalizado el trabajo, `Result` es **null**. Si se ha finalizado el trabajo, `Result` tendría el formulario siguiente: 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

Detalles muestra los detalles del error, si los hay.

**Código de ejemplo**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3. Eliminar un trabajo de ejecución por lotes**              
También es posible eliminar un trabajo una vez que se ha iniciado. Esto se llevaría a cabo por diversas razones, como que el trabajo está tardando demasiado en completarse. Para eliminar un trabajo, primero debe tener el identificador del trabajo, que se incluye en la respuesta al envío del trabajo.

No hay ninguna entrada real en esta llamada de API. Un leve cambio en el URI de BES y en el método de solicitud. En lugar de una solicitud POST, utiliza una solicitud DELETE siguiendo el URI definido en la página de ayuda de la API. El ejemplo de código para esto es muy sencillo.


<!--HONumber=49--> 