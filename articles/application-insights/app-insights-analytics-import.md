---
title: "Importación de datos a Analytics en Azure Application Insights | Microsoft Docs"
description: "Importe datos estadísticos para asociarlos con la telemetría de la aplicación, o bien importe un flujo de datos independiente para consultar con Analytics."
services: application-insights
keywords: "esquema abierto, importación de datos"
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 963e5cfd929f57b34dcb045df82b64f870e897e2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="import-data-into-analytics"></a>Importación de datos a Analytics

Importe los datos tabulares en [Analytics](app-insights-analytics.md), ya sea para combinarlos con la telemetría de [Application Insights](app-insights-overview.md) de la aplicación, o bien para poder analizarlos como un flujo independiente. Analytics es un sólido lenguaje de consulta adecuado para analizar flujos de telemetría con marcas de tiempo de alto volumen.

Puede importar datos en Analytics mediante un esquema propio. No tiene que utilizar los esquemas de Application Insights estándar como solicitud o seguimiento.

Puede importar archivos JSON o DSV (valores separados por delimitadores, como pueden ser comas, puntos y coma o tabulaciones).

Existen tres situaciones donde es útil importar a Analytics:

* **Combinar con la telemetría de la aplicación.** Por ejemplo, puede importar una tabla que asigna las direcciones URL de un sitio web a los títulos de página más legibles. En Analytics, puede crear un informe gráfico de panel que muestra las diez páginas más populares del sitio web. Ahora puede mostrar los títulos de página en lugar de las direcciones URL.
* **Correlacionar la telemetría de la aplicación** con otros orígenes, como tráfico de red, datos de servidor o archivos de registro de red CDN.
* **Aplicar Analytics a un flujo de datos independiente.** Application Insights de Analytics es una herramienta eficaz, que funciona bien con flujos dispersos con marca de tiempo; en muchos casos, de manera mucho mejor que SQL. Si tiene un flujo de este tipo de algún otro origen, puede analizarlo con Analytics.

Enviar datos al origen de datos es fácil. 

1. (Una vez) Defina el esquema de los datos en un "origen de datos".
2. (Periódicamente) Cargue datos en Azure Storage y llame a la API de REST para avisar de que está esperando la ingesta de nuevos datos. En unos pocos minutos, los datos están disponibles para consultarlos en Analytics.

El usuario define la frecuencia de carga y la rapidez con que desea que los datos estén disponibles para consultarlos. Es más eficaz cargar datos en fragmentos más grandes, pero que no superen 1 GB.

