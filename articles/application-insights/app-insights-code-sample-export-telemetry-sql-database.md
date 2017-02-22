---
title: "Ejemplo: Análisis de los datos exportados desde Azure Application Insights | Microsoft Docs"
description: "Codifique su propio análisis de telemetría en Application Insights de código mediante la característica de exportación continua. Guarde los datos en SQL."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: fedd078402bbd220bce9b71cd035508d46f92f82


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>Ejemplo de código: Análisis de los datos exportados desde Application Insights
En este artículo se muestra cómo escribir código para procesar los datos que se han exportado de [Azure Application Insights][start] mediante la [exportación continua][export]. La Exportación continua traslada la telemetría al Almacenamiento de Azure en formato JSON, por lo que escribiremos algún código para analizar los objetos JSON y crear filas en una tabla de base de datos.

Como ejemplo, escribiremos código para mover los datos de telemetría de Application Insights a SQL Database.

Antes de empezar, tenga en cuenta lo siguiente:

* Un método más eficaz de transferir datos exportados en una base de datos consiste en el [uso de Stream Analytics](app-insights-code-sample-export-sql-stream-analytics.md), pero nuestro objetivo aquí es mostrarle una parte de código para procesar los datos exportados. Se puede adaptar este ejemplo de código para realizar otras operaciones con la telemetría exportada.
* En este ejemplo, movemos los datos a una base de datos de Azure ejecutando el código en un rol de trabajo de Azure. Pero puede adaptar este código para ejecutarlo en un servidor local, para que extraiga los datos en un servidor SQL local.
* Puede [escribir código para acceder a su telemetría directamente](http://dev.applicationinsights.io/) en Application Insights, sin exportarlo.

Si todavía no ha iniciado la supervisión de su aplicación web con Application Insights, [hágalo ahora][start].



## <a name="create-storage-in-azure"></a>Creación de almacenamiento en Azure
Los datos de Application Insights siempre se exportan a una cuenta de almacenamiento de Azure en formato JSON. Es desde este almacenamiento donde el código leerá los datos.

1. Cree una cuenta de almacenamiento "clásica" en su suscripción en [Azure Portal][portal].
   
    ![En el portal de Azure, elija Nuevo, Datos, Almacenamiento.](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. Crear un contenedor
   
    ![En el nuevo almacenamiento, seleccione Contenedores, haga clic en el icono Contenedores y luego, en Agregar.](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicio de la exportación continua al almacenamiento de Azure
1. En el portal de Azure, busque el recurso de Application Insights que ha creado para la aplicación.
   
    ![Elija Examinar, Application Insights, su aplicación.](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. Cree una exportación continua.
   
    ![Elija Configuración, Exportación continua, Agregar.](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    Seleccione la cuenta de almacenamiento que creó anteriormente:

    ![Establezca el destino de exportación.](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    Configure los tipos de eventos que desea ver:

    ![Elija los tipos de evento.](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. Permita que se acumulen algunos datos. Póngase cómo y deje que los usuarios usen su aplicación durante un tiempo. Así, aparecerá la telemetría y verá gráficos estadísticos en el [explorador de métricas](app-insights-metrics-explorer.md) y eventos individuales en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md). 
   
    Y, además, exportará los datos en el almacenamiento. 
2. Inspeccione los datos exportados. En Visual Studio, elija **Ver/Cloud Explorer** y abra Azure/Almacenamiento. (Si no dispone de esta opción de menú, deberá instalar el SDK de Azure: abra el cuadro de diálogo Nuevo proyecto y Visual C#/Nube/Obtener el SDK de Microsoft Azure para. NET.)
   
    ![En Visual Studio, abra Explorador de servidores, Azure, Almacenamiento.](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    Tome nota de la parte común del nombre de la ruta de acceso, que se deriva del nombre de la aplicación y de la clave de instrumentación. 

Los eventos se escriben en archivos de blob en formato JSON. Cada archivo puede contener uno o varios eventos. Así, es probable que queramos leer los datos de eventos y filtrar por los campos que deseemos. Se pueden realizar multitud de acciones con los datos, pero nuestro plan de hoy consiste en escribir código para trasladar los datos a una base de datos SQL. De este modo, será más sencillo ejecutar muchas consultas interesantes.

## <a name="create-an-azure-sql-database"></a>Creación de una Base de datos SQL de Azure
En este ejemplo, escribiremos código para insertar los datos en una base de datos.

De nuevo, empiece desde su suscripción en [Azure Portal][portal], cree la base de datos (y un servidor, a menos que ya tenga uno) donde escribirá los datos.

![Nuevo, Datos, SQL.](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

Asegúrese de que el servidor de base de datos permite el acceso a servicios de Azure:

![Examinar, Servidores, su servidor, Configuración, Firewall, Permitir acceso a Azure.](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>Creación de roles de trabajo
Ahora podemos escribir [código](https://sesitai.codeplex.com/) para analizar el JSON en los blobs exportados y crear registros en la base de datos. Puesto que el almacén de la exportación y la base de datos están en Azure, ejecutaremos el código en un rol de trabajo de Azure.

Este código extrae automáticamente las propiedades que están presentes en JSON. Para obtener descripciones de las propiedades, consulte el [modelo de exportación de datos](app-insights-export-data-model.md).

#### <a name="create-worker-role-project"></a>Creación del proyecto de rol de trabajo
En Visual Studio, cree un nuevo proyecto para el rol de trabajo:

![Nuevo proyecto, Visual C#, Nube, Servicio en la nube de Azure.](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![En el nuevo cuadro de diálogo del servicio en la nube, elija Visual C#, Rol de trabajo.](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>Conexión con la cuenta de almacenamiento
En Azure, obtenga la cadena de conexión a partir de la cuenta de Almacenamiento:

![En la Cuenta de almacenamiento, seleccione Claves y copie la cadena de conexión principal.](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

En Visual Studio, configure las opciones de rol de trabajo con la cadena de conexión de la cuenta de Almacenamiento:

![En el Explorador de soluciones, bajo el proyecto Servicio en la nube, expanda Roles y abra su rol de trabajo. Abra la pestaña de configuración, elija Agregar configuración y establezca name=StorageConnectionString, type=connection string y haga clic para establecer el valor. Configúrelo manualmente y pegue la cadena de conexión.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>Paquetes
En el Explorador de soluciones, haga clic con el botón derecho en el proyecto Rol de trabajo y seleccione Administrar paquetes de NuGet.
Busque e instale estos paquetes: 

* Entity Framework 6.1.2 o posterior: lo utilizaremos para generar el esquema de la tabla de base de datos sobre la marcha, según el contenido de JSON en el blob.
* JsonFx: lo usaremos para acoplar el JSON a las propiedades de clase de C#.

Utilice esta herramienta para generar la clase de C# fuera de nuestro documento JSON sencillo. Esto necesita algunos cambios menores, como acoplar las matrices de JSON a la propiedad sencilla de C# en y a la vez en una columna sencilla de tabla de base de datos (por ejemplo, urlData_port) 

* [Generador de clases de C# de JSON](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>Código
Puede colocar el código en `WorkerRole.cs`.

#### <a name="imports"></a>Importaciones
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>Recuperación de la cadena de conexión de almacenamiento
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>Ejecute el trabajo a intervalos regulares.
Reemplace el método de ejecución existente y, después, elija el intervalo deseado. Debe ser al menos una hora, porque la característica de exportación completa un objeto JSON en una hora.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000

        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### <a name="insert-each-json-object-as-a-table-row"></a>Inserción de cada objeto JSON como una fila de tabla.
    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
             {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
        //handle exception
      }
    }

#### <a name="parse-each-blob"></a>Análisis de cada blob
    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());

        string json;

        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());

          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));

          recCount = entities.Count();
          failureCount = 0; //resetting failure count

          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);

            Dictionary<string, object> dict = new Dictionary<string, object>();

            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");

            switch (FilterType)
            {
              case "PageViewPerformance":

              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
                }

              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }

            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);

            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;

            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### <a name="prepare-a-dictionary-for-each-json-document"></a>Preparación de un diccionario para cada documento JSON
    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }
        }

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>Conversión del documento JSON en propiedades del objeto de telemetría de clase C#
     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }

            return res;
        }

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>Archivo de clase PageViewPerformance generado fuera del documento JSON
    public class PageViewPerformance
    {
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>DBcontext para la interacción de SQL por Entity Framework
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Agregue la cadena de conexión de base de datos con el nombre `TelemetryContext` en `app.config`.

## <a name="schema-information-only"></a>Esquema (solo información)
Este es el esquema para la tabla que se generará para PageView.

> [!NOTE]
> No debe ejecutar este script. Los atributos en JSON determinan las columnas de la tabla.
> 
> 

    CREATE TABLE [dbo].[PageViewPerformances](
    [Id] [uniqueidentifier] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlData_port] [int] NOT NULL,
    [urlData_protocol] [nvarchar](max) NULL,
    [urlData_host] [nvarchar](max) NULL,
    [urlData_base] [nvarchar](max) NULL,
    [urlData_hashTag] [nvarchar](max) NULL,
    [total_value] [float] NOT NULL,
    [networkConnection_value] [float] NOT NULL,
    [sendRequest_value] [float] NOT NULL,
    [receiveRequest_value] [float] NOT NULL,
    [clientProcess_value] [float] NOT NULL,
    [name] [nvarchar](max) NULL,
    [User] [nvarchar](max) NULL,
    [internal_data_id] [nvarchar](max) NULL,
    [internal_data_documentVersion] [nvarchar](max) NULL,
    [context_data_eventTime] [datetime] NULL,
    [context_device_id] [nvarchar](max) NULL,
    [context_device_type] [nvarchar](max) NULL,
    [context_device_os] [nvarchar](max) NULL,
    [context_device_osVersion] [nvarchar](max) NULL,
    [context_device_locale] [nvarchar](max) NULL,
    [context_device_userAgent] [nvarchar](max) NULL,
    [context_device_browser] [nvarchar](max) NULL,
    [context_device_browserVersion] [nvarchar](max) NULL,
    [context_device_screenResolution_value] [nvarchar](max) NULL,
    [context_user_anonId] [nvarchar](max) NULL,
    [context_user_anonAcquisitionDate] [nvarchar](max) NULL,
    [context_user_authAcquisitionDate] [nvarchar](max) NULL,
    [context_user_accountAcquisitionDate] [nvarchar](max) NULL,
    [context_session_id] [nvarchar](max) NULL,
    [context_session_isFirst] [bit] NOT NULL,
    [context_operation_id] [nvarchar](max) NULL,
    [context_location_point_lat] [float] NOT NULL,
    [context_location_point_lon] [float] NOT NULL,
    [context_location_clientip] [nvarchar](max) NULL,
    [context_location_continent] [nvarchar](max) NULL,
    [context_location_country] [nvarchar](max) NULL,
    [context_location_province] [nvarchar](max) NULL,
    [context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


Para ver este ejemplo en acción, [descargue](https://sesitai.codeplex.com/) el código de trabajo completo, cambie la configuración de `app.config` y publique el rol de trabajo en Azure.

## <a name="next-steps"></a>Pasos siguientes

* [Escribir código para acceder a su telemetría directamente](http://dev.applicationinsights.io/)
* [Exportación a SQL con un rol de trabajo](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exportación continua en Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [Modelo de exportación de datos](app-insights-export-data-model.md)
* [Más ejemplos y tutoriales](app-insights-code-samples.md)


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md





<!--HONumber=Jan17_HO4-->


