<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure: 5 de 5.

Este es el quinto tutorial de una serie de cinco que muestra cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie][].

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Agregar un rol de trabajo a un proyecto de servicio en la nube.
-   Realizar el sondeo de una cola y procesar los elementos de trabajo de dicha cola.
-   Enviar correos electrónicos mediante SendGrid.
-   Administrar apagados planificados mediante la anulación del método `OnStop`.
-   Administrar apagados no planificados asegurándose de que no se envían correos electrónicos duplicados.

## Apartados de este tutorial

-   [Agregar el proyecto de rol de trabajo B a la solución][]
-   [Agregar una referencia al proyecto web][]
-   [Agregar el paquete NuGet de SendGrid al proyecto][]
-   [Agregar configuración del proyecto][]
-   [Agregar código que se ejecuta cuando el rol de trabajo se inicia][]
-   [Probar rol de trabajo B][]
-   [Pasos siguientes][]

## <a name="addworkerrole"></a><span class="short-header">Incorporación del rol de trabajo B</span>Incorporación del proyecto de rol de trabajo B a la solución

1.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de servicio en la nube y, a continuación, seleccione **Nuevo proyecto de rol de trabajador**.

    ![Menú Nuevo proyecto de rol de trabajador][]

2.  En el cuadro de diálogo **Agregar nuevo proyecto de rol**, seleccione **C#**, elija **Rol de trabajo**, asigne el nombre WorkerRoleB al proyecto y, a continuación, haga clic en **Agregar**.

    ![Cuadro de diálogo Nuevo proyecto de rol][]

## <a name="addreference"></a>Agregar una referencia al proyecto web

Necesita una referencia para el proyecto web porque es ahí donde se definen las clases de entidad. Usará las clases de entidad en el rol de trabajo B para leer y escribir datos en las tablas de Azure que usa la aplicación.

1.  Haga clic con el botón secundario en el proyecto WorkerRoleB y seleccione **Agregar referencia**.

2.  En **Administrador de referencias**, agregue una referencia al proyecto MvcWebRole.

    ![Agregar una referencia a MvcWebRole][]

## <a name="addsendgrid"></a>Agregar el paquete NuGet de SendGrid al proyecto

Para enviar correos electrónicos con SendGrid, necesita instalar el paquete NuGet de SendGrid.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto WorkerRoleB y, a continuación, seleccione **Manage NuGet Packages**.

    ![Administración de paquetes de NuGet][]

2.  En el cuadro de diálogo **Manage NuGet Packages**, seleccione la pestaña **Online**, escriba "sendgrid" en el cuadro de búsqueda y presione Entrar.

3.  Haga clic en **Install** en el paquete de **SendGrid**.

    ![Instalar el paquete de Sendgrid][]

4.  Cierre el cuadro de diálogo.

## <a name="addsettings"></a>Agregar configuración del proyecto

Al igual que el rol de trabajo A, el rol de trabajo B necesita las credenciales de la cuenta de almacenamiento para usar tablas, colas y blobs. Además, para enviar correos electrónicos, el rol de trabajo necesita tener credenciales para insertar las llamadas al servicio SendGrid. Por otra parte, para crear un vínculo de cancelación de suscripción a fin de incluirlo en los correos electrónicos enviados, el rol de trabajo necesita saber la URL de la aplicación. Estos valores están almacenados en la configuración del proyecto.

Para las credenciales de la cuenta de almacenamiento, el procedimiento es el mismo que el descrito en el [tercer tutorial][].

1.  En **Explorador de soluciones**, en **Roles** en el proyecto en la nube, haga clic con el botón secundario en **WorkerRoleB** y seleccione **Propiedades**.

2.  Seleccione la pestaña **Settings**.

3.  Asegúrese de que la opción **All Configurations** está seleccionada en la lista desplegable **Configuración de servicio**.

4.  Seleccione la pestaña **Settings** y, a continuación, haga clic en **Agregar configuración**.

