<properties
	pageTitle="SDK de dispositivo IoT de Microsoft Azure para C: más información sobre IoTHubClient"
	description="Detalles adicionales sobre la biblioteca de IoTHubClient en el SDK de dispositivos IoT de Azure para C"
	services="iot-hub"
	documentationCenter="na"
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# SDK de dispositivos IoT de Microsoft Azure para C: más información sobre IoTHubClient

En el [primer el artículo](iot-hub-device-sdk-c-intro.md) de esta serie se presentaba el **SDK de dispositivos IoT de Microsoft Azure para C**. En este artículo se explicaba que hay dos capas de arquitectura en SDK. En la base está la biblioteca de **IoTHubClient** que administra directamente la comunicación con Centro de IoT. Y está también la biblioteca del **serializador** que se basa en el mismo para proporcionar servicios de serialización. En este artículo le proporcionamos detalles adicionales sobre la biblioteca **IoTHubClient**.

El artículo anterior describe cómo usar la biblioteca **IoTHubClient** para enviar eventos a Centro de IoT y recibir mensajes. En este artículo extenderemos esa discusión, explicando cómo administrar con mayor exactitud *cuándo* enviar y recibir datos con una introducción a la **las API de nivel inferior**. También explicaremos cómo adjuntar propiedades a eventos (y recuperarlas de mensajes) mediante las características de control de propiedades en la biblioteca **IoTHubClient**. Y, a continuación, proporcionaremos una explicación adicional sobre las diferentes formas de controlar los mensajes recibidos desde el Centro de IoT.

El artículo concluye cubriendo un par de temas variados, incluyendo más información acerca de las credenciales del dispositivo y cómo cambiar el comportamiento de **IoTHubClient** a través de las opciones de configuración.

Usaremos ejemplos del SDK de **IoTHubClient** para explicar estos temas. Así, si desea seguir el artículo, eche un vistazo a las aplicaciones **iothub\_client\_sample\_http** y **iothub\_client\_sample\_amqp** que se incluyen en el SDK de dispositivo IoT de Azure para C. Todo lo descrito a continuación se muestra en estos ejemplos.

## Las API de nivel inferior

En el artículo anterior describíamos el funcionamiento básico de **IotHubClient** dentro del contexto de la aplicación **iothub\_client\_sample\_amqp**. Por ejemplo, explicamos cómo inicializar la biblioteca mediante este código...

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

...cómo enviar eventos mediante la llamada a esta función...

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

... también describimos cómo recibir mensajes mediante el registro de una función de devolución de llamada...

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

... y cómo liberar los recursos mediante código así...

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Sin embargo, hay funciones complementarias de cada una de estas API:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


Estas funciones incluyen "LL" en el nombre de la API. Aparte de eso, los parámetros de cada una de estas funciones son idénticos a sus analógicos no LL. Sin embargo, el comportamiento de estas funciones es diferente en un aspecto importante...

Cuando se llama a **IoTHubClient\_CreateFromConnectionString** las bibliotecas subyacentes crean un nuevo subproceso que se ejecuta en segundo plano. Este subproceso se ocupa de enviar eventos a Centro de IoT y de recibir sus mensajes. Ningún subproceso de este tipo se crea cuando se trabaja con las API "LL". La creación de subproceso en segundo plano es muy práctico para el desarrollador. No tiene que preocuparse de forma explícita por enviar eventos y recibir mensajes de Centro de IoT: esto se produce automáticamente en segundo plano. En contraste, las API "LL" proporcionan un control explícito sobre la comunicación con Centro de IoT, si lo necesita.

Para comprenderlo mejor, veamos un ejemplo:

Cuando se llama a **IoTHubClient\_SendEventAsync** lo que realmente se está haciendo es colocar el evento en un búfer. El subproceso en segundo plano creado cuando se llama a **IoTHubClient\_CreateFromConnectionString** supervisa continuamente este búfer y envía los datos que contiene a Centro de IoT. Esto sucede en segundo plano al mismo tiempo que el subproceso principal está realizando otro trabajo.

De forma similar al registrar una función de devolución de llamada para mensajes con **IoTHubClient\_SetMessageCallback**, deja preparado el SDK para que el subproceso en segundo plano invoque la función de devolución de llamada cuando se recibe un mensaje, independientemente del subproceso principal.

Las API "LL" no crean un subproceso en segundo plano. En su lugar, debe llamarse una nueva API para enviar y recibir datos a y desde Centro de IoT de forma explícita. Una vez más, echemos un vistazo a un ejemplo...

