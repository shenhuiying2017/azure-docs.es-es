<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Creación del rol web para la aplicación Servicio de correo electrónico de Azure - 3 de 5

Este es el tercer de la serie de cinco que muestra cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie][primer tutorial de la serie].

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Creación de una solución que contenga un proyecto de Servicio en la nube con un rol web y un rol de trabajo
-   Uso de tablas, blobs y colas de Azure en las vistas y los controladores MVC 5
-   Control de conflictos de simultaneidad al usar tablas de Azure

## Apartados de este tutorial

-   [Creación de la solución de Visual Studio][Creación de la solución de Visual Studio]
-   [Actualización del paquete NuGet de la biblioteca de clientes de almacenamiento][Actualización del paquete NuGet de la biblioteca de clientes de almacenamiento]
-   [Configuración de los proyectos para usar el emulador de almacenamiento][Configuración de los proyectos para usar el emulador de almacenamiento]
-   [Configuración de seguimiento y gestión de reinicios][Configuración de seguimiento y gestión de reinicios]
-   [Incorporación de código para crear tablas, colas y contenedores de blobs en el método Application\_Start][Incorporación de código para crear tablas, colas y contenedores de blobs en el método Application\_Start]
-   [Creación y prueba de la lista de distribución de correo][Creación y prueba de la lista de distribución de correo]
-   [Creación y prueba de las vistas y el controlador Subscriber][Creación y prueba de las vistas y el controlador Subscriber]
-   [Creación y prueba de las vistas y el controlador Message][Creación y prueba de las vistas y el controlador Message]
-   [Creación y prueba de las vistas y el controlador Unsubscribe][Creación y prueba de las vistas y el controlador Unsubscribe]
-   [Pasos siguientes][Pasos siguientes]

## <a name="cloudproject"></a>Creación de la solución de Visual Studio

Empiece por crear una solución de Visual Studio con un proyecto para el front-end web y un proyecto para uno de los roles de trabajo back-end de Azure. Agregará el segundo rol de trabajo más adelante.

### Creación de un proyecto del servicio en la nube con un rol web y un rol de trabajo

1.  Inicie Visual Studio.

2.  En el menú **Archivo**, seleccione **Nuevo proyecto**.

    ![Menú Nuevo proyecto][Menú Nuevo proyecto]

3.  Expanda **C\#** y seleccione **Nube** en **Plantillas instaladas** y, a continuación, seleccione **Servicio de nube de Azure**.

4.  Asigne a la aplicación el nombre **AzureEmailService** y haga clic en **OK**.

    ![Cuadro de diálogo Nuevo proyecto][Cuadro de diálogo Nuevo proyecto]

5.  En el cuadro de diálogo **New Azure Cloud Service**, seleccione **Rol web de ASP.NET** y haga clic en la flecha hacia la derecha.

    ![Cuadro de diálogo del nuevo proyecto en nube de Azure][Cuadro de diálogo del nuevo proyecto en nube de Azure]

6.  En la columna de la derecha, mantenga el puntero del mouse sobre **WebRole1** y, a continuación, haga clic en el icono de lápiz para cambiar el nombre del rol web.

7.  Escriba MvcWebRole para el nuevo nombre y, a continuación, presione Entrar.

    ![Cuadro de diálogo del nuevo proyecto en nube de Azure - cambiar el nombre del rol web][Cuadro de diálogo del nuevo proyecto en nube de Azure - cambiar el nombre del rol web]

8.  Siga el mismo procedimiento para agregar un **Rol de trabajo**, asígnele el nombre WorkerRoleA y, a continuación, haga clic en **OK**.

    ![Cuadro de diálogo del nuevo proyecto en nube de Azure - agregar un rol de trabajo][Cuadro de diálogo del nuevo proyecto en nube de Azure - agregar un rol de trabajo]

9.  En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**, active la casilla **Web API** y, a continuación, haga clic en **Cambiar autenticación**.

    ![Cuadro de diálogo Nuevo proyecto][1]

10. En el cuadro de diálogo **Change Authentication**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

    ![Sin autenticación][Sin autenticación]

11. En el cuadro de diálogo **New ASP.NET Project**, haga clic en **Aceptar**.

### Establecimiento del encabezado, el menú y el pie de página

En esta sección puede actualizar los encabezados, pies de página y elementos de menú que se muestran en cada página de la interfaz de usuario web para administradores. La aplicación contendrá tres conjuntos de páginas web para administradores: una para listas de distribución de correo, otra para los suscriptores a las listas de distribución de correo y otra para los mensajes.

1.  Si aún no ha descargado la [solución completa][solución completa], hágalo antes de continuar con el siguiente paso.

    En el resto del tutorial, cuando sea necesario agregar código, copiará los archivos del proyecto descargado en el nuevo proyecto, en lugar de copiar y pegar fragmentos. El tutorial mostrará y explicará las partes principales del código que va a copiar.

    Para agregar un archivo del proyecto descargado, haga clic con el botón secundario en el proyecto al que desea agregar el archivo, o en la carpeta a la que desea agregarlo, y elija **Agregar elemento existente** en el menú contextual. A continuación, vaya al lugar donde ha descargado el proyecto completo, seleccione los archivos que desee y haga clic en **Agregar**. Si aparece el cuadro de diálogo **El archivo de destino ya existe**, haga clic en **Sí**.

2.  En el proyecto MvcWebRole, agregue el archivo *Views\\Shared\_Layout.cshtml* del proyecto descargado (para ello, en *Vistas*, haga clic con el botón secundario en *Compartida*).

    De esta manera se agregan el encabezado, el pie de página y las entradas de menú de las páginas Mailing List, Message y Subscriber:

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### Ejecución de la aplicación de forma local

1.  Presione CTRL+F5 para ejecutar la aplicación.

    Si está acostumbrado a iniciar proyectos web que no son proyectos de servicio en la nube de Azure, observará que se tarda más tiempo de lo normal en ver la página principal en el explorador.

    ![página principal][página principal]

    Esto se debe a que Visual Studio inicia el emulador de equipos y el emulador de almacenamiento, ambos de Azure. Puede ver el icono del emulador de proceso en la bandeja del sistema de Windows:

    ![Emulador de proceso en la bandeja del sistema][Emulador de proceso en la bandeja del sistema]

2.  Cierre el explorador.

## <a name="updatescl"></a>Actualización del paquete NuGet de la biblioteca de clientes de almacenamiento

El marco API que usa para trabajar con tablas, colas y blobs de almacenamiento de Azure es la biblioteca del cliente de almacenamiento (SCL). Esta API se incluye en un paquete NuGet en la plantilla del proyecto del Servicio en la nube. Sin embargo, las actualizaciones de SCL se publican con frecuencia después de que se crean las plantillas de proyecto, así que siempre es bueno comprobar si hay una actualización disponible para el paquete NuGet de SCL.

