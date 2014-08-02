<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="" solutions="" manager="" editor="" />

Uso del servicio Cola desde Node.js
===================================

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio Cola de Azure. Los ejemplos están escritos usando la API Node.js. Entre los escenarios descritos, se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de los mensajes en cola, así como la **creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [¿Qué es el servicio Cola?](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#create-account)
-   [Creación de una aplicación Node.js](#create-app)
-   [Configuración de su aplicación para obtener acceso al almacenamiento](#configure-access)
-   [Configuración de una cadena de conexión de almacenamiento de Azure](#setup-connection-string)
-   [Creación de una cola](#create-queue)
-   [Inserción de un mensaje en una cola](#insert-message)
-   [Inspección del siguiente mensaje](#peek-message)
-   [Extracción del siguiente mensaje de la cola](#get-message)
-   [Cambio del contenido de un mensaje en cola](#change-contents)
-   [Opciones adicionales para quitar mensajes de la cola](#advanced-get)
-   [Obtención de la longitud de la cola](#get-queue-length)
-   [Eliminación de una cola](#delete-queue)
-   [Pasos siguientes](#next-steps)

¿Qué es el servicio Cola?
-------------------------

El servicio Cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de 100 TB de una cuenta de almacenamiento. Entre los usos frecuentes del servicio Cola se encuentran los siguientes:

-   Creación de trabajo pendiente para el procesamiento asincrónico
-   Cambio de mensajes de un rol web de Azure a un rol de trabajo

Conceptos
---------

El servicio Cola contiene los siguientes componentes:

![Cola1](./media/storage-nodejs-how-to-use-queues/queue1.png)

-   **Formato de dirección URL:** Es posible dirigir las colas con el siguiente formato de dirección URL:

        http://storageaccount.queue.core.windows.net/queue  

    Las siguientes direcciones URL desvían una de las colas del diagrama:

        http://myaccount.queue.core.windows.net/imagesToDownload

-   **Cuenta de almacenamiento:** Todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Una cuenta de almacenamiento es el nivel superior del espacio de nombres para el acceso a las colas. El tamaño total del contenido del blob, la tabla y la cola de una cuenta de almacenamiento no puede superar los 100 TB.

-   **Cola:** Una cola contiene un conjunto de mensajes. Todos los mensajes deben encontrarse en una cola.

-   **Mensaje:** Un mensaje, en cualquier formato, de hasta 64 KB.

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

Necesita una cuenta de almacenamiento de Azure para usar operaciones de almacenamiento. Siga estos pasos para crear una cuenta de almacenamiento. También puede crear una cuenta de almacenamiento [usando la API de REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx).

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  En la parte inferior del panel de navegación, haga clic en **+NEW**.

    ![+new](./media/storage-nodejs-how-to-use-queues/plus-new.png)

3.  Haga clic en **Cuenta de almacenamiento** y, a continuación, en **Quick Create**.

    ![Cuadro de diálogo de creación rápida](./media/storage-nodejs-how-to-use-queues/quick-storage.png)

4.  En URL, escriba el nombre de subdominio que vaya usar en el URI para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

5.  Seleccione un grupo de región/afinidad en el que ubicar el almacenamiento. Si va a usar el almacenamiento desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

6.  Haga clic en **Create Storage Account**.

Creación de una aplicación Node.js
----------------------------------

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un Sitio web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servicio en la nube Node.js]({localLink:2221} "Aplicación web con Express") (usando Windows PowerShell) o [Sitio web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configuración de su aplicación para obtener acceso al almacenamiento
--------------------------------------------------------------------

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Node.js azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure** en la ventana de comandos. Esto debería devolver la salida siguiente:

        azure@0.7.5 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@1.1.1
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.7 (sax@0.5.2)
        |-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, busque el paquete **azure**, que contiene las bibliotecas necesarias para obtener acceso al almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo **server.js** de la aplicación en la que pretenda usar el almacenamiento:

     var azure = require('azure');

Configuración de una conexión de almacenamiento de Azure
--------------------------------------------------------

El módulo azure leerá las variables de entorno AZURE\_STORAGE\_ACCOUNT y AZURE\_STORAGE\_ACCESS\_KEY para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta mediante la llamada a **createQueueService**.

Para ver un ejemplo de cómo configurar las variables de entorno en un archivo de configuración para un servicio de nube de Azure, consulte [Servicio de nube de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Creación de una cola
--------------------

El siguiente código crea un objeto **QueueService**, que le permite trabajar con colas.

    var queueService = azure.createQueueService();

Utilice el método **createQueueIfNotExists**, que devuelve la cola especificada si ya existe o crea una nueva cola con el nombre especificado si todavía no existe.

    queueService.createQueueIfNotExists(queueName, function(error){
        if(!error){
            // La cola existe.
        }
    });

### Filtros

Las operaciones de filtrado opcionales pueden aplicarse a las tareas realizadas utilizando **QueueService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

     function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

     function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **QueueService** que usa **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueService = azure.createQueueService().withFilter(retryOperations);

Inserción de un mensaje en una cola
-----------------------------------

Para insertar un mensaje en una cola, utilice el método **createMessage** para crear un nuevo mensaje y agregarlo a la cola.

    queueService.createMessage(queueName, "Hello world!", function(error){
        if(!error){
            // Mensaje insertado.
        }
    });

Inspección del siguiente mensaje
--------------------------------

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **peekMessages**. De forma predeterminada, **peekMessages** inspecciona un único mensaje.

    queueService.peekMessages(queueName, function(error, messages){
        if(!error){
            // Mensajes inspeccionados
            // El texto está disponible en messages[0].messagetext
        }
    });

> [WACOM.NOTE] Si se utiliza **peekMessage** cuando no existen mensajes en la cola, no se devolverá un error, pero tampoco se devolverán mensajes.

Extracción del siguiente mensaje de la cola
-------------------------------------------

El código borra un mensaje de una cola en dos pasos. Si llama a **getMessages**, obtiene, de forma predeterminada, el siguiente mensaje en una cola. Un mensaje devuelto por **getMessages** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **deleteMessage**. Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **deleteMessage** justo después de que se haya procesado el mensaje.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Procesar el mensaje en menos de 30 segundos.
            // El texto está disponible en messages[0].messagetext 
            var message = messages[0]
            queueService.deleteMessage(queueName
                , message.messageid
                , message.popreceipt
                , function(error){
                    if(!error){
                        // Mensaje eliminado
                    }
                });
        }
    });

> [WACOM.NOTE] Si se utiliza **getMessages** cuando no existen mensajes en la cola, no se devolverá un error, pero tampoco se devolverán mensajes.

Cambio del contenido de un mensaje en cola
------------------------------------------

Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el estado de la tarea de trabajo. El código siguiente utiliza el método **updateMessage** para actualizar un mensaje.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Mensaje obtenido
            var message = messages[0];
            queueService.updateMessage(queueName
                , message.messageid
                , message.popreceipt
                , 10
                , { messagetext: 'in your message, doing stuff.' }
                , function(error){
                    if(!error){
                        // Mensaje actualizado correctamente
                    }
                });
        }
    });

Opciones adicionales para quitar mensajes de la cola
----------------------------------------------------

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **getMessages** para obtener 15 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle for. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

    queueService.getMessages(queueName
        , {numofmessages: 15, visibilitytimeout: 5 * 60}
        , function(error, messages){
        if(!error){
            // Mensajes recuperados
            for(var index in messages){
                // El texto está disponible en messages[index].messagetext
                var message = messages[index];
                queueService.deleteMessage(queueName
                    , message.messageid
                    , message.popreceipt
                    , function(error){
                        if(!error){
                            // Mensaje eliminado
                        }
                    });
            }
        }
    });

Obtención de la longitud de la cola
-----------------------------------

Puede obtener una estimación del número de mensajes existentes en una cola. El método **getQueueMetadata** solicita al servicio de cola la devolución de metadatos acerca de la cola, y la propiedad **approximatemessagecount** de la respuesta contiene un recuento del número de mensajes que hay en la cola. El recuento solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud.

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
        if(!error){
            // La longitud de la cola está disponible en queueInfo.approximatemessagecount
        }
    });

Eliminación de una cola
-----------------------

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **deleteQueue** en el objeto de cola.

    queueService.deleteQueue(queueName, function(error){
        if(!error){
            // La cola se ha eliminado
        }
    });

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento en cola, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).
-   Visite el repositorio del [SDK de Azure para Node.js](https://github.com/WindowsAzure/azure-sdk-for-node) (en inglés) en GitHub.

