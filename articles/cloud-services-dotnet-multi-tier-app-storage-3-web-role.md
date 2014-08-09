<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Step 3: Web Role" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 3: Web role" metaKeywords="Azure tutorial, Email Service application, ASP.NET MVC web role, MVC controllers, Web API controller, Cloud Service project" description="The third tutorial in a series that teaches how to create and deploy the Email Service app in an Azure cloud service." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building the web role for the Azure Email Service application - 3 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Creación del rol web para la aplicación Servicio de correo electrónico de Azure - 3 de 5
========================================================================================

Este es el tercer de la serie de cinco que muestra cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/).

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Creación de una solución que contenga un proyecto de Servicio en la nube con un rol web y un rol de trabajo
-   Uso de tablas, blobs y colas de Azure en las vistas y los controladores MVC 4
-   Control de conflictos de simultaneidad al usar tablas de Azure
-   Configuración de un rol web o proyecto web para usar la cuenta de almacenamiento de Azure

Creación de solucionesCreación de la solución de Visual Studio
--------------------------------------------------------------

Empiece por crear una solución de Visual Studio con un proyecto para el front-end web y un proyecto para uno de los roles de trabajo back-end de Azure. Agregará el segundo rol de trabajo más adelante.

(Si desea ejecutar la interfaz de usuario web en un sitio web de Azure en lugar de en un Servicio en la nube de Azure, consulte la sección [Arquitectura alternativa](#alternativearchitecture) más adelante en este tutorial para ver los cambios de estas instrucciones).

### Creación de un proyecto del servicio en la nube con un rol web y un rol de trabajo

1.  Inicie Visual Studio con privilegios elevados.

    > [WACOM.NOTE] Si se trata de Visual Studio 2013, no tiene que usar privilegios elevados, porque los nuevos proyectos usan el emulador de proceso exprés de forma predeterminada.

2.  En el menú **Archivo**, seleccione **Nuevo proyecto**.

    ![Menú Nuevo proyecto](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png)

3.  Expanda **C\#** y seleccione **Nube** en **Plantillas instaladas** y, a continuación, seleccione **Servicio de nube de Azure**.

4.  Asigne a la aplicación el nombre **AzureEmailService** y haga clic en **OK**.

    ![Cuadro de diálogo Nuevo proyecto](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png)

5.  En el cuadro de diálogo **New Azure Cloud Service**, seleccione **Rol web de ASP.NET** y haga clic en la flecha hacia la derecha.

    > [WACOM.NOTE] El código descargado usado para este tutorial es MVC 4, pero no puede crear un rol web MVC 4 en Visual Studio 2013 con las instrucciones escritas para Visual Studio 2012. Si se trata de Visual Studio 2013, proceda como sigue: 1) Omita los pasos que aquí se detallan para crear el rol web y, en su lugar, siga los pasos para el rol de trabajo. 2) Después de haber creado el rol de trabajo, haga clic con el botón secundario en el **Explorador de soluciones** y, a continuación, haga clic en **Agregar** -- **Nuevo proyecto**. En el panel izquierdo del cuadro de diálogo **Agregar nuevo proyecto**, expanda **Web** y seleccione **Visual Studio 2012**. 3) Seleccione **Aplicación web de ASP.NET MVC 4**, asigne al proyecto el nombre **MvcWebRole** y, a continuación, haga clic en **OK**. 4) En el cuadro de diálogo **New ASP.NET Project**, seleccione la plantilla **Internet Application**. 5) Haga clic con el botón secundario en **Roles**, en la sección **AzureEmailService** del **Explorador de soluciones** y, a continuación, haga clic en **Agregar** - **Proyecto de rol web en la solución**. 6) En el cuadro **Asociar a proyecto de rol**, seleccione el proyecto **MvcWebRole** y, a continuación, haga clic en **OK**.

    ![Cuadro de diálogo del nuevo proyecto en nube de Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png)

6.  En la columna de la derecha, mantenga el puntero del mouse sobre **MvcWebRole1** y, a continuación, haga clic en el icono de lápiz para cambiar el nombre del rol web.

7.  Escriba MvcWebRole para el nuevo nombre y, a continuación, presione Entrar.

    ![Cuadro de diálogo del nuevo proyecto en nube de Azure - cambiar el nombre del rol web](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png)

8.  Siga el mismo procedimiento para agregar un **Rol de trabajo**, asígnele el nombre WorkerRoleA y, a continuación, haga clic en **OK**.

    ![Cuadro de diálogo del nuevo proyecto en nube de Azure - agregar un rol de trabajo](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png)

9.  En el cuadro de diálogo **New ASP.NET Project**, seleccione la plantilla **Internet Application**.

10. En la lista desplegable **Motor de vistas**, asegúrese de que la opción **Razor** está seleccionada y, a continuación, haga clic en **OK**.

    ![Cuadro de diálogo Nuevo proyecto](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png)

### Establecimiento del encabezado, el menú y el pie de página

En esta sección puede actualizar los encabezados, pies de página y elementos de menú que se muestran en cada página de la interfaz de usuario web para administradores. La aplicación contendrá tres conjuntos de páginas web para administradores: una para listas de distribución de correo, otra para los suscriptores a las listas de distribución de correo y otra para los mensajes.

1.  En el **Explorador de soluciones**, expanda la carpeta Views\\Shared y abra el archivo \_Layout.cshtml.

    ![\_Layout.cshtml en el Explorador de soluciones](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png)

2.  En el elemento **&lt;title\>**, cambie "My ASP.NET MVC Application" por "Azure Email Service".

3.  En el elemento **&lt;p\>** con la clase "site-title", cambie "your logo here" por "Azure Email Service" y, a continuación, cambie "Home" por "MailingList".

    ![título y encabezado de \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png)

4.  Elimine la sección de menú:

    ![menú de \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png)

5.  Inserte una nueva sección de menú donde se encontraba la anterior:

         <ul id="menu">
             <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
             <li>@Html.ActionLink("Messages", "Index", "Message")</li>
             <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
         </ul>

6.  En el elemento **&lt;footer\>**, cambie "My ASP.NET MVC Application" por "Azure Email Service".

    ![pie de página de \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png)

### Ejecución de la aplicación de forma local

1.  Presione CTRL+F5 para ejecutar la aplicación.

    Aparece la página principal de la aplicación en el explorador predeterminado.

    ![página principal](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png)

    La aplicación se ejecuta en el emulador de proceso de Azure. Puede ver el icono del emulador de proceso en la bandeja del sistema de Windows:

    ![Emulador de proceso en la bandeja del sistema](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png)

Configuración del seguimientoConfiguración del seguimiento
----------------------------------------------------------

Para habilitar el almacenamiento de los datos de seguimiento, abra el archivo *WebRole.cs* y agregue el siguiente método `ConfigureDiagnostics`. Agregue el código que llama al nuevo método en el método `OnStart`.

> [WACOM.NOTE] Para Visual Studio 2013, en lugar de aplicar los siguientes pasos que cambian manualmente el código en *WebRole.cs*, haga clic con el botón secundario en el proyecto MvcWebRole, haga clic en **Agregar elemento existente** y agregue el archivo *WebRole.cs* del proyecto descargado.

    private void ConfigureDiagnostics()
    {
        DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
    }

    public override bool OnStart()
    {
        ConfigureDiagnostics();
        return base.OnStart();
    }

