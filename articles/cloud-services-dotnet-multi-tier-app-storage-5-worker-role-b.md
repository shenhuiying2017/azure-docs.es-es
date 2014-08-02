<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Step 5: Worker Role B" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 5: Worker role B" metaKeywords="Azure tutorial, adding working role cloud service, C# worker role" description="The fifth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role B (email sender) for the Azure Email Service application - 5 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure: 5 de 5.
===================================================================================================================================

Este es el quinto tutorial de una serie de cinco que muestra cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Agregar un rol de trabajo a un proyecto de servicio en la nube.
-   Realizar el sondeo de una cola y procesar los elementos de trabajo de dicha cola.
-   Enviar correos electrónicos mediante SendGrid.
-   Administrar apagados planificados mediante la anulación del método `OnStop`.
-   Administrar apagados no planificados asegurándose de que no se envían correos electrónicos duplicados.

Incorporación del rol de trabajo BIncorporación del proyecto de rol de trabajo B a la solución
----------------------------------------------------------------------------------------------

1.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de servicio en la nube y, a continuación, seleccione **Nuevo proyecto de rol de trabajador**.

    ![Menú Nuevo proyecto de rol de trabajador](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png)

2.  En el cuadro de diálogo **Agregar nuevo proyecto de rol**, seleccione **C\#**, elija **Rol de trabajo**, asigne el nombre WorkerRoleB al proyecto y, a continuación, haga clic en **Agregar**.

    ![Cuadro de diálogo Nuevo proyecto de rol](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png)

Incorporación de una referenciaIncorporación de una referencia al proyecto web
------------------------------------------------------------------------------

Necesita una referencia para el proyecto web porque es ahí donde se definen las clases de entidad. Usará las clases de entidad en el rol de trabajo B para leer y escribir datos en las tablas de Azure que usa la aplicación.

1.  Haga clic con el botón secundario en el proyecto WorkerRoleB y seleccione **Agregar referencia**.

    ![Agregar una referencia al proyecto de WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png)

2.  En **Administrador de referencias**, agregue una referencia al proyecto MvcWebRole (o al proyecto de aplicación web si está ejecutando la interfaz de usuario web en un sitio web de Azure).

    ![Agregar una referencia a MvcWebRole](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png)

Incorporación del paquete SCL 2.0Incorporación al proyecto del paquete NuGet de la biblioteca del cliente de almacenamiento 2.0
-------------------------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Puede omitir esta sección con Visual Studio 2013, ya que el paquete de almacenamiento de Azure actual está instalado en el nuevo proyecto de rol de trabajo.

Cuando agregó el proyecto, no obtuvo automáticamente la versión actualizada del paquete NuGet de la biblioteca del cliente de almacenamiento. En su lugar, obtuvo la versión 1.7 anterior del paquete, ya que es la que está incluida en la plantilla de proyecto. Ahora la solución tiene dos versiones del paquete NuGet de almacenamiento de Azure: la versión 2.0 en los proyectos MvcWebRole y WorkerRoleA y la versión 1.7 en el proyecto WorkerRoleB. Necesita desinstalar la versión 1.7 e instalar la 2.0 en el proyecto WorkerRoleB.

1.  En el menú **Tools**, seleccione **Library Package Manager** y, a continuación **Manage NuGet Packages for Solution**.

2.  Con la opción **Installed Packages** seleccionada en al panel izquierdo, desplácese hacia abajo hasta localizar el paquete de almacenamiento de Azure.

    El paquete aparecerá dos veces, una para la versión 1.7 y otra para la versión 2.0.

3.  Seleccione la versión 1.7 del paquete y haga clic en **Manage**.

    Las casillas de MvcWebRole y WorkerRoleB están desactivadas, pero la casilla de WorkerRoleB está seleccionada.

4.  Desactive la casilla de WorkerRoleB y, a continuación, haga clic en **OK**.