1.  En el menú **Tools** de Visual Studio, mantenga el mouse sobre **Library Package Manager** y, a continuación, haga clic en **Manage NuGet Packages for Solution**.

    ![Opción para administrar paquetes NuGet para la solución en el menú][Opción para administrar paquetes NuGet para la solución en el menú]

2.  En el panel izquierdo del cuadro de diálogo **Manage NuGet Packages**, seleccione **Actualizaciones**, desplácese hacia abajo hasta el paquete **Almacenamiento de Azure** y haga clic en **Actualizar**.

    ![Paquete Almacenamiento de Azure del cuadro de diálogo para administrar paquetes de NuGet][Paquete Almacenamiento de Azure del cuadro de diálogo para administrar paquetes de NuGet]

3.  En el cuadro de diálogo **Select Projects**, asegúrese de que ambos proyectos están seleccionados y, a continuación, haga clic en **OK**.

    ![Selección de ambos proyectos en el cuadro de diálogo Select Projects][Selección de ambos proyectos en el cuadro de diálogo Select Projects]

4.  Acepte los términos de licencia para completar la instalación del paquete y, a continuación, cierre el cuadro de diálogo **Manage NuGet Packages**.

## <a name="configurestorage"></a>Configuración de los proyectos para usar el emulador de almacenamiento

El código de rol web y rol de trabajo que agregará más adelante usará una cadena de conexión llamada StorageConnectionString para la conexión al Almacenamiento de Azure. En esta sección, agregará el valor a las propiedades del rol y lo configurará para usar el emulador de almacenamiento. El segundo tutorial de esta serie muestra cómo configurar la cadena de conexión para usar una cuenta de almacenamiento de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **MvcWebRole** en la opción **Roles** del proyecto de nube **AzureEmailService** y, a continuación, seleccione **Propiedades**.

    ![Propiedades del rol web][Propiedades del rol web]

2.  Asegúrese de que la opción **All Configurations** está seleccionada en la lista desplegable **Configuración de servicio**.

3.  Seleccione la pestaña **Settings** y, a continuación, haga clic en **Agregar configuración**.

4.  Escriba "StorageConnectionString" en la columna **Name**.

5.  Seleccione **Cadena de conexión** en la lista desplegable **Tipo**.

6.  Haga clic en el botón de los puntos suspensivos (**...**) en el extremo derecho de la línea para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

    ![Clic con el botón secundario en Propiedades][Clic con el botón secundario en Propiedades]

7.  En el cuadro de diálogo **Crear cadena de conexión de almacenamiento**, haga clic en el botón de radio **Emulador de almacenamiento de Azure** y, a continuación, haga clic en **Aceptar**.

    La cadena de conexión `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` está establecida de forma predeterminada en el emulador de almacenamiento, así que no es necesario cambiar eso.

8.  Con el mismo procedimiento que ha usado con MvcWebRole, agregue la cadena de conexión de almacenamiento para el rol WorkerRoleA.

Al agregar una nueva configuración con el botón **Add Settings**, la nueva configuración se agregó al XML en el archivo *ServiceDefinition.csdf* y en cada uno de los dos archivos de configuración *.cscfg*. Visual Studio agrega el siguiente XML al archivo *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

El siguiente XML se agrega a cada archivo de configuración *.cscfg*.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

Puede agregar manualmente la configuración al archivo *ServiceDefinition.csdf* y a los dos archivos de configuración *.cscfg*, pero usar el editor de propiedades tiene las siguientes ventajas para las cadenas de conexión:

-   Solo se agrega la nueva configuración en un lugar y el XML de la configuración correcto se agrega a los tres archivos.
-   El XML correcto se genera para los tres archivos de configuración. El archivo *ServiceDefinition.csdf* define la configuración que debe contener cada archivo de configuración *.cscfg*. Si la configuración del archivo *ServiceDefinition.csdf* y de los archivos de configuración *.cscfg* no es coherente, puede obtener el siguiente mensaje de error de Visual Studio: *El modelo de servicio actual no está sincronizado. Asegúrese de que los archivos de configuración y definición de servicio sean válidos.*

    ![Error de archivos de definición y configuración del servicio no válidos][Error de archivos de definición y configuración del servicio no válidos]

Si obtiene este error, el editor de propiedades no funcionará hasta que resuelva el problema de incoherencia. Para ello, deberá editar manualmente los archivos.

## <a name="tracing"></a>Configuración de seguimiento y gestión de reinicios

1.  En el proyecto MvcWebRole, agregue el archivo *WebRole.cs* del proyecto descargado.

De esta forma se agrega un método que configura el registro, al cual se llama desde el método `OnStart` que se ejecuta cuando se inicia el rol web. El código del nuevo método `ConfigureDiagnostics` se explica en [el segundo tutorial][el segundo tutorial].

También se agrega código que se ejecuta cuando se le notifica al rol web que está a punto de apagarse. Las aplicaciones del Servicio de nube de Azure se reinician aproximadamente dos veces al mes para las actualizaciones del sistema operativo. (Para obtener más información acerca de las actualizaciones del sistema operativo, consulte el blog sobre el [reinicio de las instancias de rol debido a actualizaciones del sistema operativo][reinicio de las instancias de rol debido a actualizaciones del sistema operativo] [en inglés]). Cuando una aplicación web se va a apagar, se genera un evento `OnStop`. El texto reutilizable del rol web creado por Visual Studio no sobrescribe el método `OnStop`, por lo que la aplicación tendrá solo unos segundos para terminar de procesar las solicitudes HTTP antes de apagarse. Puede agregar código para sobrescribir el método `OnStop` a fin de garantizar el control correcto de los apagados.

El archivo que acaba de agregar contiene la siguiente anulación del método `OnStop`.

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

El método `OnStop` tiene hasta 5 minutos para salir antes de que la aplicación se apague. Podría agregar una llamada de suspensión de 5 minutos al método `OnStop` a fin de dar a la aplicación la máxima cantidad de tiempo para procesar las solicitudes actuales, pero si la aplicación se escala correctamente, debe tener la capacidad de procesar las solicitudes restantes en mucho menos de 5 minutos. Lo mejor es detener el proceso lo antes posible, a fin de que la aplicación pueda reiniciarse cuanto antes para continuar con el procesamiento de las solicitudes.

Cuando Azure pone el rol sin conexión, el equilibrador de carga deja de enviar solicitudes a la instancia de rol y, después de eso, se realiza la llamada al método `OnStop`. Si no tiene otra instancia del rol, no se procesará ninguna solicitud hasta que el rol se apague y se reinicie (un proceso que suele durar varios minutos). Ese es un motivo por el que el contrato de nivel de servicio de Azure requiere disponer al menos de dos instancias de cada rol a fin de poder beneficiarse de la garantía de tiempo de actividad.