El método `ConfigureDiagnostics` se explica en el [segundo tutorial](/es-es/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

ReiniciosIncorporación de código para gestionar los reinicios con eficacia
--------------------------------------------------------------------------

Las aplicaciones del Servicio de nube de Azure se reinician aproximadamente dos veces al mes para las actualizaciones del sistema operativo. (Para obtener más información acerca de las actualizaciones del sistema operativo, consulte el blog sobre el [reinicio de las instancias de rol debido a actualizaciones del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) [en inglés]). Cuando una aplicación web se va a apagar, se genera un evento `OnStop`. El texto reutilizable del rol web creado por Visual Studio no sobrescribe el método `OnStop`, por lo que la aplicación tendrá solo unos segundos para terminar de procesar las solicitudes HTTP antes de apagarse. Puede agregar código para sobrescribir el método `OnStop` a fin de garantizar el control correcto de los apagados.

Para controlar los apagados y reinicios, abra el archivo *WebRole.cs* y agregue la siguiente anulación del método `OnStop`.

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

Este código requiere una instrucción `using` adicional:

      using System.Diagnostics;

El método `OnStop` tiene hasta 5 minutos para salir antes de que la aplicación se apague. Podría agregar una llamada de suspensión de 5 minutos al método `OnStop` a fin de dar a la aplicación la máxima cantidad de tiempo para procesar las solicitudes actuales, pero si la aplicación se escala correctamente, debe tener la capacidad de procesar las solicitudes restantes en mucho menos de 5 minutos. Lo mejor es detener el proceso lo antes posible, a fin de que la aplicación pueda reiniciarse cuanto antes para continuar con el procesamiento de las solicitudes.

Cuando Azure pone el rol sin conexión, el equilibrador de carga deja de enviar solicitudes a la instancia de rol y, después de eso, se realiza la llamada al método `OnStop`. Si no tiene otra instancia del rol, no se procesará ninguna solicitud hasta que el rol se apague y se reinicie (un proceso que suele durar varios minutos). Ese es un motivo por el que el contrato de nivel de servicio de Azure requiere disponer al menos de dos instancias de cada rol a fin de poder beneficiarse de la garantía de tiempo de actividad.

En el código que aparece para el método `OnStop`, se crea un contador de rendimiento de ASP.NET para `Requests Current`. El valor de contador `Requests Current` contiene el número actual de solicitudes, incluidas las que están en cola, las que se encuentran en ejecución actualmente o las que están pendientes de escribirse en el cliente. El valor `Requests Current` se comprueba cada segundo y, cuando llega a cero, se devuelve el método `OnStop`. Cuando se devuelve el método `OnStop`, el rol se apaga.

Los datos de seguimiento no se guardan cuando la llamada se realiza desde el método `OnStop` sin [realizar una transferencia a petición](http://msdn.microsoft.com/es-es/library/windowsazure/gg433075.aspx). Puede ver la información de seguimiento de `OnStop` en tiempo real con la utilidad [dbgview](http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx) desde una conexión a Escritorio remoto.

Actualización de la biblioteca del cliente de almacenamientoActualización del paquete NuGet de la biblioteca del cliente de almacenamiento
------------------------------------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Es posible que este paso no sea necesario. Si el paquete NuGet de almacenamiento de Azure no aparece en la lista Actualizaciones, significa que la versión instalada está actualizada.

El marco API que usa para trabajar con tablas, colas y blobs de almacenamiento de Azure es la biblioteca del cliente de almacenamiento (SCL). Esta API se incluye en un paquete NuGet en la plantilla del proyecto del Servicio en la nube. No obstante, en la fecha en que se escribe este tutorial, las plantillas de proyecto incluyen la versión 1.7 de SCL, no la versión actual 2.0. Por tanto, antes de empezar a escribir código, deberá actualizar el paquete NuGet.

1.  En el menú **Tools** de Visual Studio, mantenga el mouse sobre **Library Package Manager** y, a continuación, haga clic en **Manage NuGet Packages for Solution**.

    ![Opción para administrar paquetes NuGet para la solución en el menú](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png)

2.  En el panel izquierdo del cuadro de diálogo **Manage NuGet Packages**, seleccione **Actualizaciones**, desplácese hacia abajo hasta el paquete **Almacenamiento de Azure** y haga clic en **Actualizar**.

    ![Paquete Almacenamiento de Azure del cuadro de diálogo para administrar paquetes de NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png)

3.  En el cuadro de diálogo **Select Projects**, asegúrese de que ambos proyectos están seleccionados y, a continuación, haga clic en **OK**.

    ![Selección de ambos proyectos en el cuadro de diálogo Select Projects](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png)

4.  Acepte los términos de licencia para completar la instalación del paquete y, a continuación, cierre el cuadro de diálogo **Manage NuGet Packages**.

5.  En el proyecto WorkerRoleA de *WorkerRole.cs*, si encuentra la siguiente instrucción `using`, elimínela porque ya no es necesaria:

         using Microsoft.WindowsAzure.StorageClient;

La versión 1.7 de SCL comprende un proveedor LINQ que simplifica la codificación de las consultas de tabla. En la fecha en que se escribe este tutorial, Table Service Layer (TSL) 2.0 aún no tiene un proveedor LINQ. Si desea usar LINQ, aún tiene acceso al proveedor LINQ de SCL 1.7 en el espacio de nombres [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.storage.table.dataservices.aspx). TSL 2.0 se ha diseñado para mejorar el rendimiento, y el proveedor LINQ 1.7 no se beneficia de todas estas mejoras. La aplicación de ejemplo usa TLS 2.0, por lo que no usa LINQ para las consultas. Para obtener más información acerca de SCL y TSL 2.0, consulte los recursos que se incluyen al final del [último tutorial de esta serie](/es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

> [WACOM.NOTE] La biblioteca del cliente de almacenamiento 2.1 reincorporó la compatibilidad de LINQ, pero este tutorial no usa LINQ para almacenar las consultas de tabla. La SCL actual también admite la programación asincrónica, pero en este tutorial no se aborda el código asincrónico. Para obtener más información acerca de la programación asincrónica y un ejemplo de código que la use con la SCL de Azure, consulte el siguiente capítulo de libro electrónico y el proyecto descargable que lo acompaña: [Use .NET 4.5™'s async support to avoid blocking calls](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

Incorporación de referencias a SCL 1.7Incorporación de una referencia a un ensamblado de SCL 1.7
------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Si usa SCL 2.1 o posterior (Visual Studio 2013 con el último SDK), omita esta sección.

La versión 2.0 de la biblioteca del cliente de almacenamiento (SCL) 2.0 no contiene todo lo necesario para los diagnósticos, por lo que es necesario agregar una referencia a un ensamblado de la versión 1.7.

1.  Haga clic con el botón secundario en el proyecto MvcWebRole y seleccione **Agregar referencia**.

2.  Haga clic en el botón **Examinar...** en la parte inferior del cuadro de diálogo.

3.  Desplácese hasta la siguiente carpeta:

         C:\Archivos de programa\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Seleccione *Microsoft.WindowsAzure.StorageClient.dll* y, a continuación, haga clic en **Agregar**.

5.  En el cuadro de diálogo **Administrador de referencias**, haga clic en **OK**.

6.  Repita el proceso para el proyecto WorkerRoleA.

Código App\_StartIncorporación de código para crear tablas, colas y contenedores de blobs en el método Application\_Start
-------------------------------------------------------------------------------------------------------------------------

La aplicación web usará las tablas `MailingList` y `Message`, la cola `azuremailsubscribequeue` y el contenedor de blobs `azuremailblobcontainer`. Podría crearlos manualmente mediante una herramienta como el Explorador de almacenamiento de Azure, pero en su caso tendría que hacerlo manualmente cada vez que empezara a usar la aplicación con una nueva cuenta de almacenamiento. En esta sección, agregará código que se ejecuta cuando la aplicación se inicia, comprueba si existen las tablas, las colas y los contenedores de blobs necesarios y los crea en caso de que no existan.

Podría agregar este código de inicio de un solo uso al método `OnStart` en los archivos *WebRole.cs* o *Global.asax*. Para este tutorial, inicializará el Almacenamiento de Azure en el archivo *Global.asax*, ya que funciona con Sitios web Azure y con los roles web del Servicio de nube de Azure.

1.  En el **Explorador de soluciones**, expanda *Global.asax* y, a continuación, abra *Global.asax.cs*.

2.  Agregue un nuevo método `CreateTablesQueuesBlobContainers` después del método `Application_Start` y, a continuación, llame al nuevo método desde el método `Application_Start`, como se muestra en el siguiente ejemplo:

         protected void Application_Start()
         {
             AreaRegistration.RegisterAllAreas();
             WebApiConfig.Register(GlobalConfiguration.Configuration);
             FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
             RouteConfig.RegisterRoutes(RouteTable.Routes);
             BundleConfig.RegisterBundles(BundleTable.Bundles);
             AuthConfig.RegisterAuth();
             // Compruebe que existen todas las tablas, las colas y los contenedores de blobs usados en esta aplicación
             // y cree los que todavía no existan.
             CreateTablesQueuesBlobContainers();
         }

         private static void CreateTablesQueuesBlobContainers()
         {
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
             // Si se ejecuta en un sitio web de Azure (no en un servicio en la nube), use el archivo Web.config:
             //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
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

3.  Haga clic con el botón secundario en la línea serpenteante azul en `RoleEnvironment`, seleccione **Resolver** y, a continuación, seleccione **using Microsoft.WindowsAzure.ServiceRuntime**.

    ![ClicSecundario](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png)

4.  Haga clic con el botón secundario en la línea serpenteante azul en `CloudStorageAccount`, seleccione **Resolver** y, a continuación, seleccione **using Microsoft.WindowsAzure.Storage**.

5.  También puede agregar manualmente las siguientes instrucciones using:

         using Microsoft.WindowsAzure.ServiceRuntime;
         using Microsoft.WindowsAzure.Storage;

6.  Desarrolle la aplicación, lo que guarda el archivo y comprueba que no haya errores de compilación.

En las siguientes secciones, va a compilar los componentes de la aplicación web; puede probarlos con el almacenamiento de desarrollo o la cuenta de almacenamiento sin tener que crear primero manualmente tablas, colas o contenedores de blobs.

Mailing ListCreación y prueba de vistas y controladores de Mailing List
-----------------------------------------------------------------------

Los administradores usan la interfaz de usuario web **Mailing List** para crear, editar y mostrar listas de distribución de correo, como "Contoso University History Department announcements" y "Fabrikam Engineering job postings".

### Incorporación de la clase de entidad MailingList a la carpeta Modelos

La clase de entidad `MailingList` se usa para las filas de la tabla `MailingList` que contienen información acerca de la lista, como su descripción y la dirección "De" para los correos electrónicos enviados a la lista.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta `Modelos` del proyecto MVC y seleccione **Agregar elemento existente**.

    ![Agregar elemento existente a la carpeta Modelos](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png)

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *MailingList.cs* de la carpeta `Modelos` y haga clic en **Agregar**.

3.  Abra *MailingList.cs* y examine el código.

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

    La API de TSL 2.0 del Almacenamiento de Azure requiere que las clases de entidad que usa para las operaciones de tabla deriven de la clase [TableEntity](http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx). Esta clase define los campos `PartitionKey`, `RowKey`, `TimeStamp` y `ETag`. El sistema usa las propiedades `TimeStamp` y `ETag`. Verá cómo se usa la propiedad `ETag` para el control de simultaneidad más adelante en el tutorial.

    (También existe la clase [DynamicTableEntity](http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) para usarla si desea trabajar con filas de tabla como colecciones de diccionario de pares clave-valor en lugar de usar clases de modelo predefinidas. Para obtener más información, consulte [Azure Storage Client Library 2.0 Tables Deep Dive](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx)).

    La clave de partición de tabla `mailinglist` es el nombre de lista. En esta clase de entidad, se puede obtener acceso al valor de la clave de partición con la propiedad `PartitionKey` (definida en la clase `TableEntity`) o con la propiedad `ListName` (definida en la clase `MailingList`). La propiedad `ListName` usa `PartitionKey` como su variable de respaldo. La definición de la propiedad `ListName` le permite usar un nombre de variable más descriptivo en el código y facilita la programación de la interfaz de usuario web, ya que los atributos DataAnnotations de validación y formato se pueden agregar a la propiedad `ListName`, pero no se pueden agregar directamente a la propiedad `PartitionKey`.

    El atributo `RegularExpression` de la propiedad `ListName` da lugar a que MVC valide la entrada de usuario a fin de garantizar que el valor de nombre de lista escrito solo contiene caracteres alfanuméricos o subrayado. Esta restricción se implementó a fin de mantener nombres de lista sencillos que se puedan usar con facilidad en las cadenas de consulta en las URL.

    **Nota:** si desea que el formato del nombre de lista sea menos restrictivo, podría admitir otros caracteres y nombres de lista codificados como URL cuando se usen en las cadenas de consulta. No obstante, no se permiten determinados caracteres en las claves de fila o de partición de tablas de Azure, por lo que tendría que excluir al menos dichos caracteres. Para obtener más información acerca de los caracteres no admitidos o que causan problemas en los campos de clave de fila o de partición, consulte [Introducción al modelo de datos del servicio Tabla](http://msdn.microsoft.com/es-es/library/windowsazure/dd179338.aspx) y el blog [% Character in PartitionKey or RowKey](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx).

    La clase `MailingList` define un constructor predeterminado que establece `RowKey` como la cadena codificada de forma rápida "mailinglist", porque todas las filas de la lista de distribución de correo de esta tabla tienen dicho valor como la clave de fila. (Para obtener una explicación de la estructura de la tabla, consulte el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/)). Se podría haber elegido cualquier valor constante para este propósito, siempre que no hubiera coincidido con una dirección de correo electrónico, que es la clave de fila para las filas de suscriptor de esta tabla.

    El nombre de lista y la dirección de correo electrónico "De" siempre deben escribirse cuando se crea una nueva entidad `MailingList`, por lo que tienen atributos `Required`.

    Los atributos `Display` especifican la leyenda predeterminada que se usará para un campo en la interfaz de usuario de MVC.

### Incorporación del controlador MVC de MailingList

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Controladores del proyecto MVC y seleccione **Agregar elemento existente**.

    ![Agregar elemento existente a la carpeta Controladores](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png)

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *MailingListController.cs* de la carpeta `Controladores` y haga clic en **Agregar**.

3.  Abra *MailingListController.cs* y examine el código.

    El constructor predeterminado crea un objeto `CloudTable` para usarlo al trabajar con la tabla `mailinglist`.

         public class MailingListController : Controller
         {
             private CloudTable mailingListTable;

             public MailingListController()
             {
                 var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // Si se ejecuta en un sitio web de Azure (no en un servicio en la nube), use el archivo Web.config:
                 //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 var tableClient = storageAccount.CreateCloudTableClient();
                 mailingListTable = tableClient.GetTableReference("mailinglist");
             }

    El código obtiene las credenciales para la cuenta de almacenamiento de Azure del archivo de configuración del proyecto del Servicio en la nube a fin de realizar una conexión a la cuenta de almacenamiento. (Realizará esta configuración más adelante en este tutorial, antes de probar el controlador). Si va a ejecutar el proyecto MVC en un sitio web de Azure, en su lugar, puede obtener la cadena de conexión desde el archivo Web.config.

    A continuación, hay un método `FindRow` al que se llama siempre que el controlador necesita buscar una entrada de lista de distribución de correo específica de la tabla `MailingList`, por ejemplo para editar una entrada de la lista de distribución de correo. El código recupera una entidad `MailingList` exclusiva con la utilización de valores de clave de fila y de clave de partición que se le transfieren. Las filas que este controlador edita son las que tienen "MailingList" como la clave de fila, por lo que "MailingList" podría haberse codificado de forma rápida para la clave de fila, pero la definición de la clave de partición y de la clave de fila es un patrón usado para los métodos `FindRow` en todos los controladores.

         private MailingList FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 throw new Exception("No mailing list found for: " + partitionKey);
             }

             return mailingList;
         }

    Es instructivo comparar el método `FindRow` en el controlador `MailingList`, que devuelve una fila de lista de distribución de correo, con el método `FindRow` en el controlador `Subscriber`, que devuelve una fila de suscriptor de la misma tabla `mailinglist`.

         private Subscriber FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var subscriber = retrievedResult.Result as Subscriber;
             if (subscriber == null)
             {
                 throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
             }
             return subscriber;
         }

    La única diferencia entre las dos consultas es el tipo de modelo que transfieren al método [TableOperation.Retrieve](http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx). El tipo de modelo especifica el esquema (las propiedades) de la fila o las filas que espera que la consulta devuelva. Una única tabla puede tener diferentes esquemas en distintas filas. Normalmente, se especifica el mismo tipo de modelo al leer una fila usada para crear la fila.

    En la página **Index** se muestran todas las filas de lista de distribución de correo, de forma que la consulta del método `Index` devuelve todas las entidades `MailingList` que tienen "mailinglist" como la clave de fila (las otras filas de la tabla tienen la dirección de correo electrónico como la clave de fila y contienen información acerca del suscriptor).

                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

    El método `Index` envuelve esta consulta con código diseñado para controlar las condiciones del tiempo de espera.

         public ActionResult Index()
         {
             TableRequestOptions reqOptions = new TableRequestOptions()
             {
                 MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                 RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
             };
             List<MailingList> lists;
             try
             {
                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
             }
             catch (StorageException se)
             {
                 ViewBag.errorMessage = "Timeout error, try again. ";
                 Trace.TraceError(se.Message);
                 return View("Error");
             }

             return View(lists);
         }

    Si no especifica los parámetros del tiempo de espera, la API automáticamente hace tres reintentos de aumentar de manera exponencial los límites de tiempo de espera. Si se trata de una interfaz web en la que un usuario espera que aparezca una página, esto podría suponer largos tiempos de espera inaceptables. Por tanto, este código especifica reintentos lineales (para que el límite de tiempo de espera no aumente en cada ocasión) y un límite de tiempo de espera que resulta razonable para el usuario.

    Cuando el usuario hace clic en el botón **Create** de la página **Create**, el enlazador de modelos de MVC crea una entidad `MailingList` a partir de la entrada escrita en la vista y el método `HttpPost Create` agrega la entidad a la tabla.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(MailingList mailingList)
         {
             if (ModelState.IsValid)
             {
                 var insertOperation = TableOperation.Insert(mailingList);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             return View(mailingList);
         }

  En la página **Edit**, el método `HttpGet Edit` busca la fila y el método `HttpPost` la actualiza.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Error de simultaneidad
                        var currentMailingList = FindRow(partitionKey, rowKey);
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
            }
            return View(editedMailingList);
        }

  El bloque try-catch controla los errores de simultaneidad. Se genera una excepción de simultaneidad si un usuario selecciona una lista de distribución de correo para editarla; a continuación, mientras la página **Edit** se muestra en el explorador, otro usuario edita la misma lista de distribución de correo. Cuando eso ocurre, el código muestra un mensaje de advertencia e indica qué campos ha modificado el otro usuario.  La API de TSL usa "ETag" para comprobar los conflictos de simultaneidad. Cada vez que se actualiza una fila de la tabla, el valor "ETag" cambia.  Si obtiene una fila para editarla, se guarda el valor "ETag" y, al ejecutar una operación de actualización o eliminación, se reemplaza el valor "ETag" guardado. (La vista "Edit" tiene un campo oculto para el valor ETag). Si la operación de actualización identifica que el valor "ETag" del registro que se va a actualizar difiere del valor "ETag" transferido a la operación de actualización, genera una excepción de simultaneidad. Si no le preocupan los conflictos de simultaneidad, puede definir el campo ETag con un asterisco ("*") en la entidad transferida a la operación de actualización, de forma que se ignorarán los conflictos. 

  Nota: el error HTTP 412 no es exclusivo para los errores de simultaneidad. La API de SCL también puede generarlo para otros errores.

  Para la página **Delete**, el método "HttpGet Delete" busca la fila para mostrar su contenido y el método "HttpPost" elimina el fila "MailingList" junto con las filas "Subscriber" asociadas con ella en la tabla "MailingList".

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Elimine todas las filas de esta lista de distribución de correo, es decir, 
            // las filas Subscriber y las filas MailingList.
            // Por tanto, no necesita especificar la clave de fila.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

  En caso de que sea preciso eliminar una gran cantidad de suscriptores, el código elimina los registros por lotes. El coste de la transacción que conlleva eliminar una fila es el mismo en que se incurre con la eliminación de 100 filas en un lote. El número máximo de operaciones que puede ejecutar en un lote es 100. 

 Aunque el bucle procesa las filas "MailingList" y "Subscriber", las lee todas en la clase de entidad "MailingList", porque los únicos campos necesarios para la operación "Delete" son "PartitionKey", "RowKey" e "ETag".

### Incorporación de las vistas MVC de MailingList

1.  En el **Explorador de soluciones**, cree una carpeta nueva en la carpeta *Vistas* del proyecto MVC y asígnele el nombre *MailingList*.

2.  Haga clic con el botón secundario en la carpeta *Vistas\\MailingList* nueva y seleccione **Agregar elemento existente**.

    ![Agregar elemento existente a la carpeta Vistas](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png)

3.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione los cuatro archivos .cshtml de la carpeta *Vistas\\MailingList* y haga clic en **Agregar**.

4.  Abra el archivo *Edit.cshtml* y examine el código.

         @model MvcWebRole.Models.MailingList
                 @{
             ViewBag.Title = "Edit Mailing List";
         }
                 <h2>Edit Mailing List</h2>
               @using (Html.BeginForm()) {
               @Html.AntiForgeryToken()
               @Html.ValidationSummary(true)
               @Html.HiddenFor(model => model.ETag)
               <fieldset>
                 <legend>MailingList</legend>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DisplayFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Description)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Description)
                     @Html.ValidationMessageFor(model => model.Description)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.FromEmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.FromEmailAddress)
                     @Html.ValidationMessageFor(model => model.FromEmailAddress)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>       
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Este código es típico de las vistas MVC. Tenga en cuenta el campo oculto incluido para preservar el valor `ETag` que se usa para controlar los conflictos de simultaneidad. Tenga en cuenta también que el campo `ListName` tiene una aplicación auxiliar `DisplayFor` en lugar de una aplicación auxiliar `EditorFor`. No habilitamos la página **Edit** para cambiar el nombre de lista, porque eso podría haber requerido código complejo en el controlador: el método `HttpPost Edit` tendría que haber eliminado la fila de la lista de distribución de correo existente y todas las filas de suscriptor asociadas y reinsertarlas todas con el nuevo valor de clave. En una aplicación de producción, puede decidir que la complejidad adicional merezca la pena. Como observará más adelante, el controlador `Subscriber` permite cambios del nombre de lista, porque solo se ve afectada una fila al mismo tiempo.

    El código *Create.cshtml* y *Delete.cshtml* es similar a *Edit.cshtml*.