5.  Escriba "StorageConnectionString" en la columna **Name**.

6.  Seleccione **Cadena de conexión** en la lista desplegable **Tipo**.

7.  Haga clic en el botón de los puntos suspensivos (**...**) en el extremo derecho de la línea para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

8.  En el cuadro de diálogo **Crear cadena de conexión de almacenamiento**, haga clic en el botón de radio **Emulador de almacenamiento de Azure** y, a continuación, haga clic en **Aceptar**.

A continuación, cree y configure las tres nuevas opciones que solo usa el rol de trabajo B.

1.  En la pestaña **Settings** de la ventana **Propiedades**, haga clic en **Agregar configuración** y, a continuación, agregue las tres opciones de configuración nuevas del tipo **Cadena**:

    -   **Name**: SendGridUserName, **Value**: el nombre de usuario de SendGrid establecido en el [segundo tutorial][].

    -   **Name**: SendGridPassword, **Value**: la contraseña de SendGrid.

    -   **Name**: AzureMailServiceURL, **Value**: la URL de base que la aplicación tendrá al implementarla; por ejemplo: <http://sampleurl.cloudapp.net>.

    ![Nueva configuración del proyecto WorkerRoleB][]

## <a name="addcode"></a>Agregar código que se ejecuta cuando el rol de trabajo se inicia

1.  En el proyecto WorkerRoleB, elimine WorkerRole.cs.

2.  En el proyecto WorkerRoleB, agregue el archivo WorkerRoleB.cs del proyecto descargado.

3.  Compile la solución.

    Si obtiene un error de compilación, es posible que haya terminado con varias versiones del paquete NuGet de Almacenamiento de Azure. En ese caso, para resolver el problema, vaya al Administrador de paquetes NuGet de la solución, seleccione Paquetes instalados y desplácese hacia abajo para ver si hay dos instancias del paquete de Almacenamiento de Azure. Seleccione la entrada de Almacenamiento de Azure 4.0.0 y elimínela de los proyectos en que está instalada. A continuación, seleccione la entrada de Almacenamiento de Azure 3.0.x e instálela en los proyectos en que falta. Cierre y reinicie Visual Studio y, a continuación, vuelva a compilar la solución.

4.  Asegúrese de que el proyecto del servicio en la nube sigue siendo el proyecto de inicio de la solución.

### Método OnStart

Como ya ha observado en el rol de trabajo A, el método `OnStart` inicializa las clases de contexto que necesita a fin de trabajar con las entidades de almacenamiento de Azure. Además, se cerciora de que existan todas las tablas, las colas y los contenedores de blobs que va a usar en el método `Run`.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

La diferencia con respecto al rol de trabajo A es la incorporación del contenedor de blobs y la cola de suscripción entre los recursos que habrá que crear si no existen aún. Usará el contenedor de blobs para obtener los archivos que contienen HTML y texto sin formato para el cuerpo del correo electrónico. La cola de suscripción se usa para enviar correos electrónicos de confirmación de suscripción.

### Método Run

El método `Run` procesa los elementos de trabajo de dos colas: la cola usada para los mensajes enviados a las listas de correo electrónico (elementos de trabajo creados por el rol de trabajo A) y la cola usada para los correos electrónicos de confirmación de suscripción (elementos de trabajo creados por el método de la API subscribe en el proyecto MvcWebRole).

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Este código se ejecuta en un bucle infinito hasta que el rol de trabajo se apaga. Si un elemento de trabajo se encuentra en la cola principal, el código lo procesa y, a continuación, comprueba la cola de suscripción.

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

Si no hay ningún elemento en espera en la cola, el código se suspende durante 60 segundos antes de continuar con el bucle.

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

El propósito del tiempo de suspensión es minimizar los costes de las transacciones de almacenamiento de Azure, como se ha explicado en el [tutorial anterior][].