La aplicación **iothub\_client\_sample\_http** que se incluye en el SDK resalta las API de nivel inferiores. En este ejemplo, enviamos eventos a Centro de IoT con un código de una forma similar a la siguiente:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Las tres primeras líneas crean el mensaje y la última línea envía el evento. Sin embargo, como se mencionó anteriormente, "enviar" el evento significa solamente que los datos se colocan en un búfer. Nada se transmite en la red cuando llamamos **IoTHubClient\_LL\_SendEventAsync**. Para poder realmente introducir los datos en Centro de IoT tiene que llamar a **IoTHubClient\_LL\_DoWork** como en este ejemplo:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este fragmento de código (de la aplicación **iothub\_client\_sample\_http**) llama repetidamente a **IoTHubClient\_LL\_DoWork**. Cada vez que se llama a **IoTHubClient\_LL\_DoWork** envía algunos eventos del búfer a Centro de IoT y recupera un mensaje en cola que se haya enviado al dispositivo. En este último caso significa si se registra una función de devolución de llamada para los mensajes, la devolución de llamada se invoca (suponiendo que hay mensajes en cola). Este tipo de función de devolución de llamada se habría registrado con un código como aquí:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

La razón por la que se llama a **IoTHubClient\_LL\_DoWork** a menudo en un bucle es porque cada vez que se llama envía *algunos* eventos almacenados en el búfer a Centro de IoT y recupera *el siguiente* mensaje en cola para el dispositivo. No está garantizado que cada llamada envíe todos los eventos almacenados en búfer o recupere todos los mensajes en cola. Si desea enviar todos los eventos en el búfer y luego continuar con el resto del procesamiento puede reemplazar el bucle anterior con un código como aquí:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Esta lógica llama a **IoTHubClient\_LL\_DoWork** hasta que todos los eventos en el búfer se hayan enviado a Centro de IoT. Tenga en cuenta que esto no significa que todos los mensajes en cola se hayan recibido también. En parte esto se debe a que recuperar "todos" los mensajes no es una acción tan determinista, ¿qué sucede si se recuperan "todos" los mensajes pero se envía otra al dispositivo inmediatamente después? Una mejor manera de afrontar esto es con un tiempo de espera programado. Por ejemplo, la función de devolución de llamada de mensaje puede restablecer un temporizador cada vez que se invoca. Y, a continuación, se puede escribir lógica para continuar el procesamiento si, por ejemplo, no se recibió ningún mensaje en los últimos X segundos.

Cuando haya terminado de especificar eventos y recibir mensajes, asegúrese de llamar a la función correspondiente para limpiar los recursos.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Eso es todo. Básicamente, hay un conjunto de API para enviar y recibir datos con un subproceso en segundo plano y otro conjunto de API que hace lo mismo sin el subproceso en segundo plano. Muchos desarrolladores pueden preferir las API no LL, pero las API de nivel inferior son útiles cuando el desarrollador desea un control explícito sobre las transmisiones de red. Por ejemplo, algunos dispositivos recopilarán datos con el tiempo y solo los eventos de entrada a intervalos especificados (por ejemplo, una vez cada hora o una vez al día). Las API de nivel inferior ofrecen la capacidad de controlar de forma explícita cuándo se envían y reciben los datos desde Centro de IoT. Hay quien simplemente prefieren la simplicidad que proporcionan las API de nivel inferiores, donde todo ocurre en el subproceso principal en lugar de que parte del trabajo se desarrolle en segundo plano.

Sea cual sea el modelo que elija, asegúrese de mantener la coherencia en las API que usa. Si empieza con una llamada a **IoTHubClient\_LL\_CreateFromConnectionString**, asegúrese de usar solamente las API de nivel inferior correspondientes para cualquier trabajo que realice a continuación:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

Y lo mismo sucede al revés... si empieza con **IoTHubClient\_CreateFromConnectionString** siga con las API no LL para cualquier procesamiento adicional.

En el SDK de dispositivos IoT de Azure para C vea la aplicación **iothub\_client\_sample\_http** para obtener un ejemplo completo de las API de nivel inferior. Puede tomar como referencia la aplicación **iothub\_client\_sample\_amqp** para obtener un ejemplo completo de las API no LL.

## Control de propiedad