5.  Abra *Index.cshtml* y examine el código.

         @model IEnumerable<MvcWebRole.Models.MailingList>
         @{
             ViewBag.Title = "Mailing Lists";
         }
         <h2>Mailing Lists</h2>
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Description)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.FromEmailAddress)
                 </th>
                 <th></th>
             </tr>
         @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.ListName)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Description)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.FromEmailAddress)
                 </td>
                 <td>
                     @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
                     @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
                 </td>
             </tr>
         }
         </table>

    Este código también es típico de las vistas MVC. Los hipervínculos **Edit** y **Delete** especifican los parámetros de la cadena de consulta de clave de fila y de clave de partición a fin de identificar una fila específica. Para las entidades `MailingList`, solo se necesita realmente la clave de partición, porque la clave de fila siempre es "MailingList", pero ambas se mantienen a fin de que el código de vista MVC sea coherente entre todos los controladores y las vistas.

### Conversión de MailingList en el controlador predeterminado

1.  Abra *Route.config.cs* en la carpeta *App\_Start*.

2.  En la línea que especifica valores predeterminados, cambie el controlador predeterminado de "Home" a "MailingList".

          routes.MapRoute(
              name: "Default",
              url: "{controller}/{action}/{id}",
              defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

Configuración del almacenamientoConfiguración del rol web para usar la cuenta de almacenamiento de Azure de prueba
------------------------------------------------------------------------------------------------------------------

Va a incorporar la configuración para la cuenta de almacenamiento de prueba, que usará al ejecutar el proyecto a nivel local. Para agregar una nueva configuración, tiene que agregarla tanto para la nube como a nivel local, pero puede cambiar el valor para la nube más adelante. Se agregará la misma configuración para el rol de trabajo A más adelante.

(Si desea ejecutar la interfaz de usuario web en un sitio web de Azure en lugar de en un Servicio en la nube de Azure, consulte la sección [Arquitectura alternativa](#alternativearchitecture) más adelante en este tutorial para ver los cambios de estas instrucciones).

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **MvcWebRole** en la opción **Roles** del proyecto de nube **AzureEmailService** y, a continuación, seleccione **Propiedades**.

    ![Propiedades del rol web](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png)

2.  Asegúrese de que la opción **All Configurations** está seleccionada en la lista desplegable **Configuración de servicio**.

3.  Seleccione la pestaña **Settings** y, a continuación, haga clic en **Agregar configuración**.

4.  Escriba "StorageConnectionString" en la columna **Name**.

5.  Seleccione **Cadena de conexión** en la lista desplegable **Tipo**.

6.  Haga clic en el botón de los puntos suspensivos (**...**) en el extremo derecho de la línea para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png)

7.  En el cuadro de diálogo **Create Storage Connection String**, haga clic en el botón de radio **Your subscription** y, a continuación, haga clic en el vínculo **Download Publish Settings**.

    > [WACOM.NOTE] Con el último SDK, no necesita descargar nada; debe seleccionar en una lista desplegable entre las cuentas de almacenamiento disponibles.

    **Nota:** si ha configurado los ajustes de almacenamiento para el tutorial 2 y va a ejecutar este tutorial en la misma máquina, no tiene que descargar la configuración de nuevo; solo tiene que hacer clic en **Your subscription** y, a continuación, seleccionar los valores correctos para **Suscripción** y **Nombre de cuenta**.

    ![Clic con el botón secundario en Propiedades](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png)

    Al hacer clic en el vínculo **Download Publish Settings**, Visual Studio inicia una nueva instancia del explorador predeterminado con la dirección URL para la página de configuración de publicación de descargas del Portal de administración de Azure. Si no ha iniciado sesión en el portal, se le solicitará que lo haga. Una vez que haya iniciado sesión, el explorador le solicitará que guarde la configuración de publicación. Anote el lugar en el que guarda la configuración.

    ![configuración de publicación](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png)

8.  En el cuadro de diálogo **Create Storage Connection String**, haga clic en **Import** y, a continuación, diríjase al archivo de configuración de publicación que guardó en el paso anterior.

9.  Seleccione la cuenta de almacenamiento y suscripción que desee usar y, a continuación, haga clic en **OK**.

    ![selección de la cuenta de almacenamiento](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png)

10. Siga el mismo procedimiento que usó para la cadena de conexión `StorageConnectionString` para configurar la cadena de conexión `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    No tiene que descargar de nuevo el archivo de configuración de la publicación. Cuando haga clic en los puntos suspensivos de la cadena de conexión `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, verá que el cuadro de diálogo **Create Storage Connection String** recuerda la información de suscripción. Si hace clic en el botón de radio **Your subscription**, todo lo que tiene que hacer es seleccionar los mismos valores de **Suscripción** y **Nombre de cuenta** que ha seleccionado anteriormente y, a continuación, hacer clic en **OK**.

11. Siga el mismo procedimiento usado para las dos cadenas de conexión para el rol MvcWebRole, con objeto de definir las cadenas de conexión para el rol WorkerRoleA.

Al agregar una nueva configuración con el botón **Add Settings**, la nueva configuración se agregó al XML en el archivo *ServiceDefinition.csdf* y en cada uno de los dos archivos de configuración *.cscfg*. Visual Studio agrega el siguiente XML al archivo *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

El siguiente XML se agrega a cada archivo de configuración *.cscfg*.

       <Setting name="StorageConnectionString"
       value="DefaultEndpointsProtocol=https;
       AccountName=azuremailstorage;
       AccountKey=[your account key]" />

Puede agregar manualmente la configuración al archivo *ServiceDefinition.csdf* y a los dos archivos de configuración *.cscfg*, pero usar el editor de propiedades tiene las siguientes ventajas para las cadenas de conexión:

-   Solo se agrega la nueva configuración en un lugar y el XML de la configuración correcto se agrega a los tres archivos.
-   El XML correcto se genera para los tres archivos de configuración. El archivo *ServiceDefinition.csdf* define la configuración que debe contener cada archivo de configuración *.cscfg*. Si la configuración del archivo *ServiceDefinition.csdf* y de los archivos de configuración *.cscfg* no es coherente, puede obtener el siguiente mensaje de error de Visual Studio: "The current service model is out of sync. Make sure both the service configuration and definition files are valid."

    ![Error de archivos de definición y configuración del servicio no válidos](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png)

Si obtiene este error, el editor de propiedades no funcionará hasta que resuelva el problema de incoherencia.

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto.

    ![Página de índice de MailingList vacía](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png)

2.  Use la función **Create** para agregar algunas listas de distribución de correo y pruebe las funciones **Edit** y **Delete** para asegurarse de que funcionan.

    ![Página de índice de MailingList con filas](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png)

SubscriberCreación y prueba de las vistas y el controlador Subscriber
---------------------------------------------------------------------

Los administradores usan la interfaz de usuario web **Subscriber** para agregar nuevos suscriptores a una lista de distribución de correo y para editar, mostrar y eliminar los suscriptores existentes.

### Incorporación de la clase de entidad Subscriber a la carpeta Modelos

La clase de entidad `Subscriber` se usa para las filas de la tabla `MailingList` que contienen información acerca de los suscriptores de una lista. Estas filas contienen información como la dirección de correo electrónico de la persona y si se ha comprobado la dirección.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Modelos* del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *Subscriber.cs* de la carpeta *Modelos* y haga clic en **Agregar**.

3.  Abra *Subscriber.cs* y examine el código.

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

    Al igual que la clase de entidad `MailingList`, la clase de entidad `Subscriber` se usa para leer y escribir filas en la tabla `mailinglist`. Las filas `Subscriber` usan las dirección de correo electrónico en lugar del valor "mailinglist" constante para la clave de fila. (Para obtener una explicación de la estructura de la tabla, consulte el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/)). Por tanto, se define una propiedad `EmailAddress` que usa la propiedad `RowKey` como su campo de respaldo de la misma forma que `ListName` usa `PartitionKey` como su campo de respaldo. Como se ha explicado anteriormente, esto le permite colocar los atributos DataAnnotations de formato y validación en las propiedades.

    El valor `SubscriberGUID` se genera cuando se crea una entidad `Subscriber`. Se usa en los vínculos de suscripción y cancelación de suscripción a fin de garantizar que solo las personas autorizadas puedan suscribir direcciones de correo electrónico o cancelar su suscripción. Cuando una fila se crea inicialmente para un nuevo suscriptor, el valor `Verified` es `false`. El valor `Verified` cambia a `true` solo después de que el nuevo suscriptor haga clic en el hipervínculo **Confirm** del correo electrónico de bienvenida. Si se envía un mensaje a una lista mientras un suscriptor tiene el valor `Verified` = `false`, no se envía ningún correo electrónico a dicho suscriptor.

    La propiedad `Verified` de la entidad `Subscriber` se define para aceptar valores null. Si especifica que una consulta debe devolver las entidades `Subscriber`, es posible que algunas de las filas recuperadas puedan no tener una propiedad `Verified`. Por tanto, la entidad `Subscriber` define su propiedad `Verified` para que acepte valores null, a fin de que pueda reflejar con mayor precisión el contenido real de una fila si una consulta devuelve las filas de la tabla que no tienen una propiedad *Verified*. Es posible que esté habituado a usar tablas de SQL Server, en las que cada fila de una tabla tiene el mismo esquema. En una tabla de Almacenamiento de Azure, cada fila es solo una recopilación de propiedades y cada fila puede tener un conjunto diferente de propiedades. Por ejemplo, en la aplicación de ejemplo Servicio de correo electrónico de Azure, las filas que tienen "MailingList" como la clave de fila no tienen una propiedad `Verified`. Si una consulta devuelve una fila de tabla que no tiene una propiedad `Verified`, cuando se crea una instancia de la clase de entidad `Subscriber`, la propiedad `Verified` del objeto de entidad será null. Si la propiedad no acepta valores null, obtendría el mismo valor de `false` para las filas que tienen `Verified` = `false` y para las filas que no tienen una propiedad `Verified` en ningún momento. Por tanto, un procedimiento recomendado para usar tablas de Azure es que cada propiedad de una clase de entidad acepte valores null a fin de leer con precisión las filas que se crearon con el uso de clases de entidades diferentes o versiones distintas de la clase de entidad actual.

### Incorporación del controlador MVC de Subscriber

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Controladores* del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *SubscriberController.cs* de la carpeta *Controladores* y haga clic en **Agregar**. (Asegúrese de que obtiene *Subscriber.cs* y no *Subscribe.cs*; agregará *Subscribe.cs* más adelante).

3.  Abra *SubscriberController.cs* y examine el código.

    La mayoría del código de este controlador es similar al que se observa en el controlador `MailingList`. Incluso el nombre de tabla es el mismo, porque la información del suscriptor se mantiene en la tabla `MailingList`. Después del método `FindRow`, se ve un método `GetListNames`. Este método obtiene los datos de una lista desplegable de las páginas **Create** y **Edit**, donde puede seleccionar la lista de distribución de correo a la que suscribir una dirección de correo electrónico.

         private List<MailingList> GetListNames()
         {
             var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
             var lists = mailingListTable.ExecuteQuery(query).ToList();
             return lists;
         }

    Se trata de la misma consulta que se observa en el controlador `MailingList`. Para la lista desplegable, desea filas que contienen información acerca de las listas de distribución de correo, por lo que solo selecciona las que tienen la propiedad RowKey = "mailinglist".

    Para el método que recupera datos para la página **Index**, desea las filas que tienen información sobre el suscriptor, por lo que selecciona todas las filas que no tienen la propiedad RowKey = "MailingList".

         public ActionResult Index()
         {
             var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();
             return View(subscribers);
         }

    Tenga en cuenta que la consulta especifica que los datos se leerán en los objetos `Subscriber` (con la especificación de `<Subscriber>`), pero los datos se leerán desde la tabla `mailinglist`.

    **Nota:** el número de suscriptores podría aumentar hasta ser demasiado elevado para controlarse de esta forma en una sola consulta. En una futura versión del tutorial, esperamos implementar la funcionalidad de paginación y mostrar cómo controlar los tokens de continuación. Necesita controlar los tokens de continuación al ejecutar consultas que devolverían más de 1.000 filas: Azure devuelve 1.000 filas y un token de continuación que se usa para ejecutar otra consulta que se inicia donde se interrumpe la anterior. (El Explorador de almacenamiento de Azure no controla los tokens de continuación; por tanto, sus consultas no devolverán más de 1.000 filas). Para obtener más información acerca de los grandes conjuntos de resultados y tokens de continuación, consulte [How to get most out of Azure Tables](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) y [Azure Tables: Expect Continuation Tokens, Seriously](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously).

    En el método `HttpGet Create`, debe configurar los datos para la lista desplegable y, en el método `HttpPost`, debe definir los valores antes de guardar la nueva entidad.

         public ActionResult Create()
         {
             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             var model = new Subscriber() { Verified = false };
             return View(model);
         }

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Subscriber subscriber)
         {
             if (ModelState.IsValid)
             {
                 subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                 if (subscriber.Verified.HasValue == false)
                 {
                     subscriber.Verified = false;
                 }

                 var insertOperation = TableOperation.Insert(subscriber);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

             return View(subscriber);
         }

    La página `HttpPost Edit` es más compleja que la que ve en el controlador `MailingList`, porque la página `Subscriber` le permite cambiar el nombre de lista o la dirección de correo electrónico; ambos son campos de clave. Si el usuario cambia uno de estos campos, tiene que eliminar el registro existente y agregar uno nuevo en lugar de actualizar el registro existente. El siguiente código muestra la parte del método de edición que controla los diferentes procedimientos de la clave frente a los cambios no correspondientes a la clave:

             if (ModelState.IsValid)
             {
                 try
                 {
                     UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                     if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                     {
                         //Las claves no han cambiado -- Actualice la fila
                         var replaceOperation = TableOperation.Replace(editedSubscriber);
                         mailingListTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // Las claves han cambiado; elimine el registro anterior e inserte el nuevo.
                         if (editedSubscriber.PartitionKey != partitionKey)
                         {
                             // PartitionKey ha cambiado; no puede ejecutar las operaciones de eliminación ni inserción en un lote.
                             var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             mailingListTable.Execute(deleteOperation);
                             var insertOperation = TableOperation.Insert(editedSubscriber);
                             mailingListTable.Execute(insertOperation);
                         }
                         else
                         {
                             // RowKey ha cambiado; ejecute las operaciones de eliminación e inserción en un lote.
                             var batchOperation = new TableBatchOperation();
                             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             batchOperation.Insert(editedSubscriber);
                             mailingListTable.ExecuteBatch(batchOperation);
                         }
                     }
                     return RedirectToAction("Index");

    Los parámetros que el enlazador de modelos de MVC transfiere al método `Edit` incluyen los valores originales de dirección de correo electrónico y nombre de lista (en los parámetros `partitionKey` y `rowKey`) y los valores escritos por el usuario (en los parámetros `listName` e `emailAddress`):

         public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

    Los parámetros transferidos al método `UpdateModel` excluyen las propiedades `PartitionKey` y `RowKey` del enlace de modelos:

             var excludeProperties = new string[] { "PartitionKey", "RowKey" };

    El motivo es que las propiedades `ListName` y `EmailAddress` usan `PartitionKey` y `RowKey` como propiedades de respaldo y el usuario puede haber cambiado alguno de estos valores. Cuando el enlazador de modelos actualiza el modelo mediante la configuración de la propiedad `ListName`, la propiedad `PartitionKey` se actualiza automáticamente. Si el enlazador de modelos actualizara la propiedad `PartitionKey` con el valor original de dicha propiedad tras actualizar la propiedad `ListName`, sobrescribiría el valor nuevo definido por la propiedad `ListName`. La propiedad `EmailAddress` actualiza automáticamente la propiedad `RowKey` de la misma forma.

    Después de actualizar el objeto de modelo `editedSubscriber`, el código determina si se ha cambiado la clave de partición o la clave de fila. Si cualquier valor de clave ha cambiado, la fila de suscriptor existente tiene que eliminarse e insertarse una nueva. Si solo cambia la clave de fila, la eliminación y la inserción pueden realizarse en una transacción por lotes atómica.

    Tenga en cuenta que el código crea una entidad nueva para reemplazar la operación `Delete`:

             // RowKey ha cambiado; ejecute las operaciones de eliminación e inserción en un lote.
             var batchOperation = new TableBatchOperation();
             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
             batchOperation.Insert(editedSubscriber);
             mailingListTable.ExecuteBatch(batchOperation);

    Las entidades que se transfieren a operaciones de un lote deben ser distintas. Por ejemplo, no puede crear una entidad `Subscriber`, transferirla a una operación `Delete`, cambiar un valor en la misma entidad `Subscriber` y pasarlo a una operación `Insert`. Si lo ha hecho, el estado de la entidad después del cambio de la propiedad se aplicaría para las operaciones de eliminación e inserción.

    **Nota:** todas las operaciones de un lote deben estar en la misma partición. Habida cuenta de que un cambio en el nombre de lista modifica la clave de partición, no puede realizarse en una transacción.

### Incorporación de las vistas MVC de Subscriber

1.  En el **Explorador de soluciones**, cree una carpeta nueva en la carpeta *Vistas* del proyecto MVC y asígnele el nombre *Subscriber*.

2.  Haga clic con el botón secundario en la carpeta *Vistas\\Subscriber* nueva y seleccione **Agregar elemento existente**.

3.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione los cinco archivos .cshtml de la carpeta *Vistas\\Subscriber* y haga clic en **Agregar**.

4.  Abra el archivo *Edit.cshtml* y examine el código.

         @model MvcWebRole.Models.Subscriber
            
         @{
             ViewBag.Title = "Edit Subscriber";
         }
            
         <h2>Edit Subscriber</h2>
            
         @using (Html.BeginForm()) {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.SubscriberGUID)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
                 <legend>Subscriber</legend>
                 <div class="display-label">
                      @Html.DisplayNameFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.EmailAddress)
                     @Html.ValidationMessageFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Verified)
                 </div>
                 <div class="display-field">
                     @Html.EditorFor(model => model.Verified)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Este código es similar al que ha observado anteriormente en la vista **Edit** de `MailingList`. El valor `SubscriberGUID` no aparece, por lo que no se ofrece automáticamente en un campo de formulario para el método de controlador `HttpPost`. Por tanto, se incluye un campo oculto para preservar este valor.

    Las otras vistas contienen código que es similar al que ya ha visto para el controlador `MailingList`.

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto y, a continuación, haga clic en **Subscribers**.

    ![Página de índice de Subscriber vacía](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png)

2.  Use la función **Create** para agregar algunas listas de distribución de correo y pruebe las funciones **Edit** y **Delete** para asegurarse de que funcionan.

    ![Página de índice de Subscribers con filas](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png)

MessageCreación y prueba de las vistas y el controlador Message
---------------------------------------------------------------

Los administradores usan la interfaz de usuario web **Message** para crear, editar y mostrar información acerca de los mensajes programados para enviarlos a las listas de distribución de correo.

### Incorporación de la clase de entidad Message a la carpeta Modelos

La clase de entidad `Message` se usa para las filas de la tabla `Message` que contienen información acerca del mensaje previsto para enviarlo a una lista. Estas filas incluyen información como la línea de asunto, la lista a la que enviar un mensaje y la fecha programada para enviarlo.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Modelos* del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *Message.cs* de la carpeta Modelos y haga clic en **Agregar**.

3.  Abra *Message.cs* y examine el código.

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
             // DataType.Date muestra solo la fecha (no la hora) y permite enlazar con facilidad jQuery DatePicker
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

             // Estado de cadena pública Pending, Queuing, Processing y
             Complete { get; set; }
         }

    La clase `Message` define un constructor predeterminado que define la propiedad `MessageRef` con un valor exclusivo para el mensaje. Habida cuenta de que el valor forma parte de la clave de fila, el establecedor de la propiedad `MessageRef` también define automáticamente la propiedad `RowKey`. El establecedor de propiedad `MessageRef` concatena el "mensaje" literal y el valor `MessageRef` y coloca tal concatenación en la propiedad `RowKey`.

    El valor `MessageRef` se crea al obtener el valor `Ticks` de `DateTime.Now`. Esto garantiza que, de forma predeterminada, cuando se muestran los mensajes en la interfaz de usuario web, estos aparezcan en el orden en que se crearon para una fecha programada determinada (`ScheduledDate` es la clave de partición). Podría usar un GUID para hacer que las filas del mensaje sean únicas, pero entonces el orden de recuperación predeterminado sería aleatorio.

    El constructor predeterminado también establece el estado predeterminado de Pending para las nuevas filas `message`.

    Para obtener más información acerca de la estructura de tabla `Message`, consulte el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/).

