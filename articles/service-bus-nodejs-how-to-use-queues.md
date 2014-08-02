<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Node.js) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use Service Bus queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Utilización de las colas del bus de servicio
============================================

Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están escritos en JavaScript y usan el módulo Node.js de Azure. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas, envío y recepción de mensajes** y **eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [¿Qué son las colas del bus de servicio?](#what-are-service-bus-queues)
-   [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
-   [Creación de una aplicación Node.js](#create-app)
-   [Configuración de la aplicación para usar el bus de servicio](#configure-app)
-   [Creación de una cola](#create-queue)
-   [Envío de mensajes a una cola](#send-messages)
-   [Recepción de mensajes de una cola](#receive-messages)
-   [Actuación ante errores de la aplicación y mensajes que no se pueden leer](#handle-crashes)
-   [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

Creación de una aplicación Node.js
----------------------------------

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servicio en la nube Node.js](/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/) (usando Windows PowerShell) o [Sitio web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configuración de la aplicación para usar el bus de servicio
-----------------------------------------------------------

Para utilizar el bus de servicio de Azure, es necesario descargar y usar el paquete azure para Node.js. Este paquete incluye un conjunto de prácticas bibliotecas que se comunican con los servicios REST del bus de servicio.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Use la ventana de comandos **Windows PowerShell for Node.js** para navegar hasta la carpeta **c:\\node\\sbqueues\\WebRole1** donde ha creado la aplicación de muestra.

2.  Escriba **npm install azure** en la ventana de comandos. Esto debería devolver una salida similar a la siguiente:

        azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, busque el paquete **azure**, que contiene las bibliotecas necesarias para obtener acceso a las colas del bus de servicio.

### Importación del módulo

Utilizando el Bloc de notas u otro editor de texto, agregue el código siguiente en la parte superior del archivo **server.js** de la aplicación:

     var azure = require('azure');

### Configuración de una conexión del bus de servicio de Azure

El módulo azure leerá las variables de entorno AZURE\_SERVICEBUS\_NAMESPACE y AZURE\_SERVICEBUS\_ACCESS\_KEY para obtener la información necesaria para conectarse al bus de servicio de Azure. Si estas variables de entorno no están configuradas, debe especificar la información de la cuenta al llamar a **createServiceBusService**.

Para ver un ejemplo de cómo configurar las variables de entorno en un archivo de configuración para un servicio de nube de Azure, consulte [Servicio de nube de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Creación de una cola
--------------------

El objeto **ServiceBusService** le permite trabajar con colas. El siguiente código crea un objeto **ServiceBusService**. Agréguelo cerca de la parte superior del archivo **server.js**, tras la instrucción para importar el módulo azure:

    var serviceBusService = azure.createServiceBusService();

Al llamar a **createQueueIfNotExists** en el objeto **ServiceBusService**, se obtiene la cola especificada (si existe) o se crea una nueva con el nombre especificado. El código siguiente utiliza **createQueueIfNotExists** para crear una cola llamada "myqueue" o conectarse a ella.

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // La cola existe.
        }
    });

**createServiceBusService** también admite opciones adicionales, lo que permite modificar la configuración predeterminada de las colas, como el período de vida de los mensajes o el tamaño máximo de la cola. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // La cola existe.
        }
    });

### Filtros

Las operaciones de filtrado opcionales pueden aplicarse a las tareas realizadas utilizando **ServiceBusService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

     function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

     function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Con el siguiente código se crea un objeto **ServiceBusService** que utiliza el filtro **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

Envío de mensajes a una cola
----------------------------

Para enviar un mensaje a una cola del bus de servicio, la aplicación debe llamar al método **sendQueueMessage** del objeto **ServiceBusService**. Los mensajes enviados a las colas del bus de servicio (y recibidos de ellas) son objetos **BrokeredMessage** y cuentan con un conjunto de propiedades estándar (como **Label** y **TimeToLive**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena como mensaje, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

El ejemplo siguiente demuestra cómo enviar un mensaje de prueba a la cola llamada "myqueue" usando **sendQueueMessage**:

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        };
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // Mensaje enviado.
        }
    });

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

Recepción de mensajes de una cola
---------------------------------

Los mensajes se reciben de una cola utilizando el método **receiveQueueMessage** del objeto **ServiceBusService**. De manera predeterminada, los mensajes se eliminan de la cola una vez que se leen; sin embargo, puede leer (echar un vistazo) y bloquear los mensajes sin eliminarlos de la cola estableciendo el parámetro opcional **isPeekLock** en **true**.

El funcionamiento predeterminado por el que los mensajes se eliminan tras leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **isPeekLock** está establecido en **true**, el proceso de recepción se convierte en una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **deleteMessage** y facilitando el mensaje que se va a eliminar a modo de parámetro. El método **deleteMessage** marcará el mensaje como consumido y lo eliminará de la cola.

En el ejemplo que aparece a continuación, se indica cómo se pueden recibir y procesar los mensajes usando **receiveQueueMessage**. El primer ejemplo recibe y elimina un mensaje y, a continuación, recibe un mensaje usando **isPeekLock** establecido en true; a continuación, elimina el mensaje usando **deleteMessage**:

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Mensaje recibido y eliminado
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Mensaje recibido y bloqueado
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Mensaje eliminado
                }
            }
        }
    });

Actuación ante errores de la aplicación y mensajes que no se pueden leer
------------------------------------------------------------------------

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlockMessage** del objeto **ServiceBusService**. Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

Pasos siguientes
----------------

Ahora que conoce los fundamentos de las colas del bus de servicio, siga estos vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio](http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx)
-   Visite el repositorio del [SDK de Azure para Node.js](https://github.com/WindowsAzure/azure-sdk-for-node) (en inglés) en GitHub.