Si se obtiene un elemento de la cola con el método [GetMessage][], dicho elemento se hace invisible durante 30 segundos para todos los demás roles de trabajo y web que tienen acceso a la cola. Esto es lo que garantiza que solo una instancia de rol de trabajo se ocupe del procesamiento de un mensaje de cola concreto. Puede definir de forma explícita el tiempo de la *concesión exclusiva* (el tiempo durante el cual el elemento de la cola es invisible) transfiriendo un parámetro de [tiempo de espera de visibilidad][] al método `GetMessage`. Si el rol de trabajo tarda más de 30 segundos en procesar un mensaje de la cola, debe aumentar el tiempo de concesión exclusiva para prevenir que otras instancias de rol procesen el mismo mensaje.

Por otra parte, no deseará definir el tiempo de concesión exclusiva con un valor excesivamente alto. Por ejemplo, si el tiempo de concesión exclusiva se define en 48 horas y el rol de trabajo se apaga inesperadamente después de quitar un mensaje de la cola, los demás roles de trabajo no podrían procesar el mensaje durante 48 horas. El tiempo de concesión exclusiva máximo es de 7 días.

El método [GetMessages][] (observe la "s" al final del nombre) se puede usar para obtener hasta 32 mensajes de la cola en una llamada. Cada acceso a la cola supone un coste de transacción bajo, que será el mismo independientemente de que se devuelvan 32 mensajes o ninguno. El siguiente código captura hasta 32 mensajes en una llamada y, a continuación, los procesa.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

Si usa `GetMessages` para eliminar varios mensajes, asegúrese de que el tiempo de espera de visibilidad da suficiente tiempo a la aplicación para procesar todos los mensajes. Cuando el tiempo de espera de visibilidad caduca, otras instancias de rol pueden tener acceso al mensaje y, cuando lo hacen, la primera instancia no podrá eliminar el mensaje cuando este termine de procesar el elemento de trabajo.

### Método ProcessQueueMessage

El método `Run` llama a `ProcessQueueMessage` cuando encuentra un elemento de trabajo en la cola principal:

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

Los mensajes dudosos son aquellos que provocan que la aplicación lance una excepción cuando se procesan. Si un mensaje se ha obtenido de la cola más de cinco veces, asumimos que no se puede procesar y lo quitamos de la cola, a fin de que no continuemos tratando de procesarlo. Las aplicaciones de producción deben considerar la opción de mover el mensaje dudoso a una cola de "mensajes fallidos" para analizarlo, en lugar de eliminar el mensaje.

El código analiza el mensaje de la cola en la clave de partición y en la clave de fila necesarias para recuperar la fila SendEmail y una marca de reinicio.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

Si el procesamiento de este mensaje se ha reiniciado después de un apagado inesperado, el código comprueba la tabla `messagearchive` para determinar si este correo electrónico se ha enviado ya. En este caso, el código elimina la fila `SendEmail`, en caso de que exista, y elimina el mensaje de la cola.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

A continuación, obtenemos la fila `SendEmail` de la tabla `message`. Esta fila tiene toda la información necesaria para enviar el correo electrónico, salvo los blobs que contienen HTML y texto sin formato para el cuerpo del correo electrónico.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

A continuación, el código envía el correo electrónico y archiva la fila `SendEmail`.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

En una transacción no se puede mover la fila a la tabla messagearchive porque esto afecta a varias tablas.

Por último, si todo lo demás se completa satisfactoriamente, el mensaje de la cola se elimina.

            sendEmailQueue.DeleteMessage(msg);

### Método SendEmailToList

El método `SendEmailToList` realiza el trabajo real de envío del correo electrónico mediante SendGrid. Si desea usar un servicio diferente a SendGrid, lo único que tiene que hacer es cambiar el código en este método.