### Incorporación del controlador MVC de Message

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Controladores del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *MessageController.cs* de la carpeta *Controladores* y haga clic en **Agregar**.

3.  Abra *MessageController.cs* y examine el código.

    La mayoría del código de este controlador es similar al que se observa en el controlador `Subscriber`. Lo nuevo aquí es el código para trabajar con blobs. Para cada mensaje, el contenido HTML y de texto sin formato del correo electrónico se carga en el formulario de los archivos .htm y .txt y se almacena en blobs.

    Los blobs se almacenan en contenedores. La aplicación Servicio de correo electrónico de Azure almacena todos sus blobs en un único contenedor con nombre "azuremailblobcontainer" y el código del constructor del controlador obtiene una referencia a este contenedor de blobs:

         public class MessageController : Controller
         {
             private TableServiceContext serviceContext;
             private static CloudBlobContainer blobContainer;

               public MessageController()
             {
                 var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // Si se ejecuta en un sitio web de Azure (no en un servicio en la nube), use el archivo Web.config:
                 //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 // Obtenga el objeto de contexto para trabajar con tablas y una referencia al contenedor de blobs.
                 var tableClient = storageAccount.CreateCloudTableClient();
             serviceContext = tableClient.GetTableServiceContext();
                 var blobClient = storageAccount.CreateCloudBlobClient();
                 blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
             }

    Para cada archivo que un usuario selecciona para cargarlo, la vista MVC proporciona un objeto `HttpPostedFile` que contiene información acerca del archivo. Cuando el usuario crea un mensaje nuevo, el objeto `HttpPostedFile` se usa para guardar el archivo en un blob. Cuando el usuario edita un mensaje, el usuario puede elegir entre cargar un archivo de sustitución o dejar el blob sin modificar.

    El controlador incluye un método al que los métodos `HttpPost Create` y `HttpPost Edit` llaman para guardar un blob:

         private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
         {
             // Recupere una referencia a un blob. 
             CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
             // Cree el blob o sobrescriba el blob existente al cargar un archivo local.
             using (var fileStream = httpPostedFile.InputStream)
             {
                 blob.UploadFromStream(fileStream);
             }
         }

    El método `HttpPost Create` guarda los dos blobs y, a continuación, agrega la fila de tabla `Message`. El nombre de los blobs se asigna mediante la concatenación del valor `MessageRef` con la extensión de nombre de archivo ".htm" o ".txt".

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
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
                 SaveBlob(message.MessageRef + ".htm", file);
                 SaveBlob(message.MessageRef + ".txt", txtFile);

                 var insertOperation = TableOperation.Insert(message);
                 messageTable.Execute(insertOperation);

                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             return View(message);
         }

    El método `HttpGet Edit` valida que el mensaje recuperado tenga el estado `Pending`, a fin de que el usuario no pueda cambiar el mensaje una vez que el rol de trabajo B ha empezado a procesarlo. Los métodos `HttpPost Edit`, `Delete` y `DeleteConfirmed` presentan un código similar.

         public ActionResult Edit(string partitionKey, string rowKey)
         {
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Message can't be edited because it isn't in Pending status.");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
             return View(message);
         }

    En el método `HttpPost Edit`, el código guarda un blob nuevo solo si el usuario elige cargar un archivo nuevo. El siguiente código omite la parte del control de simultaneidad del método, que es el mismo que el que ha visto anteriormente para el controlador `MailingList`.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
             DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
         {
             if (ModelState.IsValid)
             {
                 var excludePropLst = new List<string>();
                 excludePropLst.Add("PartitionKey");
                 excludePropLst.Add("RowKey");

                 if (httpFile == null)
                 {
                     // No han escrito ninguna ruta de acceso ni se han desplazado a ningún archivo, así que no actualice el archivo.
                     excludePropLst.Add("HtmlPath");
                 }
                 else
                 {
                     // Han escrito una ruta de acceso o se han desplazado a un archivo; asuma que se ha modificado.
                     SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                 }

                 if (txtFile == null)
                 {
                     excludePropLst.Add("TextPath");
                 }
                 else
                 {
                     SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                 }

                 string[] excludeProperties = excludePropLst.ToArray();

                 try
                 {
                     UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                     if (editedMsg.PartitionKey == partitionKey)
                     {
                         // Las claves no se han cambiado -- actualice la fila.
                         var replaceOperation = TableOperation.Replace(editedMsg);
                         messageTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // La clave de partición se ha cambiado -- elimine e inserte la fila.
                         // (La clave de partición tiene la fecha programada, que se puede cambiar;
                         // la clave de fila tiene MessageRef, que no cambia).
                         var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                         messageTable.Execute(deleteOperation);
                         var insertOperation = TableOperation.Insert(editedMsg);
                         messageTable.Execute(insertOperation);
                     }
                     return RedirectToAction("Index");
                 }

    Si se cambia la fecha programada, la clave de partición también cambia y es necesario eliminar e insertar una fila. Esto no puede realizarse en una transacción porque afecta a más de una partición.

    El método `HttpPost Delete` elimina los blobs si elimina la fila de la tabla:

         [HttpPost, ActionName("Delete")]
         public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
         {
             // Obtenga la fila otra vez para asegurarse de que aún tiene el estado Pending.
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Message can't be deleted because it isn't in Pending status.");
             }

             DeleteBlob(message.MessageRef + ".htm");
             DeleteBlob(message.MessageRef + ".txt");
             var deleteOperation = TableOperation.Delete(message);
             messageTable.Execute(deleteOperation);
             return RedirectToAction("Index");
         }

         private void DeleteBlob(string blobName)
         {
             var blob = blobContainer.GetBlockBlobReference(blobName);
             blob.Delete();
         }

### Incorporación de las vistas MVC de Message

1.  En el **Explorador de soluciones**, cree una carpeta nueva en la carpeta *Vistas* del proyecto MVC y asígnele el nombre `Message`.

2.  Haga clic con el botón secundario en la carpeta *Vistas\\Message* nueva y seleccione **Agregar elemento existente**.

3.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione los cinco archivos .cshtml de la carpeta *Vistas\\Message* y haga clic en **Agregar**.

4.  Abra el archivo *Edit.cshtml* y examine el código.

         @model MvcWebRole.Models.Message
            
         @{
             ViewBag.Title = "Edit Message";
         }
            
         <h2>Edit Message</h2>
            
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
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName, "MailingList")
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.SubjectLine)
                     @Html.ValidationMessageFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-label">
                     HTML Path: Leave blank to keep current HTML File.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="file" />
                 </div>
                 <div class="editor-label">
                     Text Path: Leave blank to keep current Text File.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="TxtFile" />
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ScheduledDate)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.ScheduledDate)
                     @Html.ValidationMessageFor(model => model.ScheduledDate)
                 </div>
                 <div class="display-label">
                     @Html.DisplayNameFor(model => model.Status)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Status)
                 </div>
                <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    El método `HttpPost Edit` necesita la clave de partición y la clave de fila, por lo que el código ofrece estos valores en los campos ocultos. Los campos ocultos no resultaron necesarios en el controlador `Subscriber` porque a) las propiedades `ListName` y `EmailAddress` del modelo `Subscriber` actualizan las propiedades `PartitionKey` y `RowKey` y b) las propiedades `ListName` y `EmailAddress` se incluían con las aplicaciones auxiliares `EditorFor` en la vista Edit. Cuando el enlazador de modelos de MVC del modelo `Subscriber` actualiza la propiedad `ListName`, la propiedad `PartitionKey` se actualiza automáticamente y, cuando el enlazador de modelos de MVC actualiza la propiedad `EmailAddress` del modelo `Subscriber`, la propiedad `RowKey` se actualiza automáticamente. En el modelo `Message`, los campos que se asignan a la clave de partición y a la clave de fila no son campos editables, por lo que no se definen de la misma manera.

    También se incluye un campo oculto para la propiedad `MessageRef`. Se trata del mismo valor que la clave de partición, pero se incluye para que el método `HttpPost Edit` ofrezca mayor claridad del código. Incluir el campo oculto `MessageRef` permite que el código del método `HttpPost Edit` haga referencia al valor `MessageRef` mediante dicho nombre cuando construye los nombres de archivo para los blobs.