En el código que aparece para el método `OnStop`, se crea un contador de rendimiento de ASP.NET para `Requests Current`. El valor de contador `Requests Current` contiene el número actual de solicitudes, incluidas las que están en cola, las que se encuentran en ejecución actualmente o las que están pendientes de escribirse en el cliente. El valor `Requests Current` se comprueba cada segundo, y cuando llega a cero, se devuelve el método `OnStop`. Cuando se devuelve `OnStop`, el rol se apaga.

Los datos de seguimiento no se guardan cuando la llamada se realiza desde el método `OnStop` sin realizar una [transferencia a petición][transferencia a petición]. Puede ver la información de seguimiento de `OnStop` en tiempo real con la utilidad [dbgview][dbgview] desde una conexión a Escritorio remoto.

## <a name="createifnotexists"></a>Incorporación de código para crear tablas, colas y contenedores de blobs en el método Application\_Start

La aplicación web usará la tabla `MailingList`, la tabla `Message`, la cola `azuremailsubscribequeue` y el contenedor de blobs `azuremailblobcontainer`. Podría crearlos manualmente mediante una herramienta como el Explorador de almacenamiento de Azure, pero en su caso tendría que hacerlo manualmente cada vez que empezara a usar la aplicación con una nueva cuenta de almacenamiento. En esta sección, agregará código que se ejecuta cuando la aplicación se inicia, comprueba si existen las tablas, las colas y los contenedores de blobs necesarios y los crea en caso de que no existan.

Podría agregar este código de inicio de un solo uso al método `OnStart` en los archivos *WebRole.cs* o *Global.asax*. En este tutorial, inicializará el Almacenamiento de Azure en el archivo *Global.asax*.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto MvcWebRole, y agregue el archivo *Global.asax.cs* del proyecto descargado.

Ha agregado un nuevo método al que se llama desde el método `Application_Start`:

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

En las siguientes secciones, va a compilar los componentes de la aplicación web; puede probarlos con el almacenamiento de desarrollo o la cuenta de almacenamiento sin tener que crear primero manualmente tablas, colas o contenedores de blobs.

## <a name="mailinglist"></a>Creación y prueba de las vistas y el controlador Subscriber

Los administradores usan la interfaz de usuario web **Mailing List** para crear, editar y mostrar listas de distribución de correo, como "Contoso University History Department announcements" y "Fabrikam Engineering job postings".

### Incorporación de la clase de entidad MailingList a la carpeta Modelos

La clase de entidad `MailingList` se usa para las filas de la tabla `MailingList` que contienen información acerca de la lista, como su descripción y la dirección "De" para los correos electrónicos enviados a la lista.

1.  En la carpeta `Models` del proyecto MVC, agregue el archivo *MailingList.cs* del proyecto descargado.

    Usará la clase de entidad `MailingList` para leer y escribir filas de lista de distribución de correo en la tabla mailinglist.

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    La API de Almacenamiento de Azure requiere que las clases de entidad para las operaciones de tabla se obtengan de [TableEntity][TableEntity]. `TableEntity` define los campos `PartitionKey`, `RowKey`, `TimeStamp` y `ETag`. Las propiedades `TimeStamp` y `ETag` son las que utiliza el sistema. Verá cómo se usa la propiedad `ETag` para el control de simultaneidad más adelante en el tutorial.

    (También existe la clase [DynamicTableEntity][DynamicTableEntity] para usarla si desea trabajar con filas de tabla como colecciones de diccionario de pares clave-valor en lugar de usar clases de modelo predefinidas. Para obtener más información, consulte [Azure Storage Client Library 2.0 Tables Deep Dive][Azure Storage Client Library 2.0 Tables Deep Dive]).

    La clave de partición de tabla `mailinglist` es el nombre de lista. En esta clase de entidad, se puede obtener acceso al valor de la clave de partición con la propiedad `PartitionKey` (definida en la clase `TableEntity`) o con la propiedad `ListName` (definida en la clase `MailingList`). La propiedad `ListName` usa `PartitionKey` como su variable de respaldo. La definición de la propiedad `ListName` le permite usar un nombre de variable más descriptivo en el código y facilita la programación de la interfaz de usuario web, ya que los atributos DataAnnotations de validación y formato se pueden agregar a la propiedad `ListName`, pero no se pueden agregar directamente a la propiedad `PartitionKey`.

    El atributo `RegularExpression` de la propiedad `ListName` da lugar a que MVC valide la entrada de usuario a fin de garantizar que el valor de nombre de lista escrito solo contiene caracteres alfanuméricos o subrayado. Esta restricción se implementó a fin de mantener nombres de lista sencillos que se puedan usar con facilidad en las cadenas de consulta en las URL.

    > [WACOM.NOTE] Si desea que el formato del nombre de lista sea menos restrictivo, podría admitir otros caracteres y nombres de lista codificados como URL cuando se usen en las cadenas de consulta. No obstante, no se permiten determinados caracteres en las claves de fila o de partición de tablas de Azure, por lo que tendría que excluir al menos dichos caracteres. Para obtener más información acerca de los caracteres no admitidos o que causan problemas en los campos de clave de fila o de partición, consulte [Introducción al modelo de datos del servicio Tabla][Introducción al modelo de datos del servicio Tabla] y el blog [% Character in PartitionKey or RowKey][% Character in PartitionKey or RowKey].

    La clase `MailingList` define un constructor predeterminado que establece `RowKey` como la cadena codificada de forma rápida "mailinglist", porque todas las filas de la lista de distribución de correo de esta tabla tienen dicho valor como la clave de fila. (Para obtener una explicación de la estructura de la tabla, consulte el [primer tutorial de la serie][primer tutorial de la serie]). Se podría haber elegido cualquier valor constante para este propósito, siempre que no hubiera coincidido con una dirección de correo electrónico, que es la clave de fila para las filas de suscriptor de esta tabla.

    El nombre de lista y la dirección de correo electrónico "De" siempre deben escribirse cuando se crea una nueva entidad `MailingList`, por lo que tienen atributos `Required`.

    Los atributos `Display` especifican la leyenda predeterminada que se usará para un campo en la interfaz de usuario de MVC.

### Incorporación del controlador MVC de MailingList

