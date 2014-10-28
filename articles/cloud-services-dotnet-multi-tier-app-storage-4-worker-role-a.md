<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Creación del rol de trabajo A (programador de correo electrónico) para la aplicación Servicio de correo electrónico de Azure: 4 de 5.

Este es el cuarto tutorial de una serie de cinco que muestran cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie][].

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Consultar y actualizar las tablas de almacenamiento de Azure.
-   Agregar elementos de trabajo a una cola para procesar mediante otro rol de trabajo.
-   Administrar apagados planificados mediante la anulación del método `OnStop`.
-   Administrar apagados no planificados asegurándose de que no faltan correos electrónicos y no se enviaron correos electrónicos duplicados.
-   Probar un rol de trabajo que usa las tablas de Almacenamiento de Azure mediante el Explorador de servidores.

Ya creó el proyecto de rol de trabajo A cuando creó el proyecto de servicio en la nube. Por lo tanto, todo lo que tiene que hacer es programar el rol de trabajo.

## Apartados de este tutorial

-   [Agregar una referencia al proyecto web][]
-   [Agregar el modelo SendEmail][]
-   [Agregar código que se ejecuta cuando el rol de trabajo se inicia][]
-   [Probar rol de trabajo A][]
-   [Pasos siguientes][]

## <a name="addref"></a><span class="short-header">Incorporación de una referencia al proyecto</span>Incorporación de una referencia al proyecto web

Necesita una referencia para el proyecto web porque es ahí donde se definen las clases de entidad. Usará las mismas clases de entidad en el rol de trabajo B para leer y escribir datos en las tablas de Azure que usa la aplicación.

**Nota:** en una aplicación de producción no establece una referencia para un proyecto web a partir de un proyecto de rol de trabajo, porque esto tiene como resultado una referencia a varios ensamblados dependientes que no desea o no necesita en el rol de trabajo. Por lo general, mantiene clases de modelo compartidos en un proyecto de biblioteca de clases y tanto los proyectos de rol web como de trabajo harían referencia al proyecto de biblioteca de clase. Para que la estructura de la solución siga siendo sencilla, las clases de modelos se almacenan en el proyecto web de este tutorial.

1.  Haga clic con el botón secundario en el proyecto WorkerRoleA y seleccione **Agregar** y **Referencia**.

2.  En **Administrador de referencias**, agregue una referencia al proyecto MvcWebRole y haga clic en **Aceptar**.

    ![Agregar una referencia a MvcWebRole][]

## <a name="addmodel"></a>Agregar el modelo SendEmail

El rol de trabajo A crea las filas `SendEmail` en la tabla `Message` y el rol de trabajo B las lee para obtener la información que necesita para enviar correos electrónicos. La siguiente imagen muestra un subconjunto de propiedades para dos filas `Message` y tres filas `SendEmail` en la tabla `Message`.

![tabla message con sendmail][]

Estas filas de la tabla `Message` sirven a varios propósitos:

-   Proporcionan toda la información que el rol de trabajo B necesita para enviar un solo correo electrónico.
-   Realizan un seguimiento para confirmar si se envió un correo electrónico y así evitar que se envíen duplicados en caso de que un rol de trabajo se reinicie después de un error.
-   Permiten que el rol de trabajo A determine cuándo se enviaron todos los correos electrónicos para un mensaje, de modo que se pueda marcar como `Complete`.

Para leer y escribir en las filas `SendEmail`, es necesaria una clase de modelo. Debido a que debe estar accesible para el rol de trabajo A y el rol de trabajo B, y debido a que todas las demás clases de modelo se definen en el proyecto web, es conveniente definir también ésta en el proyecto web.

1.  En la carpeta *Modelos* del proyecto web, agregue el archivo *SendEmail.cs* del proyecto descargado.

Este código es similar a las otras clases de modelo, salvo que no se incluye ningún atributo DataAnnotations porque no hay una interfaz de usuario asociada a este modelo; no se usa en un controlador de MVC.

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

## <a name="addcode"></a><span class="short-header">Incorporación de código de rol de trabajo</span>Incorporación de código que se ejecuta cuando se inicia el rol de trabajo

En el proyecto WorkerRoleA, la plantilla del proyecto creó *WorkerRole.cs* con el código siguiente:

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");

                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }

            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;

                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

                return base.OnStart();
            }
        }