Hasta ahora cuando hemos descrito el envío de datos, nos hemos referido al cuerpo del mensaje. Por ejemplo, considere este fragmento de código:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este ejemplo envía un mensaje a Centro de IoT con el texto "Hello World". Pero Centro de IoT también permite que se adjunten propiedades a cada mensaje. Las propiedades son simplemente pares de valor nombrados que se pueden adjuntar al mensaje. Por ejemplo, podemos modificamos el código anterior para adjuntar una propiedad al mensaje:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Comenzamos llamando a **IoTHubMessage\_Properties** y pasándole el control del mensaje. Lo que obtenemos es una referencia **MAP\_HANDLE** que nos permite empezar a agregar propiedades. Esto se logra mediante una llamada a **Map\_AddOrUpdate** que toma una referencia de un MAP\_HANDLE, el nombre de propiedad y el valor de propiedad. Con esta API, podemos se pueden agregar todas la propiedades que se desee.

Cuando se lee el evento en el **Centro de eventos** el receptor puede enumerar las propiedades y recuperar sus valores correspondientes. Por ejemplo, en .NET esto se logra mediante el acceso a la [colección de propiedades en el objeto EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.properties.aspx).

En el ejemplo anterior, vamos a adjuntar propiedades a un evento que le enviemos a Centro de IoT. Pero las propiedades también se pueden adjuntar a los mensajes recibidos desde Centro de IoT. Si se quiere recuperar las propiedades de un mensaje, se puede usar código similar al siguiente en la función de devolución de llamada del mensaje:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

La llamada a **IoTHubMessage\_Properties** proporciona la referencia **MAP\_HANDLE**. Y, a continuación, se pasa esa referencia a **Map\_GetInternals** para obtener una referencia a una matriz de los pares nombre/valor (así como un recuento de las propiedades). A partir de aquí no hay más que enumerar las propiedades para tener acceso a los valores que se deseen.

No es necesario usar propiedades en la aplicación. Pero si necesita establecerlas en eventos o recuperarlas de mensajes, la biblioteca **IoTHubClient** se lo pone muy fácil.

## Control de mensajes

Anteriormente se explicó que cuando llegan los mensajes de Centro de IoT la biblioteca **IoTHubClient** responde invocando una función de devolución de llamada registrada. Pero hay un parámetro de valor devuelto de esta función que merece una explicación adicional. Este es un extracto de la función de devolución de llamada en la aplicación de ejemplo **iothub\_client\_sample\_http**:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
	. . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que el tipo devuelto es **IOTHUBMESSAGE\_DISPOSITION\_RESULT** y en este caso, devolvemos **IOTHUBMESSAGE\_ACCEPTED**. Resulta que hay otros valores que podemos obtener de esta función de que cambian la forma en la que la biblioteca **IoTHubClient** reacciona a la devolución de llamada del mensaje. Estas son las opciones.

-   **IOTHUBMESSAGE\_ACCEPTED**: el mensaje se procesó correctamente. La biblioteca **IoTHubClient** no volverá a invocar la función de devolución de llamada con el mismo mensaje.

-   **IOTHUBMESSAGE\_REJECTED**: no se procesó el mensaje y no hay intención hacerlo en el futuro. La biblioteca **IoTHubClient** no debe invocar la función de devolución de llamada con el mismo mensaje.

-   **IOTHUBMESSAGE\_ABANDONED**: no se procesó correctamente el mensaje. Pero la biblioteca **IoTHubClient** debe invocar la función de devolución de llamada con el mismo mensaje.

Para los dos primeros códigos de retorno la biblioteca **IoTHubClient** envía un mensaje a Centro de IoT que indica que el mensaje debe eliminarse de la cola del dispositivo y no volver a entregarse. El efecto neto es el mismo (el mensaje se elimina de la cola del dispositivo), pero se registra si el mensaje se aceptó o rechazó. El registro de esta distinción es útil para los remitentes del mensaje que pueden escuchar los comentarios y saber así si un dispositivo aceptó o rechazó un mensaje determinado.

En este último caso también se envía un mensaje a Centro de IoT que indica que debería volverse a entregar el mensaje. Normalmente un mensaje se abandona si hay algún error, pero se desea volver a procesar el mensaje. Por contraste, rechazar un mensaje es adecuado si se produce un error irrecuperable (o si simplemente se decide que no se desea procesar el mensaje).

En cualquier caso simplemente debe tener en cuenta los diferentes códigos de retorno de forma que pueda obtener el comportamiento deseado de la biblioteca **IoTHubClient**.

## Credenciales de dispositivo alternativas

