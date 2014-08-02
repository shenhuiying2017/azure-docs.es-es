<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" urlDisplayName="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 4: Worker role A" metaKeywords="Azure tutorial, .NET multi-tier app, multi-tier architecture" description="The fourth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role A (email scheduler) for the Azure Email Service application - 4 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Creación del rol de trabajo A (programador de correo electrónico) para la aplicación Servicio de correo electrónico de Azure: 4 de 5.
=====================================================================================================================================

Este es el cuarto tutorial de una serie de cinco que muestran cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Consultar y actualizar las tablas de almacenamiento de Azure.
-   Agregar elementos de trabajo a una cola para procesar mediante otro rol de trabajo.
-   Administrar apagados planificados mediante la anulación del método `OnStop`.
-   Administrar apagados no planificados asegurándose de que no faltan correos electrónicos y no se enviaron correos electrónicos duplicados.
-   Probar un rol de trabajo que usa las tablas de almacenamiento de Azure mediante el Explorador de almacenamiento de Azure.

Ya creó el proyecto de rol de trabajo A cuando creó el proyecto de servicio en la nube. Por lo tanto, todo lo que tiene que hacer es programar el rol de trabajo y configurarlo para usar su cuenta de almacenamiento de Azure.

Incorporación de una referencia al proyectoIncorporación de una referencia al proyecto web
------------------------------------------------------------------------------------------

Necesita una referencia para el proyecto web porque es ahí donde se definen las clases de entidad. Usará las mismas clases de entidad en el rol de trabajo B para leer y escribir datos en las tablas de Azure que usa la aplicación.

**Nota:** en una aplicación de producción no establece una referencia para un proyecto web a partir de un proyecto de rol de trabajo, porque esto tiene como resultado una referencia a varios ensamblados dependientes que no desea o no necesita en el rol de trabajo. Por lo general, mantiene clases de modelo compartidos en un proyecto de biblioteca de clases y tanto los proyectos de rol web como de trabajo harían referencia al proyecto de biblioteca de clase. Para que la estructura de la solución siga siendo sencilla, las clases de modelos se almacenan en el proyecto web de este tutorial.

1.  Haga clic con el botón secundario en el proyecto WorkerRoleA y seleccione **Agregar referencia**.

    ![Agregar una referencia en el proyecto de WorkerRoleA](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-reference-menu.png)

2.  En **Administrador de referencias**, agregue una referencia al proyecto MvcWebRole (o al proyecto de aplicación web si está ejecutando la interfaz de usuario web en un sitio web de Azure) y, a continuación, haga clic en **OK**.

    ![Agregar una referencia a MvcWebRole](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png)

Incorporación de referencias a SCL 1.7Incorporación de una referencia a un ensamblado de SCL 1.7
------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Omita este paso si ya instaló SDK 2.3 o posterior.

La versión 2.0 de la biblioteca del cliente de almacenamiento (SCL) 2.0 no tiene todo lo necesario para realizar un diagnóstico, por lo que tendrá que agregar una referencia a uno de los ensamblados 1.7. Esta acción ya la llevó a cabo si siguió los pasos del tutorial anterior; sin embargo, se incluyen instrucciones en caso de que omitiera ese paso.

1.  Haga clic con el botón secundario en el proyecto WorkerRoleA y seleccione **Agregar referencia**.

2.  Haga clic en el botón **Examinar...** en la parte inferior del cuadro de diálogo.

3.  Desplácese hasta la siguiente carpeta:

         C:\Archivos de programa\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Seleccione *Microsoft.WindowsAzure.StorageClient.dll* y, a continuación, haga clic en **Agregar**.

5.  En el cuadro de diálogo **Administrador de referencias**, haga clic en **OK**.

Incorporación del modelo SendEmailIncorporación del modelo SendEmail
--------------------------------------------------------------------

El rol de trabajo A crea las filas `SendEmail` en la tabla `Message` y el rol de trabajo B lee esas filas para obtener la información que necesita para enviar correos electrónicos. La siguiente imagen muestra un subconjunto de propiedades para dos filas `Message` y tres filas `SendEmail` en la tabla `Message`.

    ![message table with sendmail][mtas-sendMailTbl]