Este es el código de la plantilla predeterminada para el rol de trabajo. Existe un método `OnStart` en el que se puede poner código de inicialización que solo se ejecuta cuando se inicia una instancia del rol de trabajo y un método `Run` al que se llama después de finalizar el método `OnStart`. Debe reemplazar este código por su propio código de inicialización y ejecución.

1.  Elimine *WorkerRole.cs* en el proyecto WorkerRoleA y agregue el archivo *WorkerRoleA.cs* del proyecto descargado.

### Método OnStart

El método `OnStart` inicializa los objetos de contexto que necesita para trabajar con las entidades de Almacenamiento de Azure. Además, se cerciora de que existan todas las tablas, las colas y los contenedores de blobs que va a usar en el método `Run`. El código que realiza estas tareas es similar a lo que vio anteriormente en los constructores del controlador MVC. Podrá configurar la cadena de conexión que utiliza este método posteriormente.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 

            return base.OnStart();
        }

Es posible que haya visto documentación anterior sobre el trabajo con el almacenamiento de Azure que muestra el código de inicialización en un bucle que revisa la presencia de errores de transporte. Esta acción ya no es necesaria porque la API tiene ahora un mecanismo de reintento integrado que absorbe los errores de red transitorios hasta en 3 intentos adicionales.

El método `ConfigureDiagnostics` al que el método `OnStart` llama configura el seguimiento para que pueda ver el resultado desde los métodos `Trace.Information` y `Trace.Error`. Este método se explica en el [segundo tutorial][].

El valor [ServicePointManager.DefaultConnectionLimit][] especifica el número máximo de conexiones TCP simultáneas que se pueden abrir en .NET. En la documentación de la clase [ServicePointManager][] se explica que cada host único al que se conecta la instancia del rol de trabajo es una conexión independiente. Por ejemplo, este rol de trabajo tendría tres conexiones simultáneas: una para tablas, una para blobs y una para colas. Para algunas conexiones, como SQL Server, el software cliente realiza la agrupación de conexiones, lo que puede reducir el número de conexiones simultáneas que administra `ServicePointManager`. El mejor número para `DefaultConnectionLimit` depende, en parte, del servicio de back-end al que se conecta. Para algunos servicios puede ser adecuado tener 500 conexiones abiertas, mientras que otros servicios se pueden ver superados con solo 5 conexiones. El límite de 12 conexiones para cada procesador es una directriz general que funcionará muchos escenarios.

### Método OnStop

El método `OnStop` establece la variable global `onStopCalled` en true y posteriormente espera que el método `Run` establezca la variable global `returnedFromRunMethod` en true, lo que la señala como lista para realizar un apagado limpio.

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

Azure instala de manera periódica actualizaciones del sistema operativo para asegurar que la plataforma se mantenga segura, confiable y tenga un buen rendimiento. Por lo general, estas actualizaciones requieren que las máquinas que hospedan el servicio en la nube se cierren y se reinicien. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades][].

### Método Run

El método `Run` realiza dos funciones:

-   Examina la tabla `message` en busca de mensajes programados para su envío hoy o antes, para lo cual todavía no se han creado elementos de trabajo en cola.

-   Examina la tabla `message` en busca de mensajes que tienen un estado que indica que todos los elementos de trabajo de la cola se crearon, pero que todavía no se han enviado todos los correos electrónicos. Si encuentra uno, examina las filas `SendEmail` de ese mensaje para ver si se enviaron todos los correos electrónicos y, si así fuera, actualiza el estado a `Completed` y archiva la fila `message`.

El método comprueba también la variable global `onStopCalled`. Cuando la variable es `true`, el método deja de extraer nuevos elementos de trabajo para procesar y devuelve cuando se completan las tareas que ya se iniciaron.

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
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

                    // Sleep for one minute to minimize query costs. 
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
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Tenga en cuenta que todo el trabajo se realiza en un bucle infinito en un bloque `while` y que todo el código del bloque `while` se encapsula en un bloque `try`-`catch` para evitar una excepción no controlada. Si se produce una excepción no controlada, Azure generará el evento [UnhandledException][], el proceso de trabajo se termina y el rol se deja sin conexión. El rol de trabajo lo reinicia Azure, pero tarda varios minutos. El bloque `try` llama a `TraceError` para registrar el error y, a continuación, se suspende durante 60 segundos de modo que si el error es persistente, el mensaje de error no se repite tantas veces. En una aplicación de producción puede enviar un correo electrónico a un administrador en el bloque `try`.