Como se explicó anteriormente, lo primero que hay que hacer cuando se trabaja con la biblioteca **IoTHubClient** es obtener un **IOTHUB\_CLIENT\_HANDLE** con una llamada como ésta:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Básicamente, los argumentos de **IoTHubClient\_CreateFromConnectionString** son la cadena de conexión de nuestro dispositivo y un parámetro que indica el protocolo que se usará para comunicarse con Centro de IoT. La cadena de conexión tiene un formato que tiene el siguiente aspecto:

> HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY

Básicamente, hay cuatro grupos de información aquí: nombre de Centro de IoT, sufijo de Centro de IoT, identificador de dispositivo y clave de acceso compartido. Obtendrá el nombre de dominio completo (FQDN) de un Centro de IoT cuando cree la instancia de Centro de IoT en el Portal de administración de Azure, esta opción proporciona el nombre de Centro de IoT (la primera parte del FQDN) y el sufijo del Centro de IoT (el resto del FQDN). Obtendrá el identificador de dispositivo y la clave de acceso compartido al registrar el dispositivo con Centro de IoT (como se describe en el [artículo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** le ofrece una manera de inicializar la biblioteca. Pero si lo prefiere, puede crear un nuevo **IOTHUB\_CLIENT\_HANDLE** usando estos parámetros individuales en lugar de la cadena de conexión. Reemplace esto con el siguiente código:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Esto consigue lo mismo que **IoTHubClient\_CreateFromConnectionString**.

Puede parecer obvio que deseara usar **IoTHubClient\_CreateFromConnectionString** en lugar de este método de inicialización más detallado. Pero tenga en cuenta que cuando registramos un dispositivo en Centro de IoT lo que obtenemos es un identificador de dispositivo y una clave de dispositivo (no una cadena de conexión). La herramienta de SDK **Administrador de dispositivos** que presenté en el [artículo anterior](iot-hub-device-sdk-c-intro.md) usa las bibliotecas en el **SDK de servicios IoT de Azure** para crear la cadena de conexión desde el identificador del dispositivo, la clave de dispositivo y el nombre de host de Centro de IoT. Por lo tanto, una llamada a **IoTHubClient\_LL\_Create** puede ser preferible porque ahorra el paso de generar una cadena de conexión. Use el método que le resulte conveniente.

## Opciones de configuración

Hasta ahora todo lo descrito sobre forma en la que trabaja la biblioteca **IoTHubClient** refleja su comportamiento predeterminado. Sin embargo, existen algunas opciones que se pueden establecer para cambiar el funcionamiento de la biblioteca. Esto se logra aprovechando la API **IoTHubClient\_LL\_SetOption**. En este ejemplo:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Hay un par de opciones que se usan con frecuencia:

-   **SetBatching** (booleano): si es true, los datos enviados a Centro de IoT se envían en lotes. Si es false, se envían mensajes individualmente. El valor predeterminado es false.

-   **Tiempo de espera** (entero sin sino): este valor se representa en milisegundos. Si el envío de una solicitud HTTP o la recepción de una respuesta supera este tiempo, la conexión agota el tiempo.

Es importante tener conocimientos sobre la opción de procesamiento por lotes. De forma predeterminada la biblioteca da entrada a los eventos individualmente (un evento único es lo que se pasa a **IoTHubClient\_LL\_SendEventAsync**). Pero si la opción de procesamiento por lotes es true, la biblioteca recopilará tantos eventos como sea posible desde el búfer (hasta el tamaño máximo de mensaje que aceptará Centro de IoT). El lote de eventos se envía a Centro de IoT en una sola llamada HTTP (los eventos individuales están agrupados en una matriz JSON). Activar el procesamiento por lotes normalmente da como resultado un gran aumento del rendimiento ya que con ello se reducen la ida y vuelta de red. Y además reduce considerablemente ancho de banda ya que se va a enviar un conjunto de encabezados HTTP con un lote de eventos en lugar de un conjunto de encabezados para cada evento individual. A menos que tenga una razón concreta para no hacerlo, normalmente es mejor activar el procesamiento por lotes.

## Pasos siguientes

Este artículo brinda detalles sobre el comportamiento de la biblioteca **IoTHubClient** que se encuentra en el **SDK de dispositivo IoT de Azure para C**. Con esta información debe tener una buena comprensión de las capacidades de la biblioteca **IoTHubClient**. En el [siguiente artículo](iot-hub-device-sdk-c-serializer.md) le proporcionamos detalles similares en la biblioteca del **serializador**.

<!---HONumber=Oct15_HO1-->