1.  En la carpeta *Controladores* del proyecto MVC, agregue el archivo *MailingListController.cs* del proyecto descargado.

    El constructor predeterminado del controlador crea un objeto `CloudTable` que se usa para trabajar con la tabla `mailinglist`.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    El código obtiene las credenciales para la cuenta de almacenamiento de Azure del archivo de configuración del proyecto del Servicio en la nube a fin de realizar una conexión a la cuenta de almacenamiento. (Realizará esta configuración más adelante en este tutorial, antes de probar el controlador).

    A continuación, hay un método `FindRowAsync` al que se llama siempre que el controlador necesita buscar una entrada de lista de distribución de correo específica de la tabla `MailingList`, por ejemplo para editar una entrada de la lista de distribución de correo. El código recupera una entidad `MailingList` exclusiva con la utilización de valores de clave de fila y de clave de partición que se le transfieren. Las filas que este controlador edita son las que tienen "MailingList" como la clave de fila, por lo que "MailingList" podría haberse codificado de forma rápida para la clave de fila, pero la definición de la clave de partición y de la clave de fila es un patrón usado para los métodos `FindRow` en todos los controladores.

    > [WACOM.NOTE] La aplicación usa el código asincrónico de ASP.NET 4.5 para las operaciones de E/S en el rol web a fin de usar los recursos de servidor de manera eficiente. Para obtener información acerca del código asincrónico en la aplicación web, consulte el apartado acerca de [cómo usar la compatibilidad con el código asincrónico de .NET 4.5 para evitar que se bloqueen las llamadas][cómo usar la compatibilidad con el código asincrónico de .NET 4.5 para evitar que se bloqueen las llamadas].

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    El código de este método `FindRow` devuelve una fila de la lista de distribución de correo. El código del método `FindRow` correspondiente del controlador `Subscriber` devuelve una fila de suscriptor de la misma tabla `mailinglist`. El código de los dos métodos es idéntico a excepción del tipo de modelo usado con el método [TableOperation.Retrieve][TableOperation.Retrieve].

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    El objeto `TableOperation` que devuelve el método `TableOperation.Retrieve` especifica el esquema (las propiedades) de la fila o las filas que espera que la consulta devuelva. Una única tabla puede tener diferentes esquemas en distintas filas. Normalmente, se especifica el mismo tipo de modelo al leer una fila usada para crear la fila.

    En la página **Index** se muestran todas las filas de lista de distribución de correo, de forma que la consulta del método `Index` devuelve todas las entidades `MailingList` que tienen "mailinglist" como la clave de fila (las otras filas de la tabla tienen la dirección de correo electrónico como la clave de fila y contienen información acerca del suscriptor).

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    El método `ExecuteQuerySegmentedAsync` divide los grandes conjuntos de resultados en segmentos. Se devuelven hasta 1000 filas. Cuando ejecuta una consulta que recupera más de 1.000 filas, obtiene 1.000 filas y un token de continuación. Puede usar el token de continuación para ejecutar otra consulta que se inicia donde se interrumpe la anterior. El código mostrado se simplifica para una aplicación de ejemplo: todos los segmentos se agregan a una lista. En una aplicación de producción, implementaría código de página. Para obtener más información acerca de los grandes conjuntos de resultados y tokens de continuación, consulte [How to get most out of Azure Tables][How to get most out of Azure Tables] y [Azure Tables: Expect Continuation Tokens, Seriously][Azure Tables: Expect Continuation Tokens, Seriously].

    Cuando crea el objeto `OperationContext`, puede establecer el valor de la propiedad `ClientID` con el fin de proporcionar un identificador exclusivo que se incluirá en los registros escritos por Almacenamiento de Azure. Puede usar este identificador para realizar el seguimiento de los registros de las operaciones de almacenamiento hasta encontrar el código que ha ocasionado la actividad del servicio de almacenamiento. Para obtener información acerca del registro de Almacenamiento de Azure, consulte [Registro de Almacenamiento de Azure: Uso de registros para realizar el seguimiento de las solicitudes de almacenamiento][Registro de Almacenamiento de Azure: Uso de registros para realizar el seguimiento de las solicitudes de almacenamiento].

    Con SCL 2.1 y la última API, puede usar también LINQ en las consultas de tabla. Para ver un ejemplo de código que muestra como usar LINQ, consulte [PhluffyFotos][PhluffyFotos].

    Si no especifica una directiva de reintento, la API automáticamente hace tres reintentos de aumentar de manera exponencial los límites de tiempo de espera. Si se trata de una interfaz web en la que un usuario espera que aparezca una página, esto podría suponer largos tiempos de espera inaceptables. Por tanto, este código especifica reintentos lineales (para que el límite de tiempo de espera no aumente en cada ocasión) y un límite de tiempo de espera que resulta razonable para el usuario. La directiva de reintento se especifica en el objeto `webUIRetryPolicy` que se pasa al método `ExecuteQuerySegmentedAsync`. El objeto `webUIRetryPolicy` se define en el constructor del controlador:

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    El método `Index` incluye un bloque try-catch que está diseñado para controlar las condiciones de tiempo de espera.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    Cuando el usuario hace clic en el botón **Crear** de la página **Crear**, el enlazador de modelos de MVC crea una entidad `MailingList` a partir de la entrada escrita en la vista y el método `HttpPost Create` agrega la entidad a la tabla.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    Para la página **Editar**, el método `HttpPost Edit` actualiza la fila

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    En el método `HttpPost Edit` un bloque try-catch controla los errores de simultaneidad.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    Se genera una excepción de simultaneidad si un usuario selecciona una lista de distribución de correo para editarla; a continuación, mientras la página **Edit** se muestra en el explorador, otro usuario edita la misma lista de distribución de correo. Cuando eso ocurre, el código muestra un mensaje de advertencia e indica qué campos ha modificado el otro usuario. La API de TSL usa `ETag` para comprobar los conflictos de simultaneidad. Cada vez que se actualiza una fila de la tabla, el valor `ETag` cambia. Si obtiene una fila para editarla, se guarda el valor `ETag` y, al ejecutar una operación de actualización o eliminación, se reemplaza el valor `ETag` guardado. (La vista `Edit` tiene un campo oculto para el valor ETag). Si la operación de actualización identifica que el valor `ETag` del registro que se va a actualizar difiere del valor `ETag` transferido a la operación de actualización, genera una excepción de simultaneidad. Si no le preocupan los conflictos de simultaneidad, puede definir el campo ETag con un asterisco ("\*") en la entidad transferida a la operación de actualización, de forma que se ignorarán los conflictos.

    Nota: el error HTTP 412 no es exclusivo para los errores de simultaneidad. La API de SCL también puede generarlo para otros errores.

    Para la página **Eliminar**, el método `HttpPost Delete` elimina la fila `MailingList` junto con cualquier fila `Subscriber` a la que está asociada en la tabla `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    En caso de que sea preciso eliminar una gran cantidad de suscriptores, el código elimina los registros por lotes. El coste de la transacción que conlleva eliminar una fila es el mismo en que se incurre con la eliminación de 100 filas en un lote. El número máximo de operaciones que puede ejecutar en un lote es 100.

    Aunque el bucle procesa tanto las filas `MailingList` como las filas `Subscriber`, las lee todas en la clase de entidad `MailingList` porque los únicos campos necesarios para la operación `Delete` son `PartitionKey`, `RowKey` y `ETag`.

### Incorporación de las vistas MVC de MailingList

1.  En la carpeta *Vistas* del proyecto MVC, cree una nueva carpeta llamada *MailingList*.

2.  En la nueva carpeta *Vistas\\MailingList*, agregue los cuatro archivos *.cshtml* del proyecto descargado.

En el archivo *Edit.cshtml*, tenga en cuenta el campo oculto incluido para preservar el valor `ETag` que se usa para controlar los conflictos de simultaneidad.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

Observe también que el campo `ListName` tiene una aplicación auxiliar `DisplayFor` en lugar de una aplicación auxiliar `EditorFor`.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

No habilitamos la página **Edit** para cambiar el nombre de lista, porque eso podría haber requerido código complejo en el controlador: el método `HttpPost Edit` tendría que haber eliminado la fila de la lista de distribución de correo existente y todas las filas de suscriptor asociadas y reinsertarlas todas con el nuevo valor de clave. En una aplicación de producción, puede decidir que la complejidad adicional merezca la pena. Como observará más adelante, el controlador `Subscriber` permite cambios del nombre de lista, porque solo se ve afectada una fila al mismo tiempo.

En el archivo *Index.cshtml*, los hipervínculos **Edit** y **Delete** especifican los parámetros de la cadena de consulta de clave de fila y de clave de partición a fin de identificar una fila específica. Para entidades `MailingList`, solo se necesita realmente la clave de partición, porque la clave de fila siempre es "MailingList", pero ambas se mantienen a fin de que el código de vista MVC sea coherente entre todos los controladores y las vistas.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### Conversión de MailingList en el controlador predeterminado

1.  Abra *Route.config.cs* en la carpeta *App\_Start*.

2.  En la línea que especifica valores predeterminados, cambie el controlador predeterminado de "Home" a "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto.

    ![Página de índice de MailingList vacía][Página de índice de MailingList vacía]

2.  Use la función **Create** para agregar algunas listas de distribución de correo y pruebe las funciones **Edit** y **Delete** para asegurarse de que funcionan.

    ![Página de índice de MailingList con filas][Página de índice de MailingList con filas]

## <a name="subscriber"></a><span class="short-header">Subscriber</span>Creación y prueba de las vistas y el controlador Subscriber

Los administradores usan la interfaz de usuario web **Subscriber** para agregar nuevos suscriptores a una lista de distribución de correo y para editar, mostrar y eliminar los suscriptores existentes.

### Incorporación de la clase de entidad Subscriber a la carpeta Modelos

La clase de entidad `Subscriber` se usa para las filas de la tabla `MailingList` que contienen información acerca de los suscriptores de una lista. Estas filas contienen información como la dirección de correo electrónico de la persona y si se ha comprobado la dirección.

1.  En la carpeta *Modelos* del proyecto MVC, agregue el archivo *Subscriber.cs* del proyecto descargado.

Al igual que la clase de entidad `MailingList`, la clase de entidad `Subscriber` se usa para leer y escribir filas en la tabla `mailinglist`.

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

Las filas `Subscriber` usan la dirección de correo electrónico en lugar del valor "mailinglist" constante para la clave de fila. (Para obtener una explicación de la estructura de la tabla, consulte el [primer tutorial de la serie][primer tutorial de la serie]). Por tanto, se define una propiedad `EmailAddress` que usa la propiedad `RowKey` como su campo de respaldo de la misma forma que `ListName` usa `PartitionKey` como su campo de respaldo. Como se ha explicado anteriormente, esto le permite colocar los atributos DataAnnotations de formato y validación en las propiedades.

El valor `SubscriberGUID` se genera cuando se crea una entidad `Subscriber`. Se usa en los vínculos de suscripción y cancelación de suscripción a fin de garantizar que solo las personas autorizadas puedan suscribir direcciones de correo electrónico o cancelar su suscripción.

Cuando se crea inicialmente una fila para un nuevo suscriptor, el valor de `Verified` es `false`. El valor `Verified` cambia a `true` solo después de que el nuevo suscriptor haga clic en el hipervínculo **Confirm** del correo electrónico de bienvenida. Si se envía un mensaje a una lista mientras un suscriptor tiene el valor `Verified` = `false`, no se envía ningún correo electrónico a dicho suscriptor.

La propiedad `Verified` de la entidad `Subscriber` se define para aceptar valores null. Si especifica que una consulta debe devolver las entidades `Subscriber`, es posible que algunas de las filas recuperadas puedan no tener una propiedad `Verified`. Por tanto, la entidad `Subscriber` define su propiedad `Verified` para que acepte valores null, a fin de que pueda reflejar con mayor precisión el contenido real de una fila si una consulta devuelve las filas de la tabla que no tienen una propiedad *Verified*. Es posible que esté habituado a usar tablas de SQL Server, en las que cada fila de una tabla tiene el mismo esquema. En una tabla de Almacenamiento de Azure, cada fila es solo una recopilación de propiedades y cada fila puede tener un conjunto diferente de propiedades. Por ejemplo, en la aplicación de ejemplo Servicio de correo electrónico de Azure, las filas que tienen "MailingList" como la clave de fila no tienen una propiedad `Verified`. Si una consulta devuelve una fila de tabla que no tiene una propiedad `Verified`, cuando se crea una instancia de la clase de entidad `Subscriber`, la propiedad `Verified` del objeto de entidad será null. Si la propiedad no acepta valores null, obtendría el mismo valor de `false` para las filas que tienen `Verified` = `false` y para las filas que no tienen una propiedad `Verified` en ningún momento. Por tanto, un procedimiento recomendado para usar tablas de Azure es que cada propiedad de una clase de entidad acepte valores null a fin de leer con precisión las filas que se crearon con el uso de clases de entidades diferentes o versiones distintas de la clase de entidad actual.

### Incorporación del controlador MVC de Subscriber

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Controladores* del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *SubscriberController.cs* de la carpeta *Controladores* y haga clic en **Agregar**. (Asegúrese de que obtiene *Subscriber.cs* y no *Subscribe.cs*; agregará *Subscribe.cs* más adelante).

La mayoría del código de este controlador es similar al que se observa en el controlador `MailingList`. Incluso el nombre de tabla es el mismo, porque la información del suscriptor se mantiene en la tabla `MailingList`.

Además del método `FindRowAsync`, hay un método `FindRow`, dado que es necesario llamarlo desde un bloque catch ya que no es posible llamar un método asincrónico desde un bloque de este tipo.

Después de los métodos `FindRow`, verá un método `GetListNamesAsync`. Este método obtiene los datos de una lista desplegable de las páginas **Create** y **Edit**, donde puede seleccionar la lista de distribución de correo a la que suscribir una dirección de correo electrónico.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

Se trata de la misma consulta que se observa en el controlador `MailingList`. Para la lista desplegable, desea filas que contienen información acerca de las listas de distribución de correo, por lo que solo selecciona las que tienen la propiedad RowKey = "mailinglist".

Para el método que recupera datos para la página **Index**, desea las filas que tienen información sobre el suscriptor, por lo que selecciona todas las filas que no tienen la propiedad RowKey = "MailingList".

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

Tenga en cuenta que la consulta especifica que los datos se leerán en los objetos `Subscriber` (con la especificación de `<Subscriber>`), pero los datos se leerán desde la tabla `mailinglist`.

El número de suscriptores podría aumentar hasta ser demasiado elevado para controlarse de esta forma en una sola consulta. Como se ha observado anteriormente, en una aplicación de producción implementaría la paginación mediante tokens de continuación.

En el método `HttpGet Create`, debe configurar los datos para la lista desplegable y, en el método `HttpPost`, debe definir los valores antes de guardar la nueva entidad.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

La página `HttpPost Edit` es más compleja que la que ve en el controlador `MailingList`, porque la página `Subscriber` le permite cambiar el nombre de lista o la dirección de correo electrónico; ambos son campos de clave. Si el usuario cambia uno de estos campos, tiene que eliminar el registro existente y agregar uno nuevo en lugar de actualizar el registro existente. El siguiente código muestra la parte del método de edición que controla los diferentes procedimientos de la clave frente a los cambios no correspondientes a la clave:

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

Los parámetros que el enlazador de modelos de MVC transfiere al método `Edit` incluyen los valores originales de dirección de correo electrónico y nombre de lista (en los parámetros `partitionKey` y `rowKey`) y los valores escritos por el usuario (en los parámetros `listName` y `emailAddress`):

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

Los parámetros transferidos al método `UpdateModel` excluyen las propiedades `PartitionKey` y `RowKey` del enlace de modelos:

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

El motivo es que las propiedades `ListName` y `EmailAddress` usan `PartitionKey` y `RowKey` como propiedades de respaldo y el usuario puede haber cambiado alguno de estos valores. Cuando el enlazador de modelos actualiza el modelo mediante la configuración de la propiedad `ListName`, la propiedad `PartitionKey` se actualiza automáticamente. Si el enlazador de modelos actualizara la propiedad `PartitionKey` con el valor original de dicha propiedad tras actualizar la propiedad `ListName`, sobrescribiría el valor nuevo definido por la propiedad `ListName`. La propiedad `EmailAddress` actualiza automáticamente la propiedad `RowKey` de la misma forma.

Después de actualizar el objeto de modelo `editedSubscriber`, el código determina si se ha cambiado la clave de partición o la clave de fila. Si cualquier valor de clave ha cambiado, la fila de suscriptor existente tiene que eliminarse e insertarse una nueva. Si solo cambia la clave de fila, la eliminación y la inserción pueden realizarse en una transacción por lotes atómica.

Tenga en cuenta que el código crea una entidad nueva para pasarla a la operación `Delete`:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

Las entidades que se transfieren a operaciones de un lote deben ser distintas. Por ejemplo, no puede crear una entidad `Subscriber`, transferirla a una operación `Delete`, cambiar un valor en la misma entidad `Subscriber` y pasarlo a una operación `Insert`. Si lo ha hecho, el estado de la entidad después del cambio de la propiedad se aplicaría para las operaciones de eliminación e inserción.

Las operaciones de un lote deben estar en la misma partición. Habida cuenta de que un cambio en el nombre de lista modifica la clave de partición, no puede realizarse en una transacción.

### Incorporación de las vistas MVC de Subscriber

1.  En el **Explorador de soluciones**, cree una carpeta nueva en la carpeta *Vistas* del proyecto MVC y asígnele el nombre *Subscriber*.

2.  Haga clic con el botón secundario en la carpeta *Vistas\\Subscriber* nueva y seleccione **Agregar elemento existente**.

3.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione los cinco archivos .cshtml de la carpeta *Vistas\\Subscriber* y haga clic en **Agregar**.

En el archivo *Edit.cshtml*, se incluye un campo oculto para el valor `SubscriberGUID` , dado que ese campo no se muestra y, por lo tanto, no se proporciona automáticamente en un campo de formulario

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto y, a continuación, haga clic en **Subscribers**.

    ![Página de índice de Subscriber vacía][Página de índice de Subscriber vacía]

2.  Use la función **Create** para agregar algunas listas de distribución de correo y pruebe las funciones **Edit** y **Delete** para asegurarse de que funcionan.

    ![Página de índice de Subscribers con filas][Página de índice de Subscribers con filas]

## <a name="message"></a>Creación y prueba de las vistas y el controlador Message

Los administradores usan la interfaz de usuario web **Message** para crear, editar y mostrar información acerca de los mensajes programados para enviarlos a las listas de distribución de correo.

### Incorporación de la clase de entidad Message a la carpeta Modelos

1.  En la carpeta *Modelos* del proyecto MVC, agregue el archivo *Message.cs* del proyecto descargado.

La clase de entidad `Message` se usa para las filas de la tabla `Message` que contienen información acerca del mensaje previsto para enviarlo a una lista. Estas filas incluyen información como la línea de asunto, la lista a la que enviar un mensaje y la fecha programada para enviarlo.

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

La clase `Message` define un constructor predeterminado que define la propiedad `MessageRef` con un valor exclusivo para el mensaje. Habida cuenta de que el valor forma parte de la clave de fila, el establecedor de la propiedad `MessageRef` también define automáticamente la propiedad `RowKey`. El establecedor de propiedad `MessageRef` concatena el "mensaje" literal y el valor `MessageRef` y coloca tal concatenación en la propiedad `RowKey`.

El valor `MessageRef` se crea al obtener el valor `Ticks` de `DateTime.Now`. Esto garantiza que, de forma predeterminada, cuando se muestran los mensajes en la interfaz de usuario web, estos aparezcan en el orden en que se crearon para una fecha programada determinada (`ScheduledDate` es la clave de partición). Podría usar un GUID para hacer que las filas del mensaje sean únicas, pero entonces el orden de recuperación predeterminado sería aleatorio.

El constructor predeterminado también establece el estado predeterminado de Pending para las nuevas filas `message`.

Para obtener más información acerca de la estructura de tabla `Message`, consulte el [primer tutorial de la serie][primer tutorial de la serie].

### Incorporación del controlador MVC de Message

1.  En la carpeta *Controladores* del proyecto MVC, agregue el archivo *MessageController.cs* del proyecto descargado.

La mayoría del código de este controlador es similar al que se observa en el controlador `Subscriber`. Lo nuevo aquí es el código para trabajar con blobs. Para cada mensaje, el contenido HTML y de texto sin formato del correo electrónico se carga en el formulario de los archivos .htm y .txt y se almacena en blobs.

Los blobs se almacenan en contenedores. La aplicación Servicio de correo electrónico de Azure almacena todos sus blobs en un único contenedor con nombre "azuremailblobcontainer" y el código del constructor del controlador obtiene una referencia a este contenedor de blobs:

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

Para cada archivo que un usuario selecciona para cargarlo, la vista MVC proporciona un objeto `HttpPostedFile` que contiene información acerca del archivo. Cuando el usuario crea un mensaje nuevo, el objeto `HttpPostedFile` se usa para guardar el archivo en un blob. Cuando el usuario edita un mensaje, el usuario puede elegir entre cargar un archivo de sustitución o dejar el blob sin modificar.

El controlador incluye un método al que los métodos `HttpPost Create` y `HttpPost Edit` llaman para guardar un blob:

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

El método `HttpPost Create` guarda los dos blobs y, a continuación, agrega la fila de tabla `Message`. El nombre de los blobs se asigna mediante la concatenación del valor `MessageRef` con la extensión de nombre de archivo ".htm" o ".txt".

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

El método `HttpGet Edit` valida que el mensaje recuperado tenga el estado `Pending`, a fin de que el usuario no pueda cambiar el mensaje una vez que el rol de trabajo B ha empezado a procesarlo. Hay código similar en el método `HttpPost Edit` y en los métodos `Delete` y `DeleteConfirmed`.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

En el método `HttpPost Edit`, el código guarda un blob nuevo solo si el usuario elige cargar un archivo nuevo.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

Si se cambia la fecha programada, la clave de partición también cambia y es necesario eliminar e insertar una fila. Esto no puede realizarse en una transacción porque afecta a más de una partición.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

El método `HttpPost Delete` elimina los blobs si elimina la fila de la tabla:

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Incorporación de las vistas MVC de Message

1.  En el proyecto MVC, cree una nueva carpeta en *Vistas* llamada `Message`.

2.  En la nueva carpeta *Vistas\\Message*, agregue los cinco archivos *.cshtml* del proyecto descargado.

El método `HttpPost Edit` necesita la clave de partición y la clave de fila, así que el código del archivo *Edit.cshtml* las proporciona en campos ocultos.

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

Los campos ocultos no eran necesarios en el controlador `Subscriber` porque (a) las propiedades `ListName` y `EmailAddress` del modelo `Subscriber` actualizan las propiedades `PartitionKey` y `RowKey`, y (b) las propiedades `ListName` y `EmailAddress` se incluían con las aplicaciones auxiliares `EditorFor` en la vista Edit. Cuando el enlazador de modelos de MVC del modelo `Subscriber` actualiza la propiedad `ListName`, la propiedad `PartitionKey` se actualiza automáticamente, y cuando el enlazador de modelos de MVC actualiza la propiedad `EmailAddress` del modelo `Subscriber`, la propiedad `RowKey` se actualiza automáticamente. En el modelo `Message`, los campos que se asignan a la clave de partición y a la clave de fila no son campos editables, por lo que no se definen de la misma manera.

También se incluye un campo oculto para la propiedad `MessageRef`. Se trata del mismo valor que la clave de partición, pero se incluye para que el método `HttpPost Edit`. Incluir el campo oculto `MessageRef` permite que el código del método `HttpPost Edit` haga referencia al valor `MessageRef` mediante dicho nombre cuando construye los nombres de archivo para los blobs.

La vista **Índice** de Message en el archivo *Index.cshtml* es diferente de las otras vistas de **Índice** en que los vínculos **Edit** y **Delete** solo se muestran para los mensajes que tienen el estado `Pending`:

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

Esto ayuda a prevenir que el usuario realice cambios en un mensaje después de que el rol de trabajo A haya comenzado a procesarlo.

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto y, a continuación, haga clic en **Messages**.

    ![Página de índice de Message vacía][Página de índice de Message vacía]

2.  Use la función **Create** para agregar algunas listas de distribución de correo y pruebe las funciones **Edit** y **Delete** para asegurarse de que funcionan.

    ![Página de índice de Subscribers con filas][2]

## <a name="unsubscribe"></a>Creación y prueba de las vistas y el controlador Unsubscribe

A continuación, implementará la interfaz de usuario para el proceso de cancelación de suscripciones.

Este tutorial solo incluye instrucciones para crear el controlador para el proceso de anulación de suscripciones, no para el proceso de suscripción. Como se ha explicado en [el primer tutorial][primer tutorial de la serie], la interfaz de usuario y el método de servicio para el proceso de suscripción se han dejado fuera hasta que implementemos la seguridad apropiada para el método de servicio. Con fines de prueba, puede usar las páginas **Subscriber** para administradores a fin de suscribir las direcciones de correo electrónico a las listas.

### Incorporación del modelo de vista Unsubscribe a la carpeta Modelos

1.  En la carpeta `Models` del proyecto MVC, agregue el archivo `UnsubscribeVM.cs` del proyecto descargado.

El modelo de vista `UnsubscribeVM` se usa para transferir datos entre el controlador `Unsubscribe` y su vista.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

Los vínculos de cancelación de suscripción contienen el valor `SubscriberGUID`. Dicho valor se usa para obtener la dirección de correo electrónico, el nombre de lista y la descripción de la lista de la tabla `MailingList`. La vista muestra la dirección de correo electrónico y la descripción de la lista de la que se va a cancelar la suscripción y muestra el botón **Confirm** en el que el usuario debe hacer clic para completar el proceso de anulación de suscripción.

### Incorporación del controlador Unsubscribe

1.  En la carpeta `Controllers` del proyecto MVC, agregue el archivo *UnsubscribeController.cs* del proyecto descargado.

Este controlador tiene un método `HttpGet Index` que muestra la página de cancelación de suscripción inicial y un método `HttpPost Index` que procesa el botón **Confirm** o **Cancel**.

El método `HttpGet Index` usa el GUID y el nombre de lista en la cadena de consulta para obtener la fila de tabla `MailingList` para el suscriptor. A continuación, coloca toda la información que la vista necesita en el modelo de vista y muestra la página **Unsubscribe**. Define la propiedad `Confirmed` como null a fin de pedir a la vista que muestre la versión inicial de la página **Unsubscribe**.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

Nota: el valor SubscriberGUID no está en la clave de partición ni en la clave de fila, por lo que el rendimiento de esta consulta se reducirá a medida que aumente el tamaño de la partición (el número de direcciones de correo electrónico de una lista de distribución de correo). Para obtener información acerca de las alternativas para hacer que esta consulta sea más escalable, consulte el [primer tutorial de la serie][primer tutorial de la serie].

El método `HttpPost Index` vuelve a usar el GUID y el nombre de lista para obtener la información del suscriptor y rellenar las propiedades del modelo de vista. A continuación, si se ha hecho clic en el botón **Confirm**, elimina la fila de suscriptor de la tabla `MailingList`. Si se ha presionado el botón **Confirm**, también define la propiedad `Confirm` como `true`, de lo contrario, define la propiedad `Confirm` como `false`. El valor de la propiedad `Confirm` es el que pide a la vista que muestre la versión confirmada o cancelada de la página **Unsubscribe**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### Creación de las vistas MVC

1.  En la carpeta *Vistas* del proyecto MVC, cree una nueva carpeta llamada *Unsubscribe*.

2.  En la nueva carpeta *Vistas\\Unsubscribe*, agregue el archivo *Index.cshtml* del proyecto descargado.

En el archivo *Index.cshtml*, la línea `Layout = null` especifica que no se debe usar el archivo \_Layout.cshtml para mostrar esta página. La página **Unsubscribe** muestra una interfaz de usuario muy sencilla sin los encabezados y los pies de página usados para las páginas de administrador.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

En el cuerpo de la página, la propiedad `Confirmed` determina lo que se mostrará en la página: los botones **Confirm** y **Cancel** si la propiedad es null, el mensaje unsubscribe-confirmed si la propiedad es true y el mensaje unsubscribe-canceled si la propiedad es false.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto y, a continuación, haga clic en **Subscribers**.

2.  Haga clic en **Create** y cree un nuevo suscriptor para cualquier lista de distribución de correo creada para realizar las pruebas anteriores.

    Deje la ventana del explorador abierta en la página **Index** de **Subscribers**.

3.  Abra el **Explorador de servidores** y, a continuación, seleccione el nodo **Azure / Almacenamiento / (Development)**.

4.  Expanda **Tablas**, haga clic con el botón secundario en la tabla **MailingList** y, a continuación, haga clic en **Ver tabla**.

5.  Haga doble clic en la fila de suscriptor que ha agregado.

6.  En el cuadro de diálogo **Editar entidad**, seleccione y copie el valor `SubscriberGUID`.

    ![Explorador de almacenamiento de Azure][Explorador de almacenamiento de Azure]

7.  Vuelva a la ventana del explorador. En la barra de direcciones del explorador, cambie el texto "Subscriber" de la URL por "unsubscribe?ID=[guidvalue]&listName=[listname]", donde [guidvalue] es el GUID que ha copiado del Explorador de almacenamiento de Azure y [listname] es el nombre de la lista de distribución de correo. Por ejemplo:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Aparece la versión de la página **Unsubscribe** que pide la confirmación:

    ![Página Unsubscribe][Página Unsubscribe]

8.  Haga clic en **Confirm** y verá la confirmación de que se ha cancelado la suscripción de la dirección de correo electrónico.

    ![Página de confirmación de la cancelación de la suscripción][Página de confirmación de la cancelación de la suscripción]

9.  Vuelva a la página **Index** de **Subscribers** para comprobar que la fila de suscriptor ya no está ahí.

## <a name="nextsteps"></a>Pasos siguientes

Como se ha explicado en [el primer tutorial de la serie][primer tutorial de la serie], este tutorial no muestra los componentes del proceso de suscripción porque el método de servicio API web de ASP.NET no implementa la seguridad de secretos compartidos. No obstante, la restricción de IP que configura en el segundo tutorial también protege el método de servicio y puede agregar la funcionalidad de suscripción si copia los siguientes archivos del proyecto descargado.

Para el método de servicio de la API web de ASP.NET:

-   Controladores\\SubscribeAPI.cs

Para la página web que los suscriptores obtienen cuando hacen clic en el vínculo **Confirm** en el correo electrónico que genera el método de servicio:

-   Modelos\\SubscribeVM.cs
-   Controladores\\SubscribeController.cs
-   Vistas\\Subscribe\\Index.cshtml

En el [próximo tutorial][próximo tutorial], configurará y programará el rol de trabajo A, que se corresponde con el rol de trabajo que programa los correos electrónicos.

Podrá encontrar vínculos a recursos adicionales para trabajar con tablas, colas y blobs de Almacenamiento de Azure en el [último tutorial de la serie][último tutorial de la serie].

<div><div><a href="/es-es/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Tutorial 4</a></div>



  [primer tutorial de la serie]: /es-es/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Creación de la solución de Visual Studio]: #cloudproject
  [Actualización del paquete NuGet de la biblioteca de clientes de almacenamiento]: #updatescl
  [Configuración de los proyectos para usar el emulador de almacenamiento]: #configurestorage
  [Configuración de seguimiento y gestión de reinicios]: #tracing
  [Incorporación de código para crear tablas, colas y contenedores de blobs en el método Application\_Start]: #createifnotexists
  [Creación y prueba de la lista de distribución de correo]: #mailinglist
  [Creación y prueba de las vistas y el controlador Subscriber]: #subscriber
  [Creación y prueba de las vistas y el controlador Message]: #message
  [Creación y prueba de las vistas y el controlador Unsubscribe]: #unsubscribe
  [Pasos siguientes]: #nextsteps
  [Menú Nuevo proyecto]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [Cuadro de diálogo Nuevo proyecto]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [Cuadro de diálogo del nuevo proyecto en nube de Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [Cuadro de diálogo del nuevo proyecto en nube de Azure - cambiar el nombre del rol web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [Cuadro de diálogo del nuevo proyecto en nube de Azure - agregar un rol de trabajo]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [Sin autenticación]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [solución completa]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [página principal]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Emulador de proceso en la bandeja del sistema]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Opción para administrar paquetes NuGet para la solución en el menú]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Paquete Almacenamiento de Azure del cuadro de diálogo para administrar paquetes de NuGet]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Selección de ambos proyectos en el cuadro de diálogo Select Projects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Propiedades del rol web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Clic con el botón secundario en Propiedades]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Error de archivos de definición y configuración del servicio no válidos]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [el segundo tutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [reinicio de las instancias de rol debido a actualizaciones del sistema operativo]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [transferencia a petición]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/es-es/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Azure Storage Client Library 2.0 Tables Deep Dive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Introducción al modelo de datos del servicio Tabla]: http://msdn.microsoft.com/es-es/library/windowsazure/dd179338.aspx
  [% Character in PartitionKey or RowKey]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [cómo usar la compatibilidad con el código asincrónico de .NET 4.5 para evitar que se bloqueen las llamadas]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [How to get most out of Azure Tables]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Azure Tables: Expect Continuation Tokens, Seriously]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Registro de Almacenamiento de Azure: Uso de registros para realizar el seguimiento de las solicitudes de almacenamiento]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Página de índice de MailingList vacía]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [Página de índice de MailingList con filas]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Página de índice de Subscriber vacía]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Página de índice de Subscribers con filas]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Página de índice de Message vacía]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Explorador de almacenamiento de Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Página Unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Página de confirmación de la cancelación de la suscripción]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [próximo tutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [último tutorial de la serie]: /es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