5.  Cuando se le pregunte si desea desinstalar los paquetes dependientes, haga clic en **No**.

    Cuando termine la desinstalación, en el cuadro de diálogo NuGet solo aparecerá la versión 2.0 del paquete.

6.  Haga clic en **Manage** para la versión 2.0 del paquete.

    Las casillas de MvcWebRole y WorkerRoleA están activadas, pero la casilla de WorkerRoleA está desactivada.

7.  Desactive la casilla de WorkerRoleA y, a continuación, haga clic en **OK**.

Incorporación de referencias a SCL 1.7Incorporación de una referencia a un ensamblado de SCL 1.7
------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Omita esta sección si ha instalado el último SDK y utiliza Visual Studio 2013.

La versión 2.0 de la biblioteca del cliente de almacenamiento (SCL) no tiene todo lo necesario para realizar un diagnóstico, por lo que tendrá que agregar una referencia a uno de los ensamblados 1.7, como ha hecho anteriormente para los otros dos proyectos.

1.  Haga clic con el botón secundario en el proyecto WorkerRoleB y seleccione **Agregar referencia**.

2.  Haga clic en el botón **Examinar...** en la parte inferior del cuadro de diálogo.

3.  Desplácese hasta la siguiente carpeta:

         C:\Archivos de programa\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Seleccione *Microsoft.WindowsAzure.StorageClient.dll* y, a continuación, haga clic en **Agregar**.

5.  En el cuadro de diálogo **Administrador de referencias**, haga clic en **OK**.

Incorporación del paquete de SendGridIncorporación del paquete NuGet de SendGrid al proyecto
--------------------------------------------------------------------------------------------

Para enviar correos electrónicos con SendGrid, necesita instalar el paquete NuGet de SendGrid.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto WorkerRoleB y, a continuación, seleccione **Manage NuGet Packages**.

    ![Administrar paquetes de NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png)

2.  En el cuadro de diálogo **Manage NuGet Packages**, seleccione la pestaña **Online**, escriba "sendgrid" en el cuadro de búsqueda y presione Entrar.

3.  Haga clic en **Install** en el paquete de **SendGrid**.

    ![Instalar el paquete de Sendgrid](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png)

4.  Cierre el cuadro de diálogo.

Incorporación de la configuración del proyectoIncorporación de la configuración del proyecto
--------------------------------------------------------------------------------------------

Al igual que el rol de trabajo A, el rol de trabajo B necesita las credenciales de la cuenta de almacenamiento para usar tablas, colas y blobs. Además, para enviar correos electrónicos, el rol de trabajo necesita tener credenciales para insertar las llamadas al servicio SendGrid. Por otra parte, para crear un vínculo de cancelación de suscripción a fin de incluirlo en los correos electrónicos enviados, el rol de trabajo necesita saber la URL de la aplicación. Estos valores están almacenados en la configuración del proyecto.

Para las credenciales de la cuenta de almacenamiento, el procedimiento es el mismo que el descrito en el [tercer tutorial](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage).

1.  En el **Explorador de soluciones**, en **Roles** en el proyecto en la nube, haga clic con el botón secundario en **WorkerRoleB** y seleccione **Propiedades**.

2.  Seleccione la pestaña **Settings**.

3.  Asegúrese de que la opción **All Configurations** está seleccionada en la lista desplegable **Configuración de servicio**.

4.  Seleccione la pestaña **Settings** y, a continuación, haga clic en **Agregar configuración**.

5.  Escriba "StorageConnectionString" en la columna **Name**.

6.  Seleccione **Cadena de conexión** en la lista desplegable **Tipo**.

7.  Haga clic en el botón de los puntos suspensivos (**...**) en el extremo derecho de la línea para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

8.  En el cuadro de diálogo **Create Storage Connection String**, haga clic en el botón de radio **Your subscription**.

9.  Seleccione los mismos valores para **Subscription** y **Account name** que eligió para el rol web y el rol de trabajo A.