Estas filas de la tabla `Message` sirven a varios propósitos:

-   Proporcionan toda la información que el rol de trabajo B necesita para enviar un solo correo electrónico.
-   Realizan un seguimiento para confirmar si se envió un correo electrónico y así evitar que se envíen duplicados en caso de que un rol de trabajo se reinicie después de un error.
-   Permiten que el rol de trabajo A determine cuándo se enviaron todos los correos electrónicos para un mensaje, de modo que se pueda marcar como `Complete`.

Para leer y escribir en las filas `SendEmail`, se requiere una clase de modelo. Debido a que debe estar accesible para el rol de trabajo A y el rol de trabajo B, y debido a que todas las demás clases de modelo se definen en el proyecto web, es conveniente definir también ésta en el proyecto web.

1.  En **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Modelos en el proyecto web y seleccione **Agregar elemento existente**.

    ![Agregar elementos existentes a la carpeta Modelos en el proyecto web](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-add-existing-for-sendemail-model.png)

2.  Diríjase a la carpeta donde descargó la aplicación de ejemplo, seleccione el archivo *SendEmail.cs* de la carpeta Modelos del proyecto web y haga clic en Agregar.

3.  Abra *SendEmail.cs* y examine el código.

         public class SendEmail : TableEntity
         {
             public long MessageRef { get; set; }
             public string EmailAddress { get; set; }
             public DateTime? ScheduledDate { get; set; }
             public String FromEmailAddress { get; set; }
             public string SubjectLine { get; set; }
             public bool? EmailSent { get; set; }
             public string SubscriberGUID { get; set; }
             public string ListName { get; set; }
         }

    Este código es similar a las otras clases de modelo, salvo que no se incluye ningún atributo DataAnnotations porque no hay una interfaz de usuario asociada a este modelo; no se usa en un controlador de MVC.

Incorporación de código de rol de trabajoIncorporación de código que se ejecuta cuando se inicia el rol de trabajo
------------------------------------------------------------------------------------------------------------------

1.  En el proyecto WorkerRoleA, abra *WorkerRole.cs* y examine el código.

         public class WorkerRole : RoleEntryPoint
         {
             public override void Run()
             {
                 // Esta es una implementación de trabajo de ejemplo. Sustitúyala por su lógica.
                 Trace.WriteLine("WorkerRole1 entry point called", "Information");

                 while (true)
                 {
                     Thread.Sleep(10000);
                     Trace.WriteLine("Working", "Information");
                 }
             }

             public override bool OnStart()
             {
                 // Definir el número máximo de conexiones simultáneas 
                 ServicePointManager.DefaultConnectionLimit = 12;

                 // Para obtener información acerca de la gestión de los cambios de configuración
                 // consulte el tema de MSDN en http://go.microsoft.com/fwlink/?LinkId=166357.

                 return base.OnStart();
             }
         }

    Este es el código de la plantilla predeterminada para el rol de trabajo. Existe un método `OnStart` en el que se puede poner código de inicialización que solo se ejecuta cuando se inicia una instancia del rol de trabajo y un método `Run` al que se llama después de que se completa el método `OnStart`. Debe reemplazar este código por su propio código de inicialización y ejecución.

2.  Elimine *WorkerRole.cs* y, a continuación, haga clic con el botón secundario en el proyecto WorkerRoleA y seleccione **Agregar elemento existente**.

    ![Agregar elemento existente al rol de trabajo A](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-existing.png)

3.  Diríjase a la carpeta donde descargó la aplicación de ejemplo, seleccione el archivo *WorkerRoleA.cs* del proyecto WorkerRoleA y haga clic en **Agregar**.

