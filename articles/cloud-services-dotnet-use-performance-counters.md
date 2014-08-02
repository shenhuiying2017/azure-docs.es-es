<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Uso de contadores de rendimiento en Azure
=========================================

Puede utilizar contadores de rendimiento en una aplicación de Azure para recopilar datos que ayuden a determinar cuellos de botella en el sistema y a ajustar el rendimiento de las aplicaciones y los sistemas. Puede recopilar y utilizar los contadores de rendimiento disponibles para Windows Server 2008, Windows Server 2012, IIS y ASP.NET para determinar el estado de la aplicación de Azure.

En este tema se explica cómo habilitar los contadores de rendimiento en su aplicación mediante el uso del archivo de configuración diagnostics.wadcfg. Para obtener información sobre la supervisión del rendimiento de su aplicación en el [Portal de administración de Azure](http://manage.windowsazure.com), consulte [Supervisión de servicios en la nube](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/). Para obtener instrucciones más detalladas acerca de la creación de una estrategia de registro y seguimiento, así como del uso del diagnóstico y otras técnicas para solucionar problemas y optimizar las aplicaciones de Azure, consulte [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh771389.aspx).

Esta tarea incluye los siguientes pasos:

-   [Requisitos previos](#prereqs)
-   [Paso 1: Recopilar y almacenar datos de contadores de rendimiento](#step1)
-   [Paso 2: (opcional) Crear contadores de rendimiento personalizados](#step2)
-   [Paso 3: Consultar datos de contadores de rendimiento](#step3)
-   [Pasos siguientes](#nextsteps)
-   [Recursos adicionales](#additional)

Requisitos previos
------------------

En este artículo se supone que ha importado el monitor de diagnósticos a su aplicación y que ha agregado el archivo de configuración diagnostics.wadcfg a su solución de Visual Studio. Consulte los pasos 1 y 2 en [Habilitación de Diagnósticos en Azure](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/) para obtener más información.

Paso 1: Recopilar y almacenar datos de contadores de rendimiento
----------------------------------------------------------------

Una vez que ha agregado el archivo diagnostics.wadcfg a su solución de Visual Studio, puede configurar la recopilación y el almacenamiento de los datos de contadores de rendimiento en una aplicación de Azure. Para ello, agregue contadores de rendimiento al archivo diagnostics.wadcfg. Los datos de diagnóstico, incluidos los contadores de rendimiento, se recopilan primero en la instancia. Luego, los datos se mantienen en la tabla **WADPerformanceCountersTable** del servicio de tablas de Azure, por lo que también deberá especificar la cuenta de almacenamiento de su aplicación. Si está probando su aplicación de manera local en el emulador de proceso, también puede almacenar datos de diagnóstico localmente en el emulador de almacenamiento. Antes de almacenar datos de diagnóstico, primero debe ir al [Portal de administración de Azure](http://manage.windowsazure.com) y crear una cuenta de almacenamiento. Un procedimiento recomendado es ubicar la cuenta de almacenamiento en la misma ubicación geográfica que su aplicación de Azure, para así evitar pagar costes por ancho de banda externo y reducir la latencia.

### Agregar contadores de rendimiento al archivo diagnostics.wadcfg

Son muchos los contadores de rendimiento que puede recopilar; el ejemplo siguiente muestra varios contadores de rendimiento recomendados para la supervisión de roles de trabajo y web.

Abra el archivo diagnostics.wadcfg y agregue lo siguiente al elemento **DiagnosticMonitorConfiguration**:

     <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />
        <!-- Use the Process(w3wp) category counters in a web role -->
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />
        <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" /> 

		--\>

     <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
        </PerformanceCounters>    

El atributo **bufferQuotaInMB**, que especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para el tipo de recopilación de datos (registros de Azure, registros de IIS, etc.). El valor predeterminado es 0. Cuando se alcanza la cuota, se eliminan los datos más antiguos a medida que se agregan datos nuevos. La suma de todas las propiedades de **bufferQuotaInMB** debe ser mayor que el valor del atributo **OverallQuotaInMB**. Para obtener información más detallada acerca de cómo determinar cuánto almacenamiento será necesario para la recopilación de datos de diagnóstico, consulte la sección Configuración de WAD de [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh771389.aspx).

El atributo **scheduledTransferPeriod**, que especifica el intervalo entre las transferencias programadas de datos, redondeado al minuto más cercano. En los siguientes ejemplos está definido en PT30M (30 minutos). Definir el período de transferencia en un valor pequeño, como un minuto, afectará de manera negativa el rendimiento de la aplicación en producción, pero puede resultar útil para ver los diagnósticos rápidamente cuando está realizando pruebas. El período de transferencia programado debe ser lo suficientemente corto como para tener la seguridad de que los datos de diagnóstico no se sobrescribirán en la instancia, pero lo suficientemente largo como para no afectar el rendimiento de la aplicación.

El atributo **counterSpecifier** especifica el contador de rendimiento que se va a recopilar.

El atributo **sampleRate** especifica la velocidad a la que se debe muestrear el contador de rendimiento; en este caso, 30 segundos.

Una vez que ha agregado los contadores de rendimiento que desea recopilar, guarde los cambios en el archivo diagnostics.wadcfg. A continuación, deberá especificar la cuenta de almacenamiento en que se mantendrán los datos de diagnóstico.

### Especificar la cuenta de almacenamiento

Para mantener la información de diagnóstico en su cuenta de almacenamiento de Azure, debe especificar una cadena de conexión en su archivo de configuración de servicio (ServiceConfiguration.cscfg). Azure Tools para Visual Studio versión 1.4 (agosto de 2011) y posterior le permite tener distintos archivos de configuración (ServiceConfiguration.cscfg) para los entornos local y en la nube. Las configuraciones de servicios múltiples son útiles para los diagnósticos porque puede utilizar el emulador de almacenamiento para una prueba local gratis, mientras se mantiene un archivo de configuración independiente para producción.

Para definir las cadenas de conexión:

1.  Abra el archivo ServiceConfiguration.Cloud.cscfg con el editor de texto de su preferencia y defina la cadena de conexión para su cuenta de almacenamiento:

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    Los valores **AccountName** y **AccountKey** se encuentran en el Portal de administración en el panel de la cuenta de almacenamiento, en **Manage Keys**.

2.  Guarde el archivo ServiceConfiguration.Cloud.cscfg.
3.  Abra el archivo ServiceConfiguration.Local.cscfg y compruebe que el valor **UseDevelopmentStorage** esté definido como verdadero (el valor predeterminado).

        <ConfigurationSettings>

        </ConfigurationSettings>

    Ahora que las cadenas de conexión están definidas, la aplicación mantendrá los datos de diagnóstico en la cuenta de almacenamiento cuando se implemente la aplicación.

4.  Guarde y compile el proyecto y, a continuación, implemente la aplicación.

Paso 2: (opcional) Crear contadores de rendimiento personalizados
-----------------------------------------------------------------

Además de los contadores de rendimiento predefinidos, puede agregar sus propios contadores de rendimiento personalizados para supervisar roles de trabajo o web. Los contadores de rendimiento personalizados se pueden utilizar para realizar un seguimiento y supervisar el comportamiento específico de la aplicación y se pueden crear o eliminar en una tarea de inicio, rol web o rol de trabajo con permisos elevados.

Lleve a cabo los siguientes pasos para crear un contador de rendimiento personalizado simple llamado "\\MyCustomCounterCategory\\MyButton1Counter":

1.  Abra el archivo de definición de servicio (CSDEF) para su aplicación.
2.  Agregue el elemento **Runtime** al elemento **WebRole** o **WorkerRole** para permitir la ejecución con privilegios elevados:

        <Runtime executionContext="elevated" />

3.  Guarde el archivo.
4.  Abra el archivo diagnostics.wadcfg y agregue lo siguiente al elemento **DiagnosticMonitorConfiguration**:

        <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
        </PerformanceCounters>        

5.  Guarde el archivo.
6.  Cree la categoría de contador de rendimiento personalizada en el método **OnStart** del rol antes de invocar **base.OnStart**. Si todavía no existe, el siguiente ejemplo de C\# crea una categoría personalizada:

        public override bool OnStart()
        {
        if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
        {
           CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

           // add a counter tracking user button1 clicks
           CounterCreationData operationTotal1 = new CounterCreationData();
           operationTotal1.CounterName = "MyButton1Counter";
           operationTotal1.CounterHelp = "Mi contador personalizado Button1";
           operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
           counterCollection.Add(operationTotal1);

           PerformanceCounterCategory.Create(
             "MyCustomCounterCategory",
             "Mi categoría de contador personalizada",
             PerformanceCounterCategoryType.SingleInstance, counterCollection);

           Trace.WriteLine("Categoría de contador personalizada creada".);
        }
        else{
           Trace.WriteLine("La categoría de contador personalizada ya existe".);
        }

        return base.OnStart();
        }

7.  Actualice los contadores dentro de la aplicación. En el siguiente ejemplo se actualiza un contador de rendimiento personalizado en eventos de **Button1\_Click**:

        protected void Button1_Click(object sender, EventArgs e)
        {
           PerformanceCounter button1Counter = new PerformanceCounter(
              "MyCustomCounterCategory",
              "MyButton1Counter",
              string.Empty,
              false);

           button1Counter.Increment();
           this.Button1.Text = "Button 1 count: " +
              button1Counter.RawValue.ToString();
        }

8.  Guarde el archivo.

Una vez que se han completado estos pasos, el monitor de diagnósticos de Azure recopila los datos del contador de rendimiento personalizado.

Paso 3: Consultar datos de contadores de rendimiento
----------------------------------------------------

Una vez que la aplicación se ha implementado y está en ejecución, el monitor de diagnóstico comenzará a recopilar contadores de rendimiento y mantendrá esos datos en el almacenamiento de Azure. Utilice herramientas como el **Explorador de servidores de Visual Studio**, [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/) o [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) de Cerebrata para ver los datos de contadores de rendimiento en la tabla **WADPerformanceCountersTable**. También puede consultar el servicio de tablas mediante programación con [C\#][], [Java](http://www.windowsazure.com/en-us/develop/java/how-to-guides/table-service/), [Node.js][], [Python](http://www.windowsazure.com/en-us/develop/python/how-to-guides/table-service/) o [PHP](http://www.windowsazure.com/en-us/develop/php/how-to-guides/table-service/).

El siguiente ejemplo de C\# muestra una consulta simple contra la tabla **WADPerformanceCountersTable** y guarda los datos de diagnóstico en un archivo CSV. Una vez que los contadores de rendimiento se guardan en un archivo CSV, puede utilizar las capacidades de gráficos de Microsoft Excel o alguna otra herramienta para visualizar los datos. Asegúrese de agregar una referencia a Microsoft.WindowsAzure.Storage.dll, que está incluido en el SDK de Azure para .NET de octubre de 2012 y posterior. El ensamblado se instala en el directorio %Program Files%\\Microsoft SDKs\\Windows Azure.NET SDK\\version-num\\ref\\.

     using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Table;

        ...

        // Obtener la cadena de conexión. Cuando se utilizan los Servicios en la nube de Azure, se recomienda 
        // almacenar la cadena de conexión usando el sistema de configuración del servicio de
        // Azure (archivos *.csdef y *.cscfg). Se puede utilizar el tipo CloudConfigurationManager 
        // para recuperar la cadena de conexión de almacenamiento.  Si no se están utilizando los Servicios en la nube, se
        // recomienda almacenar la cadena de conexión en su archivo web.config o app.config.
        // Utilizar el tipo ConfigurationManager para recuperar la cadena de conexión de almacenamiento.
        
        string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
        //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
        // Hacer una referencia a la cuenta de almacenamiento con la cadena de conexión.  También se puede utilizar la cuenta de almacenamiento de desarrollo (emulador de almacenamiento)
        // para depuración local.              
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

        // Crear el cliente de tabla.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

        // Crear el objeto CloudTable que representa la tabla "WADPerformanceCountersTable".
        CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

        // Crear la consulta de tabla y filtrar por un valor específico de CounterName, DeploymentId y RoleInstance.
        TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
            .Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
                    TableOperators.And,
                    TableQuery.CombineFilters(                        
                        TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")                        
                    )
                )
            );

        // Ejecutar la consulta de tabla.
        IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

        // Procesar los resultados de la consulta y crear un archivo CSV.
        StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");
            
        foreach (PerformanceCountersEntity entity in result)
        {
            sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + "," 
                + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
        }

        StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
        sw.Write(sb.ToString());
        sw.Close();

Las entidades se asignan a objetos C\# utilizando una clase personalizada derivada de **TableEntity**. El siguiente código define una clase de entidad que representa un contador de rendimiento en la tabla **WADPerformanceCountersTable**.

     public class PerformanceCountersEntity : TableEntity
        {
            public long EventTickCount { get; set; }
            public string DeploymentId { get; set; }
            public string Role { get; set; }
            public string RoleInstance { get; set; }
            public string CounterName { get; set; }
            public double CounterValue { get; set; }                
        }

Pasos siguientes
----------------

Ahora que ha aprendido los aspectos básicos de la recopilación de contadores de rendimiento, siga estos vínculos para implementar escenarios de solución de problemas más complejos.

-   [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh771389.aspx)
-   [Supervisión de servicios en la nube](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)
-   [Uso del bloque de autoescala de la aplicación](http://www.windowsazure.com/en-us/develop/net/how-to-guides/autoscaling/)
-   [Creación de aplicaciones de nube elásticas y resistentes](http://msdn.microsoft.com/en-us/library/hh680949(PandP.50).aspx)

Recursos adicionales
--------------------

-   [Habilitación de Diagnósticos en Azure](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/)
-   [Recopilación de datos de registro mediante Diagnósticos de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433048.aspx)
-   [Depurar servicios en la nube](http://msdn.microsoft.com/en-us/library/windowsazure/ee405479.aspx)