10. Siga el mismo procedimiento para configurar la cadena de conexión **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**.

    A continuación, cree y configure las tres nuevas opciones que solo usa el rol de trabajo B.

11. En la pestaña **Settings** de la ventana **Propiedades**, haga clic en **Agregar configuración** y, a continuación, agregue las tres opciones de configuración nuevas del tipo **Cadena**:

    -   **Name**: SendGridUserName, **Value**: el nombre de usuario de SendGrid establecido en el [segundo tutorial](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

    -   **Name**: SendGridPassword, **Value**: la contraseña de SendGrid.

    -   **Name**: AzureMailServiceURL, **Value**: la URL de base que la aplicación tendrá al implementarla; por ejemplo: http://sampleurl.cloudapp.net.

    ![Nueva configuración del proyecto WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png)

### Agregar código que se ejecuta cuando el rol de trabajo se inicia

1.  En el proyecto WorkerRoleB, elimine WorkerRole.cs.

2.  Haga clic con el botón secundario en el proyecto WorkerRoleB y seleccione **Agregar elemento existente**.

    ![Agregar elemento existente al rol de trabajo B](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png)

3.  Diríjase a la carpeta donde descargó la aplicación de ejemplo, seleccione el archivo WorkerRoleB.cs del proyecto WorkerRoleB y haga clic en **Agregar**.

> [WACOM.NOTE] Para Visual Studio 2013 con el último SDK y el último paquete NuGet de SendGrid, abra *WorkerRoleB.cs* y realice los siguientes cambios en el código: (1) Elimine la instrucción `using` para `SendGridMail.Transport`. (2) Cambie las dos instancias de `SendGrid.GenerateInstance` por `SendGrid.GetInstance`. (3) Cambie las dos instancias de `REST.GetInstance` por `Web.GetInstance`.

1.  Abra WorkerRoleB.cs y examine el código.

    Como ya ha observado en el rol de trabajo A, el método `OnStart` inicializa las clases de contexto que necesita a fin de trabajar con las entidades de almacenamiento de Azure. Además, se cerciora de que existan todas las tablas, las colas y los contenedores de blobs que va a usar en el método `Run`.

    La diferencia con respecto al rol de trabajo A es la incorporación del contenedor de blobs y la cola de suscripción entre los recursos que habrá que crear si no existen aún. Usará el contenedor de blobs para obtener los archivos que contienen HTML y texto sin formato para el cuerpo del correo electrónico. La cola de suscripción se usa para enviar correos electrónicos de confirmación de suscripción.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

             // Leer la configuración de la cuenta de almacenamiento
             ConfigureDiagnostics();
             Trace.TraceInformation("Inicializando cuenta de almacenamiento en rol de trabajo B");
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

             // Inicializar almacenamiento en cola 
             Trace.TraceInformation("Crear cliente de cola".);
             CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
             this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
             this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

             // Inicializar el almacenamiento de blobs
             CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
             this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

             // Inicializar el almacenamiento de tabla
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

                     // Si se ha llamado a OnStop, devolver para realizar un apagado estable.
                     if (onStopCalled == true)
                     {
                         Trace.TraceInformation("onStopCalled WorkerRoleB");
                         returnedFromRunMethod = true;
                         return;
                     }
                     // Recuperar y procesar un mensaje nuevo desde la cola de envío de correos electrónicos a las listas.
                     msg = sendEmailQueue.GetMessage();
                     if (msg != null)
                     {
                         ProcessQueueMessage(msg);
                         messageFound = true;
                     }

                     // Recuperar y procesar un mensaje nuevo desde la cola de suscripción.
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
                     // No llene el almacén de seguimiento si tenemos un error en cualquier bucle de proceso.
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
             }
         }

    Este código se ejecuta en un bucle infinito hasta que el rol de trabajo se apaga. Si un elemento de trabajo se encuentra en la cola principal, el código lo procesa y, a continuación, comprueba la cola de suscripción.

                     // Recuperar y procesar un mensaje nuevo desde la cola de envío de correos electrónicos a las listas.
                     msg = this.sendEmailQueue.GetMessage();
                     if (msg != null)
                     {
                         ProcessQueueMessage(msg);
                         messageFound = true;
                     }

                     // Recuperar y procesar un mensaje nuevo desde la cola de suscripción.
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

    El propósito del tiempo de suspensión es minimizar los costes de las transacciones de almacenamiento de Azure, como se ha explicado en el [tutorial anterior](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/).

    Si se obtiene un elemento de la cola con el método [GetMessage](http://msdn.microsoft.com/en-us/library/windowsazure/ee741827.aspx), dicho elemento se hace invisible durante 30 segundos para todos los demás roles de trabajo y web que tienen acceso a la cola. Esto es lo que garantiza que solo una instancia de rol de trabajo se ocupe del procesamiento de un mensaje de cola concreto. Puede definir de forma explícita este tiempo de la *concesión exclusiva* (el tiempo durante el cual el elemento de la cola es invisible) mediante la transmisión de un parámetro de [tiempo de espera de visibilidad](http://msdn.microsoft.com/en-us/library/windowsazure/ee758454.aspx) al método `GetMessage`. Si el rol de trabajo tarda más de 30 segundos en procesar un mensaje de la cola, debe aumentar el tiempo de concesión exclusiva para prevenir que otras instancias de rol procesen el mismo mensaje.

    Por otra parte, no deseará definir el tiempo de concesión exclusiva con un valor excesivamente alto. Por ejemplo, si el tiempo de concesión exclusiva se define en 48 horas y el rol de trabajo se apaga inesperadamente después de quitar un mensaje de la cola, los demás roles de trabajo no podrían procesar el mensaje durante 48 horas. El tiempo de concesión exclusiva máximo es de 7 días.

    El método [GetMessages](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx) (observe la "s" al final del nombre) se puede usar para obtener hasta 32 mensajes de la cola en una llamada. Cada acceso a la cola supone un coste de transacción bajo, que será el mismo independientemente de que se devuelvan 32 mensajes o ninguno. El siguiente código captura hasta 32 mensajes en una llamada y, a continuación, los procesa.

    foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))

         {
             ProcessQueueMessage(msg);
             messageFound = true;
         }

    Si usa `GetMessages` para eliminar varios mensajes, asegúrese de que el tiempo de espera de visibilidad da suficiente tiempo a la aplicación para procesar todos los mensajes. Cuando el tiempo de espera de visibilidad caduca, otras instancias de rol pueden tener acceso al mensaje y, cuando lo hacen, la primera instancia no podrá eliminar el mensaje cuando este termine de procesar el elemento de trabajo.

    El método `Run` llama a `ProcessQueueMessage` cuando encuentra un elemento de trabajo en la cola principal:

         private void ProcessQueueMessage(CloudQueueMessage msg)
         {
             // Registrar y eliminar si se trata de un mensaje de cola "dudoso" (procesado repetidas veces
             // y siempre causa un error que impide que el procesamiento se complete).
             // Las aplicaciones de producción deben mover el mensaje "dudoso" a una cola de "mensajes fallidos"
             // para analizar el mensaje en lugar de eliminarlo.           
             if (msg.DequeueCount > 5)
             {
                 Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                     msg.ToString(), GetRoleInstance());
                 sendEmailQueue.DeleteMessage(msg);
                 return;
             }
             // Analizar el mensaje recuperado de la cola.
             // Ejemplo:  2012-01-01,0123456789email@domain.com,0
             var messageParts = msg.AsString.Split(new char[] { ',' });
             var partitionKey = messageParts[0];
             var rowKey = messageParts[1];
             var restartFlag = messageParts[2];
             Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                 partitionKey, rowKey, GetRoleInstance());
             // Si se trata de un reinicio, compruebe que el correo electrónico aún no se ha enviado.
             if (restartFlag == "1")
             {
                 var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                 var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                 var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                 if (messagearchiveRow != null)
                 {
                     // La fila SendEmail está archivada, lo que significa que el correo electrónico se ha enviado. 
                     // Si hay una fila SendEmail en la tabla message, elimínela y
                     // y suprima también el mensaje de la cola.
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
                         // Obtener la fila de la tabla message que contiene datos que necesitamos para enviar el correo electrónico.
             var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
             var retrievedResult = messageTable.Execute(retrieveOperation);
             var emailRowInMessageTable = retrievedResult.Result as SendEmail;
             if (emailRowInMessageTable == null)
             {
                 Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                     partitionKey, rowKey, GetRoleInstance());
                 return;
             }
             // Derivar nombres de blobs a partir del valor MessageRef.
             var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
             var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
             // Si aún no se ha enviado el correo electrónico, envíelo y archive la fila de la tabla.
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

             // Eliminar el mensaje de la cola.
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

    Si el procesamiento de este mensaje se ha reiniciado después de una apagado inesperado, el código comprueba la tabla `messagearchive` para determinar si este correo electrónico se ha enviado ya. En este caso, el código elimina la fila `SendEmail`, en caso de que exista, y elimina el mensaje de la cola.

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

    El método `SendEmailToList` ejecuta el trabajo real de enviar el correo electrónico con la utilización de SendGrid. Si desea usar un servicio diferente a SendGrid, lo único que tiene que hacer es cambiar el código en este método.

    **Nota:** si tiene credenciales que no son válidas en la configuración del proyecto, se producirá un error al llamar a SendGrid, pero la aplicación no obtendrá ninguna indicación del error. Si usa SendGrid en una aplicación de producción, considere la opción de configurar credenciales independientes para la API web a fin de evitar que se produzcan errores silenciosos cuando un administrador cambia la contraseña de la cuenta de usuario de SendGrid. Para obtener más información, consulte [SendGrid MultiAuth - Multiple Account Credentials](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials). Puede configurar las credenciales en <https://sendgrid.com/credentials>.

         private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
             string htmlMessageBodyRef, string textMessageBodyRef)
         {
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(fromEmailAddress);
             email.AddTo(emailAddress);
             email.Html = GetBlobText(htmlMessageBodyRef);
             email.Text = GetBlobText(textMessageBodyRef);
             email.Subject = subjectLine;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                 RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
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

    En el método `GetBlobText`, el código obtiene el tamaño del blob y, a continuación, usa dicho valor para inicializar el objeto `MemoryStream` por motivos de rendimiento. Si no proporciona el tamaño, lo que `MemoryStream` hace es asignar 256 bytes; entonces, si la descarga excede dicho tamaño, asigna 512 bytes más, y así sucesivamente, duplicando en cada ocasión la cantidad asignada. Para un blob grande este proceso resultaría ineficaz en comparación con la asignación de la cantidad correcta al inicio de la descarga.

    El método `Run` llama a `ProcessSubscribeQueueMessage` cuando encuentra un elemento de trabajo en la cola de suscripción:

         private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
         {
             // Registrar y eliminar si se trata de un mensaje de cola "dudoso" (procesado repetidas veces
             // y siempre causa un error que impide que el procesamiento se complete).
             // Las aplicaciones de producción deben mover el mensaje "dudoso" a una cola de "mensajes fallidos"
             // para analizar el mensaje en lugar de eliminarlo.  
             if (msg.DequeueCount > 5)
             {
                 Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                     msg.AsString, GetRoleInstance());
                 subscribeQueue.DeleteMessage(msg);
                 return;
             }
             // Analizar el mensaje recuperado de la cola. El mensaje consta del
             // GUID del suscriptor y el nombre de la lista.
             // Ejemplo:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
             var messageParts = msg.AsString.Split(new char[] { ',' });
             var subscriberGUID = messageParts[0];
             var listName = messageParts[1];
             Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                 subscriberGUID, listName, GetRoleInstance());
             // Obtener la información del suscriptor. 
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
             // Obtener la información de la lista de distribución de correo.
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
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe
         id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                 "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                 "<a href= private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
         {
             var email = SendGrid.GenerateInstance();
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
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }
        quot;{1} private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
         {
             var email = SendGrid.GenerateInstance();
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
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }
        quot;>Confirm Subscription</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                 "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Subscribe to " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }

PruebasPruebas del rol de trabajo B
-----------------------------------

1.  Ejecute la aplicación presionando F5.

2.  Vaya a la página **Messages** para ver el mensaje creado para probar el rol de trabajo A. Transcurrido un minuto más o menos, actualice la página web y observará que la fila ha desaparecido de la lista porque se ha archivado.

3.  Compruebe la bandeja de entrada de correo electrónico donde espera recibir el mensaje. Tenga en cuenta que pueden producirse retrasos en el envío de correos electrónicos mediante SendGrid o en la entrega al cliente de correo electrónico, por lo que es posible que tenga que esperar un poco hasta recibir el correo electrónico. Es necesario que también compruebe la carpeta de correo no deseado.

Pasos siguientesPasos siguientes
--------------------------------

Ya ha creado la aplicación Servicio de correo electrónico de Azure desde cero y lo que consigue es el mismo resultado que el proyecto completado que ha descargado. Para implementarla y probarla en la nube y pasarla a producción, puede usar los mismos procedimientos descritos en el [segundo tutorial](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). Si opta por crear la arquitectura alternativa, consulte [el tutorial de introducción a Sitios web Azure](/en-us/develop/net/tutorials/get-started) para obtener información acerca de cómo implementar el proyecto MVC en un sitio web de Azure.