5.  Abra el archivo *Index.cshtml* y examine el código.

         @model IEnumerable<MvcWebRole.Models.Message>
            
         @{
             ViewBag.Title = "Messages";
         }
            
         <h2>Messages</h2>
            
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.SubjectLine)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.ScheduledDate)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Status)
                 </th>
                 <th></th>
             </tr>
             @foreach (var item in Model)
             {
                 <tr>
                     <td>
                         @Html.DisplayFor(modelItem => item.ListName)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.SubjectLine)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.ScheduledDate)
                     </td>
                     <td>
                         @item.Status
                     </td>
                     <td>
                         @if (item.Status == "Pending")
                         {
                             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
                         }
                         @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
                     </td>
                 </tr>
             }
            
         </table>

    Una diferencia en este caso con respecto a otras vistas de **índice** es que los vínculos **Edit** y **Delete** aparecen solo para los mensajes que tienen el estado `Pending`:

         @if (item.Status == "Pending")
         {
             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
         }

    Esto ayuda a prevenir que el usuario realice cambios en un mensaje después de que el rol de trabajo A haya comenzado a procesarlo.

    Las otras vistas contienen código que es similar al de la vista **Edit** o al de otras vistas que presentan otros controladores.

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto y, a continuación, haga clic en **Messages**.

    ![Página de índice de Message vacía](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png)