El método `Run` procesa una consulta para las filas `message` de la tabla `message` que programaron una fecha antes de mañana:

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**Nota:** Una de las ventajas de mover las filas de mensaje a la tabla `messagearchive` después de que se procesan, es que esta consulta solo necesita especificar `PartitionKey` y `RowKey` como criterios de búsqueda. Si no archiváramos las filas procesadas, la consulta también tendría que especificar un campo sin clave (`Status`) y tendría que buscar en más filas. El tamaño de la tabla aumentaría y la consulta tardaría más tiempo, lo que podría iniciar la obtención de tokens de continuación.

Si un mensaje está en estado `Pending`, el procesamiento todavía no comienza; si está en estado `Queuing`, el procesamiento comenzó antes pero se interrumpió antes de que se crearan todos los mensajes en cola. En ese caso, debe realizarse una comprobación adicional en el rol de trabajo B cuando envía cada correo electrónico para asegurarse de que el correo electrónico todavía no se ha enviado. Ese es el objetivo de la variable `restartFlag`.

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

A continuación, el código define las filas `message`message con el estado `Pending` en `Queuing`. Luego, para aquellas filas, más cualquiera que ya tenga el estado `Queuing`, llama al método `ProcessMessage` para crear los elementos de trabajo en cola y así enviar correos electrónicos para el mensaje.

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

Después de procesar un mensaje en estado `Queuing`, el código establece el estado de la fila `Message` en `Processing`. Las filas de la tabla `message` que no tienen el estado `Pending` o `Queuing` ya tienen el estado `Processing` y, para esas filas, el código llama a un método que comprueba si se enviaron todos los correos electrónicos del mensaje. Si se enviaron todos los correos electrónicos, la fila `message` se archiva.

Después de procesar todos los registros recuperados por la consulta, el código se suspende durante un minuto.

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

Existe un cobro mínimo por cada consulta de almacenamiento de Azure, incluso si no devuelve ningún dato, por lo que un nuevo examen continuo se sumaría innecesariamente a sus gastos de Azure. Mientras se escribe este tutorial, el costo es de $0,10 por millón de transacciones (una consulta se considera como una transacción), por lo que el tiempo de suspensión podría ser mucho menos de un minuto y el coste del examen de las tablas en busca de los mensajes que se van a enviar todavía sería mínimo. Para obtener más información sobre el precio, consulte el [primer tutorial][primer tutorial de la serie].

**Nota sobre los subprocesos y la utilización óptima de la CPU:** Hay dos tareas en el método `Run` (poner correos electrónicos en cola y comprobar los mensajes completados) que se ejecutan de manera secuencial en un solo subproceso. Una máquina virtual (VM) pequeña tiene 1,75 GB de RAM y solo una CPU, por lo que probablemente es bueno ejecutar estas tareas de manera secuencial con solo un subproceso. Supongamos que su aplicación necesita más memoria que la máquina virtual pequeña proporcionada para funcionar eficazmente. Una máquina virtual mediana proporciona 3,5 GB de RAM y 2 CPU, pero esta aplicación solo usaría una CPU porque tiene un solo subproceso. Para aprovechar todas las CPU, necesita crear un subproceso de trabajo para cada CPU. Aun así, un subproceso no utiliza completamente una sola CPU. Cuando un subproceso realiza llamadas de red o de E/S, debe esperar a que la llamada de red o de E/S se complete y, mientras espera, no realiza ninguna tarea de utilidad. Si se implementa el método `Run` con dos subprocesos, cuando un subproceso espera que se complete una operación de red o de E/S, el otro subproceso podría realizar algún trabajo útil.

### Método ProcessMessage

El método `ProcessMessage` obtiene todas las direcciones de correo electrónico de la lista de correo electrónico de destino y crea un elemento de trabajo en cola para cada dirección de correo electrónico. Mientras crea los elementos de trabajo en cola, crea también filas `SendEmail` en la tabla `Message`. Estas filas proporcionan al rol de trabajo B la información que necesita para enviar correos electrónicos e incluye una propiedad `EmailSent` que realiza un seguimiento de cada correo electrónico que se ha enviado.

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

                // Create a SendEmail entity for this email.              
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

                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
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

                // Create the queue message.
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

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

Posteriormente, realiza consultas en la tabla `mailinglist`para todas las filas del suscriptor para la lista de correo de destino.

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

En el bucle que procesa los resultados de la consulta, el código comienza comprobando si se verifica la dirección de correo electrónico del suscriptor y si no hay ningún correo electrónico en la cola.

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