> [!NOTE]
> *¿Tiene muchos orígenes de datos para analizar?* [*Considere el uso de* logstash *para enviar los datos a Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Antes de comenzar

Necesita:

1. Un recurso de Application Insights en Microsoft Azure.

 * Si desea analizar los datos por separado de los otro datos de telemetría, [cree un nuevo recurso de Application Insights](app-insights-create-new-resource.md).
 * Si va a combinar o comparar los datos con la telemetría de una aplicación que ya está configurada con Application Insights, puede usar el recurso para esa aplicación.
 * Propietario o colaborador de acceso a ese recurso.
 
2. Azure Storage. Cargue datos en Azure Storage, y Analytics obtiene los datos de ahí. 

 * Se recomienda crear una cuenta de almacenamiento dedicado para los blobs. Si los blobs se comparten con otros procesos, los procesos tardan más tiempo en leer los blobs.


## <a name="define-your-schema"></a>Definición del esquema

Para poder importar datos, debe definir un *origen de datos,* que especifica el esquema de los datos.
Puede tener hasta 50 orígenes de datos en un único recurso de Application Insights.

1. Inicie el Asistente para orígenes de datos. Use el botón "Agregar nuevo origen de datos". También puede hacer clic en el botón de configuración en la esquina superior derecha y elegir "Orígenes de datos" en el menú desplegable.

    ![Agregar nuevo origen de datos](./media/app-insights-analytics-import/add-new-data-source.png)

    Proporcione un nombre para el nuevo origen de datos.

2. Defina el formato de los archivos que va a cargar.

    Puede definir manualmente el formato o cargar un archivo de ejemplo.

    Si los datos tienen el formato CSV, la primera fila del ejemplo puede corresponderse con encabezados de columna. Puede cambiar los nombres de campo en el paso siguiente.

    El ejemplo debe incluir al menos 10 filas o registros de datos.

    Los nombres de campo o columna deben ser alfanuméricos (sin espacios ni signos de puntuación).

    ![Carga de un archivo de ejemplo](./media/app-insights-analytics-import/sample-data-file.png)


3. Revise el esquema que ha obtenido el asistente. Si infirió los tipos de un ejemplo, probablemente tenga que ajustar los tipos inferidos de las columnas.

    ![Revisión del esquema inferido](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Opcional). Cargue una definición de esquema. Consulte el formato siguiente.

 * Seleccione una marca de tiempo. Todos los datos de Analytics deben tener un campo de marca de tiempo. Debe tener el tipo `datetime`, pero no es necesario asignarle el nombre "marca de tiempo". Si los datos tienen una columna que contiene una fecha y hora en formato ISO, elija esta opción como la columna de marca de tiempo. En caso contrario, elija "as data arrived" (cuando llegan los datos), y el proceso de importación agregará un campo de marca de tiempo.

5. Cree el origen de datos.

### <a name="schema-definition-file-format"></a>Formato del archivo de definición de esquema

En lugar de editar el esquema en la IU, puede cargar la definición de esquema de un archivo. El formato de definición de esquema es el siguiente: 

Formato delimitado 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

Formato JSON 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Cada columna se identifica por la ubicación, el nombre y el tipo. 

* Ubicación: Para formato de archivo delimitado es la posición del valor asignado. Para el formato JSON, es el jpath de la clave asignada.
* Nombre: Nombre que se muestra de la columna.
* Tipo: Tipo de datos de esa columna.
 
En caso de que se usen datos de ejemplo y el formato de archivo esté delimitado, la definición de esquema debe asignar todas las columnas y agregar nuevas columnas al final. 

JSON permite una asignación parcial de los datos, por lo que la definición de esquema de formato JSON no tiene que asignar cada clave que se encuentra en los datos de ejemplo. También pueden asignar las columnas que no forman parte de los datos de ejemplo. 

## <a name="import-data"></a>Importar datos

Para importar datos, cárguelos en Azure Storage, cree una clave de acceso para ellos y después realice una llamada a la API de REST.

![Agregar nuevo origen de datos](./media/app-insights-analytics-import/analytics-upload-process.png)

Puede llevar a cabo el siguiente proceso manualmente o configurar un sistema automatizado para hacerlo a intervalos regulares. Debe seguir estos pasos para cada bloque de datos que desea importar.

1. Cargue los datos en [Azure Blob Storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Los blobs pueden tener un tamaño de hasta 1 GB sin comprimir. Los blobs grandes de cientos de MB son ideales desde la perspectiva del rendimiento.
 * Es posible comprimir con Gzip para mejorar el tiempo de carga y la latencia de los datos, a fin de que estén disponibles para consultarlos. Use la extensión de nombre de archivo `.gz`.
 * Se recomienda usar una cuenta de almacenamiento independiente para este fin, con el fin de evitar que se realicen llamadas de distintos servicios, lo que empeora el rendimiento.
 * Al enviar datos de alta frecuencia, cada pocos segundos, se recomienda utilizar más de una cuenta de almacenamiento, por motivos de rendimiento.

 
2. [Cree una clave de firma de acceso compartido para el blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). La clave debe tener un período de caducidad de un día y proporcionar acceso de lectura.
3. Realice una llamada de REST para notificar a Application Insights que hay datos en espera.

 * Punto de conexión: `https://dc.services.visualstudio.com/v2/track`
 * Método HTTP: POST
 * Carga útil:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Los marcadores de posición son:

* `Blob URI with Shared Access Key`: puede obtener esta información con el procedimiento para crear una clave. Es específico para el blob.
* `Schema ID`: el identificador de esquema generado para el esquema definido. Los datos de este blob deben cumplir el esquema.
* `DateTime`: la hora a la que se envía la solicitud, UTC. Se aceptan estos formatos: ISO8601 (como "2016-01-01 13:45:01"); RFC822 ("Wed, 14 Dec 16 14:57:01 +0000"); RFC850 ("Wednesday, 14-Dec-16 14:57:00 UTC"); RFC1123 ("Wed, 14 Dec 2016 14:57:00 +0000").
* `Instrumentation key` del recurso de Application Insights.

Los datos se encuentran disponibles en Analytics después de unos minutos.

## <a name="error-responses"></a>Respuestas de errores

* **400 - Solicitud incorrecta**: indica que la carga útil de solicitud no es válida. Comprobar:
 * Clave de instrumentación correcta.
 * Valor de hora válido. La hora debe reflejarse ahora en UTC.
 * El JSON del evento se ajusta al esquema.
* **403 Prohibido**: no se puede acceder al blob enviado. Asegúrese de que la clave de acceso compartido es válida y no ha expirado.
* **404 No encontrado**:
 * El blob no existe.
 * El identificador de origen es incorrecto.

Se puede encontrar información más detallada en el mensaje de error de respuesta.


## <a name="sample-code"></a>Código de ejemplo

Este código usa el paquete NuGet [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1).

### <a name="classes"></a>Clases

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Introducir datos

Use este código para cada blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>pasos siguientes

* [Paseo por el lenguaje de consulta de Log Analytics](app-insights-analytics-tour.md)
* Si está utilizando Logstash, utilice el [complemento de Logstash para enviar datos a Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