Para obtener más información sobre el almacenamiento de Azure, consulte el siguiente recurso:

-   [Essential Knowledge for Windows Azure Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (blog de Bruno Terkaly)

Para obtener más información acerca del servicio de tablas de Azure, consulte los siguientes recursos:

-   [Essential Knowledge for Azure Table Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (blog de Bruno Terkaly)
-   [How to get the most out of Windows Azure Tables](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (blog del equipo de almacenamiento de Azure)
-   [Uso del servicio de almacenamiento de tablas en .NET](http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/)
-   [Windows Azure Storage Client Library 2.0 Tables Deep Dive](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (blog del equipo de almacenamiento de Azure)
-   [Casos reales: Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx)

Para obtener más información acerca del servicio de colas de Azure y las colas del Bus de servicio de Azure, consulte los siguientes recursos:

-   [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
-   [Colas de Windows Azure y Colas de Service Bus de Windows Azure: comparación y diferencias](http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx)
-   [Uso del almacenamiento en cola en .NET](/en-us/develop/net/how-to-guides/queue-service/)

Para obtener más información acerca del servicio BLOB de Azure, consulte los siguientes recursos:

-   [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
-   [Uso del servicio de almacenamiento de blobs de Azure en .NET](/en-us/develop/net/how-to-guides/blob-storage/)

Para obtener más información acerca de la autoescala de roles del Servicio en la nube de Azure, consulte los siguientes recursos:

-   [Uso del bloque de autoescala de la aplicación](/en-us/develop/net/how-to-guides/autoscaling/)
-   [Autoscaling and Azure](http://msdn.microsoft.com/en-us/library/hh680945(v=PandP.50).aspx)
-   [Building Elastic, Autoscalable Solutions with Azure](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (vídeo del canal 9 de MSDN)

ReconocimientosReconocimientos
------------------------------

Estos tutoriales y la aplicación de ejemplo los han elaborado [Rick Anderson](http://blogs.msdn.com/b/rickandy/) y Tom Dykstra. Nos gustaría mostrar nuestro agradecimiento a las siguientes personas por su colaboración:

-   Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart))
-   [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) )
-   [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
-   Don Glover
-   Jai Haridas
-   [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter: [@coolcsh](http://twitter.com/coolcsh))
-   [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
-   [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
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