2.  Use la función **Create** para agregar algunas listas de distribución de correo y pruebe las funciones **Edit** y **Delete** para asegurarse de que funcionan.

    ![Página de índice de Subscribers con filas](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png)

UnsubscriberCreación y prueba de las vistas y el controlador Unsubscribe
------------------------------------------------------------------------

A continuación, implementará la interfaz de usuario para el proceso de cancelación de suscripciones.

**Nota:** en este tutorial solo se desarrolla el controlador para el proceso de anulación de suscripciones, no para el proceso de suscripción. Como se ha explicado en el [primer tutorial](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/), la interfaz de usuario y el método de servicio para el proceso de suscripción se han dejado fuera hasta que implementemos la seguridad apropiada para el método de servicio. Hasta ese momento, puede usar las páginas **Subscriber** para administradores a fin de suscribir las direcciones de correo electrónico a las listas.

### Incorporación del modelo de vista Unsubscribe a la carpeta Modelos

El modelo de vista `UnsubscribeVM` se usa para transferir datos entre el controlador `Unsubscribe` y su vista.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta `Modelos` del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo `UnsubscribeVM.cs` de la carpeta Modelos y haga clic en **Agregar**.

3.  Abra `UnsubscribeVM.cs` y examine el código.

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

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta `Controladores` del proyecto MVC y seleccione **Agregar elemento existente**.