A continuación, el código crea una fila `SendEmail` en la tabla `message`. Esta fila contiene la información que va a usar el rol de trabajo B para enviar un correo electrónico. La fila se crea con la propiedad `EmailSent` establecida en `false`.

                // Create a SendEmail entity for this email.              
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

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### Método CheckAndUpdateStatusIfComplete

El método `CheckAndUpdateStatusIfComplete` comprueba los mensajes que se encuentran en estado Processing para ver si se enviaron todos los correos electrónicos. Si no encuentran correos electrónicos sin enviar, actualiza el estado de la fila a `Completed` y la archiva.

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
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

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>Probar rol de trabajo A

1.  Ejecute la aplicación presionando F5.

2.  Use las páginas web de administrador para crear una lista de correo y crear suscriptores para ella. Establezca la propiedad `Verified` en `true` para al menos uno de los suscriptores y configure la dirección de correo electrónico en una dirección donde pueda recibir correo.

    No se enviarán correos electrónicos hasta que implemente el rol de trabajo B, pero utilizará los mismos datos de prueba para probar el rol de trabajo B.

3.  Cree un mensaje que se va a enviar a la lista de correo que creó y establezca la fecha programada en hoy o una fecha en el pasado.

    ![Mensaje nuevo en estado pendiente][]

4.  En poco más de un minuto (debido al tiempo de suspensión de un minuto en el método Run), actualice la página web de mensajes y podrá ver el cambio de estado a Processing. (Es posible que lo vea cambiar primero a Queuing, pero es probable que pase de Queuing a Processing tan rápido que no le sea posible ver Queuing).

    ![Mensaje nuevo en estado de procesamiento][]

5.  Abra el **Explorador de servidores** y abra el nodo de Almacenamiento de desarrollo.

6.  Expanda **Colas**, haga clic con el botón secundario en **AzureMailQueue** y luego a haga clic en **Ver cola**.

    Verá un mensaje en cola por cada suscriptor verificado en la lista de correo electrónico de destino.

    ![Mensaje en cola en ASE][]

7.  Haga doble clic en un mensaje en cola.

    Verá el contenido del mensaje en cola: la clave de partición (la fecha), la clave de fila (el valor MessageRef y la dirección de correo electrónico) y la marca de reinicio, delimitada por una coma.

    ![Contenido de mensaje en cola en ASE][]

8.  Cierre el cuadro de diálogo **Ver mensaje**.

9.  Expanda el nodo **Tablas**, haga clic con el botón secundario en la tabla **Message** y, a continuación, haga clic en **Ver tabla**.

    Verá el mensaje que programó, con "message" en la clave de fila, seguido de una fila por cada suscriptor verificado, con la dirección de correo electrónico en la clave de fila.

10. Haga doble clic en una fila que tiene «mensaje" en la clave de fila para ver el contenido de la fila que creó el rol web.

    ![Fila de mensaje][]

11. Haga doble clic en una fila que tiene una dirección de correo electrónico en la clave de fila para ver el contenido de la fila `SendEmail` que creó el rol de trabajo A.

    ![Fila SendEmail en la tabla Message][]

## <a name="nextsteps"></a>Pasos siguientes

Ha creado y verificado un rol de trabajo A que crea los mensajes en cola y las filas de la tabla que el rol de trabajo B necesita para enviar correos electrónicos. En el [siguiente tutorial][], podrá crear y probar el rol de trabajo B.

Podrá encontrar vínculos a recursos adicionales para trabajar con tablas, colas y blobs de Almacenamiento de Azure en el [último tutorial de la serie][siguiente tutorial].

<div><a href="/es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Tutorial 5</a></div>

  [primer tutorial de la serie]: /es-es/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Agregar una referencia al proyecto web]: #addref
  [Agregar el modelo SendEmail]: #addmodel
  [Agregar código que se ejecuta cuando el rol de trabajo se inicia]: #addcode
  [Probar rol de trabajo A]: #testing
  [Pasos siguientes]: #nextsteps
  [Agregar una referencia a MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [tabla message con sendmail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [segundo tutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/es-es/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/es-es/library/system.net.servicepointmanager.aspx
  [Role Instance Restarts Due to OS Upgrades]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [UnhandledException]: http://msdn.microsoft.com/es-es/library/system.appdomain.unhandledexception.aspx
  [Mensaje nuevo en estado pendiente]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [Mensaje nuevo en estado de procesamiento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [Mensaje en cola en ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [Contenido de mensaje en cola en ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [Fila de mensaje]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [Fila SendEmail en la tabla Message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [siguiente tutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