4.  Abra *WorkerRoleA.cs* y examine el código.

    El método `OnStart` inicializa los objetos de contexto que necesita para trabajar con las entidades de almacenamiento de Azure. Además, se cerciora de que existan todas las tablas, las colas y los contenedores de blobs que va a usar en el método `Run`. El código que realiza estas tareas es similar a lo que vio anteriormente en los constructores del controlador MVC. Podrá configurar la cadena de conexión que utiliza este método posteriormente.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

             ConfigureDiagnostics();
             Trace.TraceInformation("Initializing storage account in WorkerA");
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

             CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
             sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
             var tableClient = storageAccount.CreateCloudTableClient(); 
             mailingListTable = tableClient.GetTableReference("mailinglist"); 
             messageTable = tableClient.GetTableReference("message"); 
             messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

             // Crear si no existe para la cola, el contenedor de blobs o la tabla SentEmail. 
             sendEmailQueue.CreateIfNotExists(); 
             messageTable.CreateIfNotExists(); 
             mailingListTable.CreateIfNotExists(); 
             messagearchiveTable.CreateIfNotExists(); 

             return base.OnStart();
         }

    Es posible que haya visto documentación anterior sobre el trabajo con el almacenamiento de Azure que muestra el código de inicialización en un bucle que revisa la presencia de errores de transporte. Esta acción ya no es necesaria porque la API tiene ahora un mecanismo de reintento integrado que absorbe los errores de red transitorios hasta en 3 intentos adicionales.

    El método `ConfigureDiagnostics` que el método `OnStart` llama configura el seguimiento para que pueda ver el resultado desde los métodos `Trace.Information` y `Trace.Error`. Este método se explica en el [segundo tutorial](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

    El método `OnStop` establece la variable global `onStopCalled` como verdadera, posteriormente espera que el método `Run` establezca la variable global `returnedFromRunMethod` como verdadera, lo que la señala como lista para realizar un apagado limpio.

         public override void OnStop()
         {
             onStopCalled = true;
             while (returnedFromRunMethod == false)
             {
                 System.Threading.Thread.Sleep(1000);
             }
         }

    Al método `OnStop` se le llama cuando el rol de trabajo se apaga debido a uno de los siguientes motivos:

    -   Azure necesita reiniciar la máquina virtual (la instancia de rol web o rol de trabajo) o la máquina física que hospeda la máquina virtual.
    -   Detuvo el servicio en la nube mediante el botón **Stop** en el Portal de administración de Azure.
    -   Implementó una actualización en su proyecto de servicio en la nube.

    El método `Run` supervisa la variable `onStopCalled` y deja de extraer todo elemento de trabajo nuevo para procesar cuando esa variable cambia a `true`. Esta coordinación entre los métodos `OnStop` y `Run` permite un apagado estable del proceso de trabajo.

    Azure instala de manera periódica actualizaciones del sistema operativo para asegurar que la plataforma se mantenga segura, confiable y tenga un buen rendimiento. Por lo general, estas actualizaciones requieren que las máquinas que hospedan el servicio en la nube se cierren y se reinicien. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

    El método `Run` realiza dos funciones:

    -   Examina la tabla `message` en busca de mensajes programados para su envío hoy o antes, para lo cual todavía no se han creado elementos de trabajo en cola.

    -   Examina la tabla `message` en busca de mensajes que tienen un estado que indica que todos los elementos de trabajo de la cola se crearon, pero que todavía no se han enviados todos los correos electrónicos. Si encuentra uno, examina las filas `SendEmail` de ese mensaje para ver si se enviaron todos los correos electrónicos y, si así fuere, actualiza el estado a `Completed` y archiva la fila `message`.

    El método comprueba también la variable global `onStopCalled`. Cuando la variable es `true`, el método deja de extraer nuevos elementos de trabajo para procesar y devuelve cuando se completan las tareas que ya se iniciaron.

         public override void Run()
         {
             Trace.TraceInformation("WorkerRoleA entering Run()");
             while (true)
             {
                 try
                 {
                     var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                     // Si se ha llamado a OnStop, devolver para realizar un apagado estable.
                     if (onStopCalled == true)
                     {
                         Trace.TraceInformation("onStopCalled WorkerRoleB");
                         returnedFromRunMethod = true;
                         return;
                     }
                     // Recuperar todos los mensajes que están programados para mañana o antes
                     // y con un estado Pending o Queuing.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                     TableOperation replaceOperation;
                     // Procesar cada mensaje (correos electrónicos de la cola que se enviarán).
                     foreach (Message messageToProcess in messagesToProcess)
                     {
                         string restartFlag = "0";
                         // Si el mensaje ya está en el estado Queuing,
                         // establecer indicador para señalar que es un reinicio.
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

                         // Si el mensaje está en estado Pending, cambiarlo
                         // a Queuing.
                         if (messageToProcess.Status == "Pending")
                         {
                             messageToProcess.Status = "Queuing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }

                         // Si el mensaje está en el estado Queuing, 
                         // procesarlo y cambiarlo al estado Processing;
                         // de lo contrario, ya está en estado de procesamiento y, 
                         // en ese caso, compruebe que el procesamiento se haya completado.
                         if (messageToProcess.Status == "Queuing")
                         {
                             ProcessMessage(messageToProcess, restartFlag);

                             messageToProcess.Status = "Processing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }
                         else
                         {
                             CheckAndArchiveIfComplete(messageToProcess);
                         }
                     }

                     // Suspender durante un minuto para reducir los costes de consulta. 
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
                 catch (Exception ex)
                 {
                     string err = ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException.Message;
                     }
                     Trace.TraceError(err);
                     // No llene el almacén de seguimiento si tenemos un error en el bucle de proceso de cola.
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
             }
         }

    Tenga en cuenta que todo el trabajo se realiza en un bucle infinito en un bloque `while` y que todo el código del bloque `while` se encapsula en un bloque `try`-`catch` para evitar una excepción no controlada. Si se produce una excepción no controlada, Azure generará el evento [UnhandledException](http://msdn.microsoft.com/en-us/library/system.appdomain.unhandledexception.aspx), el proceso de trabajo se termina y el rol se deja sin conexión. El rol de trabajo lo reinicia Azure, pero tarda varios minutos. El bloque `try` llama a `TraceError` para registrar el error y, a continuación, se suspende durante 60 segundos de modo que si el error es persistente, el mensaje de error no se repite tantas veces. En una aplicación de producción puede enviar un correo electrónico a un administrador en el bloque `try`.

    El método `Run` procesa una consulta para las filas `message` de la tabla `message` que programaron una fecha antes de mañana:

                     // Recuperar todos los mensajes que están programados para mañana o antes
                     // y con un estado Pending o Queuing.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

    **Nota:** una de las ventajas de mover las filas de mensaje a la tabla `messagearchive` después de que se procesan, es que esta consulta solo necesita especificar `PartitionKey` y `RowKey` como criterios de búsqueda. Si no archiváramos las filas procesadas, la consulta también tendría que especificar un campo sin clave (`Status`) y tendría que buscar en más filas. El tamaño de la tabla aumentaría y la consulta tardaría más tiempo, lo que podría iniciar la obtención de tokens de continuación.

    Si un mensaje está en estado `Pending`, el procesamiento todavía no comienza; si está en estado `Queuing`, el procesamiento comenzó antes pero se interrumpió antes de que se crearan todos los mensajes en cola. En ese caso, debe realizarse una comprobación adicional en el rol de trabajo B cuando envía cada correo electrónico para asegurarse de que el correo electrónico todavía no se ha enviado. Ese es el objetivo de la variable `restartFlag`.

                         string restartFlag = "0";
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

    A continuación, el código define las filas `message` con el estado `Pending` en `Queuing`. Luego, para aquellas filas, más cualquiera que ya tenga el estado `Queuing`, llama al método `ProcessMessage` para crear los elementos de trabajo en cola y así enviar correos electrónicos para el mensaje.

                         if (messageToProcess.Status == "Pending")
                         {
                             messageToProcess.Status = "Queuing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }

                         if (messageToProcess.Status == "Queuing")
                         {
                             ProcessMessage(messageToProcess, restartFlag);

                             messageToProcess.Status = "Processing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }
                         else
                         {
                             CheckAndArchiveIfComplete(messageToProcess);
                         }

    Después de procesar un mensaje en estado `Queuing`, el código define el estado de la fila `Message` en `Processing`. Las filas de la tabla `message` que no tienen el estado `Pending` o `Queuing` ya tienen el estado `Processing` y, para esas filas, el código llama a un método que comprueba si se enviaron todos los correos electrónicos del mensaje. Si se enviaron todos los correos electrónicos, la fila `message` se archiva.

    Después de procesar todos los registros recuperados por la consulta, el código se suspende durante un minuto.

                 // Suspender durante un minuto para reducir los costes de consulta.
                 System.Threading.Thread.Sleep(1000*60);

    Existe un cobro mínimo por cada consulta de almacenamiento de Azure, incluso si no devuelve ningún dato, por lo que un nuevo examen continuo se sumaría innecesariamente a sus gastos de Azure. Mientras se escribe este tutorial, el costo es de $0,10 por millón de transacciones (una consulta se considera como una transacción), por lo que el tiempo de suspensión podría ser mucho menos de un minuto y el coste del examen de las tablas en busca de los mensajes que se van a enviar todavía sería mínimo. Para obtener más información sobre el precio, consulte el [primer tutorial](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

    **Nota sobre los subprocesos y la utilización óptima de la CPU:** hay dos tareas en el método `Run` (poner correos electrónicos en cola y comprobar los mensajes completados) que se ejecutan de manera secuencial en un solo subproceso. Una máquina virtual (VM) pequeña tiene 1,75 GB de RAM y solo una CPU, por lo que probablemente es bueno ejecutar estas tareas de manera secuencial con solo un subproceso. Supongamos que su aplicación necesita más memoria que la máquina virtual pequeña proporcionada para funcionar eficazmente. Una máquina virtual mediana proporciona 3,5 GB de RAM y 2 CPU, pero esta aplicación solo usaría una CPU porque tiene un solo subproceso. Para aprovechar todas las CPU, necesita crear un subproceso de trabajo para cada CPU. Aun así, un subproceso no utiliza completamente una sola CPU. Cuando un subproceso realiza llamadas de red o de E/S, debe esperar a que la llamada de red o de E/S se complete y, mientras espera, no realiza ninguna tarea de utilidad. Si se implementa el método `Run` con dos subprocesos, cuando un subproceso espera que se complete una operación de red o de E/S, el otro subproceso podría realizar algún trabajo útil.

    El método `ProcessMessage` obtiene todas las direcciones de correo electrónico de la lista de correo electrónico de destino y crea un elemento de trabajo en cola para cada dirección de correo electrónico. Mientras crea los elementos de trabajo en cola, crea también filas `SendEmail` en la tabla `Message`. Estas filas proporcionan al rol de trabajo B la información que necesita para enviar correos electrónicos e incluye una propiedad `EmailSent` que realiza un seguimiento de cada correo electrónico que se ha enviado.

         private void ProcessMessage(Message messageToProcess, string restartFlag)
         {
             // Obtener información de lista de correo para obtener la dirección de correo electrónico del remitente.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                 return;
             }
             // Obtener direcciones de correo electrónico para esta lista de correo.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

             foreach (Subscriber subscriber in subscribers)
             {
                 // Comprobar que la dirección de correo electrónico del suscriptor se ha verificado.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                     continue;
                 }

                 // Crear una entidad SendEmail para este correo electrónico.              
                 var sendEmailRow = new SendEmail
                 {
                     PartitionKey = messageToProcess.PartitionKey,
                     RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                     EmailAddress = subscriber.EmailAddress,
                     EmailSent = false,
                     MessageRef = messageToProcess.MessageRef,
                     ScheduledDate = messageToProcess.ScheduledDate,
                     FromEmailAddress = mailingList.FromEmailAddress,
                     SubjectLine = messageToProcess.SubjectLine,
                     SubscriberGUID = subscriber.SubscriberGUID,
                     ListName = mailingList.ListName
                 };

                 // Cuando intentamos agregar la entidad a la tabla SendEmail, 
                 // se podría producir una excepción si este rol de trabajo se 
                 // volviera inactivo después de procesar alguna de las direcciones de correo electrónico y luego se reiniciara.
                 // En ese caso, la fila podría estar ya presente, así que hacemos una operación Upsert.
                 try
                 {
                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                     messageTable.Execute(upsertOperation);
                 }
                 catch (Exception ex)
                 {
                     string err = "Error creating SendEmail row:  " + ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException;
                     }
                     Trace.TraceError(err);
                 }

                 // Crear el mensaje en cola.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);
             }

             Trace.TraceInformation("ProcessMessage end PK: "
                 + messageToProcess.PartitionKey);
         }

    El código obtiene primero la fila de la lista de correo de la tabla `mailinglist` para la lista de correo de destino. Esta fila tiene la dirección de correo electrónico "de" que se tiene que proporcionar al rol de trabajo B para enviar correos electrónicos.

             // Obtener información de lista de correo para obtener la dirección de correo electrónico del remitente.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                 return;
             }

    Posteriormente, realiza consultas en la tabla `mailinglist` para todas las filas del suscriptor para la lista de correo de destino.

             // Obtener direcciones de correo electrónico para esta lista de correo.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

    En el bucle que procesa los resultados de la consulta, el código comienza comprobando si se verifica la dirección de correo electrónico del suscriptor y si no hay ningún correo electrónico en la cola.

                 // Comprobar que la dirección de correo electrónico del suscriptor se ha verificado.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                     continue;
                 }

    A continuación, el código crea una fila `SendEmail` en la tabla `message`. Esta fila contiene la información que va a usar el rol de trabajo B para enviar un correo electrónico. La fila se crea con la propiedad `EmailSent` establecida en `false`.

                 // Crear una entidad SendEmail para este correo electrónico.              
                 var sendEmailRow = new SendEmail
                 {
                     PartitionKey = messageToProcess.PartitionKey,
                     RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                     EmailAddress = subscriber.EmailAddress,
                     EmailSent = false,
                     MessageRef = messageToProcess.MessageRef,
                     ScheduledDate = messageToProcess.ScheduledDate,
                     FromEmailAddress = mailingList.FromEmailAddress,
                     SubjectLine = messageToProcess.SubjectLine,
                     SubscriberGUID = subscriber.SubscriberGUID,
                     ListName = mailingList.ListName
                 };
                 try
                 {
                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                     messageTable.Execute(upsertOperation);
                 }
                 catch (Exception ex)
                 {
                     string err = "Error creating SendEmail row:  " + ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException;
                     }
                     Trace.TraceError(err);
                 }

    El código usa una operación "upsert" porque la fila podría ya existir si el rol de trabajo A se reinicia después de un error.

    La última tarea que se debe realizar para cada dirección de correo electrónico es crear el elemento de trabajo en la cola que activará el rol de trabajo B para enviar un correo electrónico. El elemento de trabajo en cola contiene la clave de partición y el valor de clave de fila `SendEmail` que se acaba de crear, más la marca de reinicio que se estableció anteriormente. La fila `SendEmail` contiene toda la información que el rol de trabajo B necesita para enviar un correo electrónico.

                 // Crear el mensaje en cola.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);

    El método `CheckAndUpdateStatusIfComplete` comprueba los mensajes que se encuentran en estado Processing para ver si se enviaron todos los correos electrónicos. Si no encuentran correos electrónicos sin enviar, actualiza el estado de la fila a `Completed` y la archiva.

         private void CheckAndArchiveIfComplete(Message messageToCheck)
         {
             // Obtener la lista de correos electrónicos que se enviarán para este mensaje: todas las filas SendEmail
             // para este mensaje.  
             string pkrkFilter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
             var query = new TableQuery<SendEmail>().Where(pkrkFilter);
             var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

             if (emailToBeSent != null)
             {
                 return;
             }

             // Todos los correos electrónicos se han enviado; copiar la fila de mensaje en la tabla de almacenamiento.

             // Insertar la fila de mensaje en la tabla messagearchive
             var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
             messageToCheck.Status = "Complete";
             var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
             messagearchiveTable.Execute(insertOrReplaceOperation);

             // Eliminar la fila message de la tabla message.
             var deleteOperation = TableOperation.Delete(messageToDelete);
             messageTable.Execute(deleteOperation);
         }

Configuración del almacenamientoConfiguración de la cadena de conexión de almacenamiento
----------------------------------------------------------------------------------------

Si todavía no configuró las credenciales de la cuenta de almacenamiento para el rol de trabajo A cuando lo hizo para el rol web, hágalo ahora.

1.  En el Explorador de soluciones, haga clic con el botón secundario en **WorkerRoleA** debajo de **Roles** en el proyecto en la nube **AzureEmailService** y, a continuación, seleccione **Propiedades**.

2.  Asegúrese de que la opción **All Configurations** está seleccionada en la lista desplegable **Configuración de servicio**.

3.  Seleccione la pestaña **Settings** y, a continuación, haga clic en **Agregar configuración**.

4.  Escriba *StorageConnectionString* en la columna **Name**.

5.  Seleccione **Cadena de conexión** en la lista desplegable **Tipo**.

6.  Haga clic en los puntos suspensivos (**...**) en el extremo derecho de la línea para crear una cadena de conexión nueva.

7.  En el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**, haga clic en **Your subscription**.

8.  Seleccione los valores de **Subscription** y **Account name** correctos y, a continuación, haga clic en **OK**.

9.  Establezca la cadena de conexión de diagnóstico. Puede usar la misma cuenta de almacenamiento para la cadena de conexión de diagnóstico, pero el procedimiento recomendado es usar una cuenta de almacenamiento diferente para obtener información de seguimiento (diagnóstico).

PruebasPruebas del rol de trabajo A
-----------------------------------

1.  Ejecute la aplicación presionando F5.

> [WACOM.NOTE] Con Visual Studio 2013 y el último SDK, es posible que obtenga un error de "referencia ambigua" para la referencia a `LogLevel`. Haga clic con el botón secundario en `LogLevel`, haga clic en Resolve y seleccione `Microsoft.WindowsAzure.Diagnostics.LogLevel`.

1.  Use las páginas web de administrador para crear una lista de correo y crear suscriptores para ella. Establezca la propiedad `Verified` en `true` para al menos uno de los suscriptores y configure la dirección de correo electrónico en una dirección donde pueda recibir correo.

    No se enviarán correos electrónicos hasta que implemente el rol de trabajo B, pero utilizará los mismos datos de prueba para probar el rol de trabajo B.

2.  Cree un mensaje que se va a enviar a la lista de correo que creó y establezca la fecha programada en hoy o una fecha en el pasado.

    ![Mensaje nuevo en estado pendiente](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png)

3.  En poco más de un minuto (debido al tiempo de suspensión de un minuto en el método Run), actualice la página web de mensajes y podrá ver el cambio de estado a Processing. (Es posible que lo vea cambiar primero a Queuing, pero es probable que pase de Queuing a Processing tan rápido que no le sea posible ver Queuing).

    ![Mensaje nuevo en estado de procesamiento](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png)

4.  Abra el Explorador de almacenamiento de Azure y seleccione su cuenta de almacenamiento de prueba.

5.  En el Explorador de almacenamiento de Azure, en **Storage Type** seleccione **Queues** y, a continuación, seleccione **azuremailqueue**.

    Verá un mensaje en cola por cada suscriptor verificado en la lista de correo electrónico de destino.

    ![Mensaje en cola en ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png)

6.  Haga doble clic en un mensaje en cola y, a continuación, en el cuadro de diálogo **Message Detail** seleccione la pestaña **Mensaje**.

    Verá el contenido del mensaje en cola: la clave de partición (la fecha 2012-12-14), la clave de fila (el valor MessageRef y la dirección de correo electrónico) y la marca de reinicio, delimitada por una coma.

    ![Contenido de mensaje en cola en ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png)

7.  Cierre el cuadro de diálogo **Message Detail**.

8.  En **Storage Type**, seleccione **Tables** y, a continuación, seleccione la tabla **Message**.

9.  Haga clic en **Query** para ver todas las filas de la tabla.

    Verá el mensaje que programó, con "Message" en la clave de fila, seguido de una fila por cada suscriptor verificado, con la dirección de correo electrónico en la clave de fila.

    ![Filas de la tabla Message en ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png)

10. Haga doble clic en una fila que tiene una dirección de correo electrónico en la clave de fila para ver el contenido de la fila `SendEmail` que creó el rol de trabajo A.

    ![Fila SendEmail en la tabla Message](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png)

Pasos siguientesPasos siguientes
--------------------------------

Ha creado y verificado un rol de trabajo A que crea los mensajes en cola y las filas de la tabla que el rol de trabajo B necesita para enviar correos electrónicos. En el [siguiente tutorial](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/), podrá crear y probar el rol de trabajo B.

Podrá encontrar vínculos a recursos adicionales acerca de cómo utilizar las tablas, colas y blobs de almacenamiento de Azure al final del [último tutorial de la serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
[Tutorial 5](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)