2.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *UnsubscribeController.cs* de la carpeta *Controladores* y haga clic en **Agregar**.

3.  Abra *UnsubscribeController.cs* y examine el código.

    Este controlador tiene un método `HttpGet Index` que muestra la página de cancelación de suscripción inicial y un método `HttpPost Index` que procesa el botón **Confirm** o **Cancel**.

    El método `HttpGet Index` usa el GUID y el nombre de lista en la cadena de consulta para obtener la fila de tabla `MailingList` para el suscriptor. A continuación, coloca toda la información que la vista necesita en el modelo de vista y muestra la página **Unsubscribe**. Define la propiedad `Confirmed` como null a fin de pedir a la vista que muestre la versión inicial de la página **Unsubscribe**.

          public ActionResult Index(string id, string listName)
          {
              if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
              {
                  ViewBag.errorMessage = "Empty subscriber ID or list name.";
                  return View("Error");
              }
              string filter = TableQuery.CombineFilters(
                  TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                  TableOperators.And,
                  TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
              var query = new TableQuery<Subscriber>().Where(filter);
              var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
              if (subscriber == null)
              {
                  ViewBag.Message = "You are already unsubscribed";
                  return View("Message");
              }
              var unsubscribeVM = new UnsubscribeVM();
              unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
              unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
              unsubscribeVM.SubscriberGUID = id;
              unsubscribeVM.Confirmed = null;
              return View(unsubscribeVM);
          }

    Nota: el valor SubscriberGUID no está en la clave de partición ni en la clave de fila, por lo que el rendimiento de esta consulta se reducirá a medida que aumente el tamaño de la partición (el número de direcciones de correo electrónico de una lista de distribución de correo). Para obtener más información acerca de las alternativas para hacer que esta consulta sea más escalable, consulte el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/).

    El método `HttpPost Index` vuelve a usar el GUID y el nombre de lista para obtener la información del suscriptor y rellenar las propiedades del modelo de vista. A continuación, si se ha hecho clic en el botón **Confirm**, elimina la fila de suscriptor de la tabla `MailingList`. Si se presiona el botón **Confirm**, también define la propiedad `Confirm` como `true`; de lo contrario, define la propiedad `Confirm` como `false`. El valor de la propiedad `Confirm` es el que pide a la vista que muestre la versión confirmada o cancelada de la página **Unsubscribe**.

         [HttpPost] 
         [ValidateAntiForgeryToken]
         public ActionResult Index(string subscriberGUID, string listName, string action)
         {
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

             var unsubscribeVM = new UnsubscribeVM();
             unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
             unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
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

1.  En el **Explorador de soluciones**, cree una carpeta nueva en la carpeta *Vistas* del proyecto MVC y asígnele el nombre *Unsubscribe*.

2.  Haga clic con el botón secundario en la carpeta *Vistas\\Unsubscribe* nueva y seleccione **Agregar elemento existente**.

3.  Desplácese hasta la carpeta en que ha descargado la aplicación de ejemplo, seleccione el archivo *Index.cshtml* de la carpeta *Vistas\\Unsubscribe* y haga clic en **Agregar**.

4.  Abra el archivo *Index.cshtml* y examine el código.

         @model MvcWebRole.Models.UnsubscribeVM
            
         @{
             ViewBag.Title = "Unsubscribe";
             Layout = null;
         }
            
         <h2>Email List Subscription Service</h2>
            
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
                         Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)

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
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    La línea `Layout = null` especifica que no se debe usar el archivo \_Layout.cshtml para mostrar esta página. La página **Unsubscribe** muestra una interfaz de usuario muy sencilla sin los encabezados y los pies de página usados para las páginas de administrador.

    En el cuerpo de la página, la propiedad `Confirmed` determina lo que se mostrará en la página: los botones **Confirm** y **Cancel** si la propiedad es null, el mensaje unsubscribe-confirmed si la propiedad es true y el mensaje unsubscribe-canceled si la propiedad es false.

