<properties urlDisplayName="Service Bus Queues" pageTitle="Uso de las colas del bus de servicio (Node.js) - Azure" metaKeywords="colas del bus de servicio de Azure, colas de Azure, mensajería de Azure, colas de Azure en Node.js" description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Utilización de las colas del bus de servicio

Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están
escritos en JavaScript y usan el módulo Node.js de Azure. Los escenarios
cubiertos incluyen **creación de colas, envío y recepción de mensajes** y 
**eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos
siguientes].

## Tabla de contenido

-   [¿Qué son las colas del bus de servicio?][]
-   [Creación de un espacio de nombres de servicio][]
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres][]
-   [Creación de una aplicación Node.js](#create-app)
-   [Configuración de la aplicación para usar el bus de servicio](#configure-app)
-   [Direccionamiento del una cola](#create-queue)
-   [Direccionamiento del mensajes a una cola](#send-messages)
-   [Direccionamiento del mensajes desde una cola](#receive-messages)
-   [Direccionamiento del ante errores de la aplicación y mensajes que no se pueden leer](#handle-crashes)
-   [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure], [Servicio en la nube de Node.js][Servicio en la nube Node.js] (usando Windows PowerShell) o [Sitio web con WebMatrix].

## <a name="configure-app"> </a>Configuración de la aplicación para usar el bus de servicio

Para usar Bus de servicio de Azure, tendrá que descargar y usar el
paquete Node.js de Azure. Esto incluye un conjunto de útiles bibliotecas que
se comunican con los servicios REST de Bus de servicio.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Use la ventana de comandos **Windows PowerShell para Node.js** para desplazarse
    hasta la carpeta **c:\\node\\sbqueues\\WebRole1** en la que
    ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure** en la ventana de comandos, cuyo resultado
    debe ser similar al siguiente:

        azure@0.7.5 node_modules\azure
		+-- dateformat@1.0.2-1.2.3
		+-- xmlbuilder@0.4.2
		+-- node-uuid@1.2.0
		+-- mime@1.2.9
		+-- underscore@1.4.4
		+-- validator@1.1.1
		+-- tunnel@0.0.2
		+-- wns@0.5.3
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Puede ejecutar manualmente el comando **ls** para verificar que se ha creado la carpeta
    **node\_modules**. Dentro de esa carpeta, busque el paquete
    **azure**, que contiene las bibliotecas que necesita para obtener acceso a las
    colas de Bus de servicio.

### Importación del módulo

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del
archivo **server.js** de la aplicación:

    var azure = require('azure');

### Configuración de una conexión del bus de servicio de Azure

El módulo azure leerá las variables de entorno AZURE\_SERVICEBUS\_NAMESPACE y AZURE\_SERVICEBUS\_ACCESS\_KEY para obtener la información necesaria para conectarse al bus de servicio de Azure. Si estas variables de entorno no están configuradas, debe especificar la información de la cuenta al llamar a **createServiceBusService**.

Para ver un ejemplo de cómo configurar las variables de entorno en un archivo de configuración para un servicio de nube de Azure, consulte [Servicio en la nube de Node.js con almacenamiento].

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento]

## <a name="create-queue"> </a>Creación de una cola

El objeto **ServiceBusService** le permite trabajar con colas. El
siguiente código crea un objeto **ServiceBusService**. Agréguelo cerca de la
parte superior del archivo **server.js**, después de la instrucción para importar el módulo de
azure:

    var serviceBusService = azure.createServiceBusService();

Al llamar a **createQueueIfNotExists** en el objeto **ServiceBusService**
se devolverá la cola especificada (si existe) o se creará una nueva
cola con el nombre especificado. El código siguiente utiliza
**createQueueIfNotExists** para crear una cola llamada "myqueue", o bien
para conectarse a ella:

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** también admite opciones adicionales, que
permiten sobrescribir la configuración de cola predeterminada como el tiempo que dura
la transmisión de un mensaje o el tamaño máximo de la cola. En el ejemplo siguiente se muestra cómo establecer
el tamaño máximo de la cola en 5 GB con un tiempo de transmisión de 1 minuto:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

###Filtros

Las operaciones de filtrado opcionales pueden aplicarse a las tareas realizadas utilizando **ServiceBusService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

		function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

		function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Con el siguiente código se crea un objeto **ServiceBusService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Envío de mensajes a una cola

Para enviar un mensaje a una cola de bus de servicio, la aplicación llamará al método
**sendQueueMessage** en el objeto **ServiceBusService**.
Los mensajes enviados a (y recibidos de) las colas de Bus de servicio son objetos
**BrokeredMessage**, y tienen un conjunto de propiedades estándar (como
**Label** y **TimeToLive**), un diccionario que se usa para contener
las propiedades específicas de aplicación personalizada y un cuerpo de datos de
de aplicación arbitrarios. Una aplicación puede establecer el cuerpo del mensaje
al pasar un valor de cadena como el mensaje y cualquier propiedad estándar necesaria
se rellenará con valores predeterminados.

En el siguiente ejemplo se demuestra cómo enviar un mensaje de prueba a
la cola "myqueue" mediante **sendQueueMessage**:

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        }};
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

Las colas de bus de servicio admiten un mensaje con un tamaño máximo de 256 KB (el encabezado,
que incluye las propiedades de la aplicación estándar y personalizadas, puede tener
un tamaño máximo de 64 KB). No hay ningún límite en el número de mensajes
contenidos en una cola, pero sí hay un límite en el tamaño total de los mensajes
que una cola contiene. El tamaño de esta cola se define en tiempo de creación, con un
límite máximo de 5 GB.

## <a name="receive-messages"> </a>Recepción de mensajes de una cola

Los mensajes se reciben de una cola con la utilización del método **receiveQueueMessage**
en el objeto **ServiceBusService**. De forma predeterminada, los mensajes se
eliminan de la cola cuando se leen; sin embargo, puede leer (peek)
y bloquear los mensajes sin eliminarlos de la cola estableciendo el
parámetro opcional **isPeekLock** en **true**.

El comportamiento predeterminado de leer y eliminar el mensaje como parte de la
operación de recepción es el modelo más sencillo y funciona mejor para los escenarios
en que una aplicación puede tolerar no procesar un mensaje en caso
de un error. Para comprender esto, considere un escenario en el que el
consumidor emite la solicitud de recepción y, a continuación, se bloquea antes de
procesarla. Dado que el Bus de servicio habrá marcado el mensaje como consumido,
a continuación, cuando la aplicación se reinicia y comienza a consumir mensajes de nuevo,
habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **isPeekLock** está definido en **true**, la recepción
se realiza en una operación en dos fases, lo que permite admitir aplicaciones
que no pueden tolerar la pérdida de mensajes. Cuando el Bus de servicio recibe una
solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que
otros consumidores lo reciban y lo devuelve a la aplicación.
Después de que la aplicación termina de procesar el mensaje (o lo almacena
de forma confiable para su futuro procesamiento), completa la segunda fase del
proceso de recepción al llamar al método **deleteMessage** y establecimiento como parámetro
la eliminación del mensaje. El método **deleteMessage**
marcará el mensaje como consumido y lo eliminará de la cola.

El ejemplo siguiente muestra cómo se pueden recibir y
procesar menajes con **receiveQueueMessage**. El ejemplo primero recibe y
elimina un mensaje y, a continuación, recibe un mensaje con **isPeekLock** definido en
true; a continuación, elimina el mensaje mediante **deleteMessage**:

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de
errores de la aplicación o de las dificultades para procesar un mensaje. Si una
aplicación de recepción es incapaz de procesar el mensaje por alguna razón,
puede llamar al método **unlockMessage** en el objeto
**ServiceBusService**. Esto hará que el Bus de servicio desbloquee el
mensaje de la cola y esté disponible para recibirlo de nuevo,
por la misma aplicación que lo consume o por otra
aplicación.

También hay un tiempo de espera asociado con un mensaje bloqueado en la
cola, y si la aplicación no puede procesar el mensaje antes del vencimiento
del tiempo de espera (por ejemplo, si se bloquea la aplicación), el bus de servicio
desbloquea el mensaje automáticamente y lo vuelve a poner a disposición
para que se pueda volver a recibir.

En caso de que la aplicación se bloquee después de procesar el mensaje
pero antes de llamar al método **deleteMessage**, el mensaje
se volverá a entregar a la aplicación cuando se reinicie. Esto suele denominarse
**Al menos un procesamiento**, es decir, que cada mensaje se procesará
al menos una vez, pero en ciertas situaciones se puede volver a entregar
el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado,
los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación
para controlar la entrega de mensajes duplicados. A menudo esto se consigue mediante la
propiedad del mensaje **MessageId**, que permanecerá constante entre los
intentos de entrega.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de las colas del bus de servicio, siga estos
vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio][]
-   Visite el repositorio de [SDK de Azure para Node.js] en GitHub.

  [SDK de Azure para Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Pasos siguientes]: #next-steps
  [¿Qué son las colas del bus de servicio?]: #what-are-service-bus-queues
  [Creación de un espacio de nombres de servicio]: #create-a-service-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-default-credentials
  [Creación de una aplicación Node.js]: #create-app
  [Configuración de la aplicación para usar el bus de servicio]: #configure-app
  [Direccionamiento del una cola]: #create-queue
  [Direccionamiento del mensajes a una cola]: #send-messages
  [Direccionamiento del mensajes desde una cola]: #receive-messages
  [Direccionamiento del ante errores de la aplicación y mensajes que no se pueden leer]: #handle-crashes
  [Conceptos de cola]: ../../dotNet/Media/sb-queues-08.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Servicio en la nube de Node.js]: /es-es/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Colas, temas y suscripciones del bus de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx
  [Sitio web con WebMatrix]: /es-es/develop/nodejs/tutorials/web-site-with-webmatrix/
[Portal de administración anterior]: ../../Shared/Media/previous-portal.png
  [Creación e implementación de una aplicación Node.js en un sitio web de Azure]: /es-es/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servicio en la nube de Node.js con almacenamiento]: /es-es/develop/nodejs/tutorials/web-app-with-storage/
  [Aplicación web de Node.js con almacenamiento]: /es-es/develop/nodejs/tutorials/web-site-with-storage/