**Nota:** si tiene credenciales que no son válidas en la configuración del proyecto, se producirá un error al llamar a SendGrid, pero la aplicación no obtendrá ninguna indicación del error. Si usa SendGrid en una aplicación de producción, considere la opción de configurar credenciales independientes para la API web a fin de evitar que se produzcan errores silenciosos cuando un administrador cambia la contraseña de la cuenta de usuario de SendGrid. Para obtener más información, consulte [SendGrid MultiAuth - Multiple Account Credentials][]. Puede configurar las credenciales en [][]<https://sendgrid.com/credentials></a>.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

En el método `GetBlobText`, el código obtiene el tamaño del blob y, a continuación, usa ese valor para inicializar el objeto `MemoryStream` por motivos de rendimiento. Si no proporciona el tamaño, lo que `MemoryStream` hace es asignar 256 bytes; entonces, si la descarga excede dicho tamaño, asigna 512 bytes más, y así sucesivamente, duplicando en cada ocasión la cantidad asignada. Para un blob grande este proceso resultaría ineficaz en comparación con la asignación de la cantidad correcta al inicio de la descarga.

### Método ProcessSubscribeQueueMessage

El método `Run` llama a `ProcessSubscribeQueueMessage` cuando encuentra un elemento de trabajo en la cola de suscripción:

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

Este método ejecuta las siguientes tareas:

-   Si el mensaje es "dudoso", lo registra y lo elimina.
-   Obtiene el GUID del suscriptor del mensaje de la cola.
-   Usa el GUID para obtener información del suscriptor de la tabla MailingList.
-   Envía un correo electrónico de confirmación al nuevo suscriptor.
-   Elimina el mensaje de la cola.

Como sucede con los correos electrónicos enviados a listas, el envío real del correo electrónico se realiza en un método independiente, facilitándole el cambio a un servicio de correo electrónico distinto si así lo desea.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>Probar rol de trabajo B

1.  Ejecute la aplicación presionando F5.

2.  Vaya a la página **Messages** para ver el mensaje creado para probar el rol de trabajo A. Transcurrido un minuto más o menos, actualice la página web y observará que la fila ha desaparecido de la lista porque se ha archivado.

3.  Compruebe la bandeja de entrada de correo electrónico donde espera recibir el mensaje. Tenga en cuenta que pueden producirse retrasos en el envío de correos electrónicos mediante SendGrid o en la entrega al cliente de correo electrónico, por lo que es posible que tenga que esperar un poco hasta recibir el correo electrónico. Es necesario que también compruebe la carpeta de correo no deseado.

## <a name="nextsteps"></a>Pasos siguientes

Ya ha creado la aplicación Servicio de correo electrónico de Azure desde cero y lo que consigue es el mismo resultado que el proyecto completado que ha descargado. Para implementarla y probarla en la nube y pasarla a producción, puede usar los mismos procedimientos descritos en el [segundo tutorial][].

Para ver una aplicación de ejemplo que muestra cómo usar LINQ en las consultas del servicio de tabla de Almacenamiento de Azure, consulte [PhluffyFotos][].

Para obtener más información sobre el almacenamiento de Azure, consulte el siguiente recurso:

-   [Essential Knowledge for Windows Azure Storage][] (blog de Bruno Terkaly)

Para obtener más información acerca del servicio de tablas de Azure, consulte los siguientes recursos:

-   [Essential Knowledge for Azure Table Storage][] (blog de Bruno Terkaly)
-   [How to get the most out of Windows Azure Tables][] (blog del equipo de almacenamiento de Azure)
-   [Uso del servicio de almacenamiento de tablas en .NET][]
-   [Windows Azure Storage Client Library 2.0 Tables Deep Dive][] (blog del equipo de almacenamiento de Azure)
-   [Casos reales: Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure][]

Para obtener más información acerca del servicio de colas de Azure y las colas del Bus de servicio de Azure, consulte los siguientes recursos:

-   [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure)][]
-   [Colas de Windows Azure y Colas de Service Bus de Windows Azure: comparación y diferencias][]
-   [Uso del almacenamiento en cola en .NET][]

Para obtener más información acerca del servicio BLOB de Azure, consulte los siguientes recursos:

-   [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure)][]
-   [Uso del servicio de almacenamiento de blobs de Azure en .NET][]

Para obtener más información acerca de la autoescala de roles del Servicio en la nube de Azure, consulte los siguientes recursos:

-   [Uso del bloque de autoescala de la aplicación][]
-   [Autoscaling and Azure][]
-   [Building Elastic, Autoscalable Solutions with Azure][] (vídeo del canal 9 de MSDN)

## <a name="Acknowledgments"></a><span class="short-header">Reconocimientos</span>Reconocimientos

Estos tutoriales y la aplicación de ejemplo los han elaborado [Rick Anderson][] y Tom Dykstra. Nos gustaría mostrar nuestro agradecimiento a las siguientes personas por su colaboración:

-   Barry Dorrans (Twitter [@blowdart][])
-   [Cory Fowler][] (Twitter [@SyntaxC4][] )
-   [Joe Giardino][]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][] (Twitter: [@coolcsh][])
-   [Brian Swan][]
-   [Daniel Wang][]
-   A los miembros del Consejo consultivo de desarrolladores que han dado sus comentarios:
 -   Damir Arh
 -   Jean-Luc Boucho
 -   Carlos dos Santos
 -   Mike Douglas
 -   Robert Fite
 -   Gianni Rosa Gallina
 -   Fabien Lavocat
 -   Karl Ots
 -   Carlos-Alejandro Perez
 -   Sunao Tomita
 -   Perez Jones Tsisah
 -   Michiel van Otegem

  [primer tutorial de la serie]: /es-es/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Agregar el proyecto de rol de trabajo B a la solución]: #addworkerrole
  [Agregar una referencia al proyecto web]: #addreference
  [Agregar el paquete NuGet de SendGrid al proyecto]: #addsendgrid
  [Agregar configuración del proyecto]: #addsettings
  [Agregar código que se ejecuta cuando el rol de trabajo se inicia]: #addcode
  [Probar rol de trabajo B]: #testing
  [Pasos siguientes]: #nextsteps
  [Menú Nuevo proyecto de rol de trabajador]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [Cuadro de diálogo Nuevo proyecto de rol]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Agregar una referencia a MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [Administración de paquetes de NuGet]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Instalar el paquete de Sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [tercer tutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [segundo tutorial]: /es-es/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Nueva configuración del proyecto WorkerRoleB]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [tutorial anterior]: /es-es/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/es-es/library/windowsazure/ee741827.aspx
  [tiempo de espera de visibilidad]: http://msdn.microsoft.com/es-es/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - Multiple Account Credentials]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Essential Knowledge for Windows Azure Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Essential Knowledge for Azure Table Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [How to get the most out of Windows Azure Tables]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Uso del servicio de almacenamiento de tablas en .NET]: http://www.windowsazure.com/es-es/develop/net/how-to-guides/table-services/
  [Windows Azure Storage Client Library 2.0 Tables Deep Dive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Casos reales: Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh508997.aspx
  [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Colas de Windows Azure y Colas de Service Bus de Windows Azure: comparación y diferencias]: http://msdn.microsoft.com/es-es/library/windowsazure/hh767287.aspx
  [Uso del almacenamiento en cola en .NET]: /es-es/develop/net/how-to-guides/queue-service/
  [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Uso del servicio de almacenamiento de blobs de Azure en .NET]: /es-es/develop/net/how-to-guides/blob-storage/
  [Uso del bloque de autoescala de la aplicación]: /es-es/develop/net/how-to-guides/autoscaling/
  [Autoscaling and Azure]: http://msdn.microsoft.com/es-es/library/hh680945(v=PandP.50).aspx
  [Building Elastic, Autoscalable Solutions with Azure]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