### Prueba de la aplicación

1.  Presione CTRL+F5 para ejecutar el proyecto y, a continuación, haga clic en **Subscribers**.

2.  Haga clic en **Create** y cree un nuevo suscriptor para cualquier lista de distribución de correo creada para realizar las pruebas anteriores.

    Deje la ventana del explorador abierta en la página **Index** de **Subscribers**.

3.  Abra el Explorador de almacenamiento de Azure y, a continuación, seleccione la cuenta de almacenamiento de prueba.

4.  Haga clic en **Tablas** en **Tipo de almacenamiento**, seleccione la tabla **MailingList** y, a continuación, haga clic en **Consulta**.

5.  Haga doble clic en la fila de suscriptor que ha agregado.

    ![Explorador de almacenamiento de Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png)

6.  En el cuadro de diálogo **Edit Entity**, seleccione y copie el valor `SubscriberGUID`.

    ![Explorador de almacenamiento de Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png)

7.  Vuelva a la ventana del explorador. En la barra de direcciones del explorador, cambie el texto "Subscriber" de la URL por "unsubscribe?ID=[guidvalue]&listName=[listname]", donde [guidvalue] es el GUID que ha copiado del Explorador de almacenamiento de Azure y [listname] es el nombre de la lista de distribución de correo. Por ejemplo:

         http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Aparece la versión de la página **Unsubscribe** que pide la confirmación:

    ![Página Unsubscribe](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png)

8.  Haga clic en **Confirm** y verá la confirmación de que se ha cancelado la suscripción de la dirección de correo electrónico.

    ![Página de confirmación de la cancelación de la suscripción](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png)

9.  Vuelva a la página **Index** de **Subscribers** para comprobar que la fila de suscriptor ya no está ahí.

Arquitectura alternativa(opcional) Desarrollo de la arquitectura alternativa
----------------------------------------------------------------------------

Los siguientes cambios de las instrucciones se aplican solo si desea compilar la arquitectura alternativa, es decir, ejecutar la interfaz de usuario web en un sitio web de Azure en lugar de en un rol web del Servicio de nube de Azure.

-   Cuando crea la solución, cree primero el proyecto **Aplicación web de ASP.NET MVC 4** y, a continuación, agregue a la solución un proyecto del **Servicio de nube de Azure** con un rol de trabajo.

-   Almacene la cadena de conexión de Almacenamiento de Azure en el archivo Web.config en lugar de en el archivo de configuración del servicio en la nube. (Esto solo funciona para los sitios web de Azure. Si trata de usar el archivo Web.config para la cadena de conexión de almacenamiento en un rol web del Servicio de nube de Azure, obtendrá un error HTTP 500).

Agregue una cadena de conexión nueva con nombre `StorageConnectionString` al archivo *Web.config*, como se muestra en el ejemplo siguiente:

           <connectionStrings>
              <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
              <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
           </connectionStrings>

Obtenga los valores para la cadena de conexión del [Portal de administración de Azure](http://manage.windowsazure.com): seleccione la pestaña **Almacenamiento** y la cuenta de almacenamiento y, a continuación, haga clic en **Manage keys** en la parte inferior de la página.

-   Siempre que aparezca `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` en el código, reemplácelo por `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`.

Pasos siguientesPasos siguientes
--------------------------------

Tal y como se ha explicado en el [primer tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/), no vamos a explicar cómo desarrollar el proceso de suscripción de forma detallada en este tutorial hasta que implementemos un secreto compartido para proteger el método de servicio de la API web de ASP.NET. No obstante, la restricción de IP también protege el método de servicio y puede agregar la funcionalidad de suscripción si copia los siguientes archivos del proyecto descargado.

Para el método de servicio de la API web de ASP.NET:

-   Controladores\\SubscribeAPI.cs

Para la página web que los suscriptores obtienen cuando hacen clic en el vínculo **Confirm** en el correo electrónico que genera el método de servicio:

-   Modelos\\SubscribeVM.cs
-   Controladores\\SubscribeController.cs
-   Vistas\\Subscribe\\Index.cshtml

En el [próximo tutorial](/es-es/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/), configurará y programará el rol de trabajo A, que se corresponde con el rol de trabajo que programa los correos electrónicos.

Podrá encontrar vínculos a recursos adicionales acerca de cómo utilizar las tablas, colas y blobs de almacenamiento de Azure al final del [último tutorial de la serie](/es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
[Tutorial 4](/es-es/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)

