---
title: "Guía del protocolo de conexiones híbridas de Retransmisión de Azure | Microsoft Docs"
description: "Guía del protocolo de conexiones híbridas de Azure Relay."
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 43c40baa74b3f7c1f5c9d6626b25bcd45c2f9a10
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de conexiones híbridas de Azure Relay
Relay de Azure es uno de los pilares básicos de las funcionalidades de la plataforma Azure Service Bus. La nueva funcionalidad *Conexiones híbridas* de Relay es una evolución segura y de protocolo abierto basada en HTTP y WebSockets. Sustituye a la antigua característica de *BizTalk Services* con el mismo nombre que se basaba en un protocolo propietario. La integración de Conexiones híbridas en Azure App Service seguirá funcionando de la misma forma.

Conexiones híbridas permite establecer una comunicación de secuencias binaria y bidireccional entre dos aplicaciones en red durante la que una de las partes, o ambas, pueden residir detrás de firewalls o mecanismos NAT. En este artículo se describen las interacciones del lado cliente con la característica de retransmisión de Conexiones híbridas para conectar clientes con los roles de agente de escucha y remitente. Además, se explica cómo los agentes de escucha aceptan nuevas conexiones.

## <a name="interaction-model"></a>Modelo de interacción
El servicio de retransmisión de Conexiones híbridas conecta dos partes mediante la creación de un punto de encuentro en la nube de Azure que ambas partes pueden detectar y a la que pueden conectarse desde su propia red. Ese punto de encuentro se denomina "Conexión híbrida" en esta documentación y en otros artículos, en las API y también en Azure Portal. Al punto de conexión del servicio Conexiones híbridas nos referiremos como el "servicio" en el resto de este artículo. El modelo de interacción se basa en la nomenclatura que han establecido muchas otras API de red.

Hay un agente de escucha que, primero, indica que está preparado para controlar las conexiones entrantes y, después, las acepta a medida que llegan. Por otro lado, hay un cliente de conexión que se conecta con el agente de escucha, de modo que se espera que se acepte dicha conexión para establecer una ruta de comunicación bidireccional.
"Conectar", "escuchar" y "aceptar" son los mismos términos que encontrará en la mayoría de las API de socket.

En cualquier modelo de comunicación retransmitida, las partes establecen las conexiones salientes hacia un punto de conexión de servicio, lo que provoca que al "agente de escucha" también se le conozca como "cliente" en el uso coloquial, y también puede causar otras sobrecargas terminológicas. Por consiguiente, esta es la terminología precisa que usamos en Conexiones híbridas:

Los programas de ambos extremos de la conexión se denominan "clientes", ya que son clientes del servicio. El cliente que espera y acepta conexiones es el "agente de escucha" o que asume el "rol de escucha". El cliente que inicia una nueva conexión hacia un agente de escucha a través del servicio se denominará "remitente" o que asume el "rol de remitente".

### <a name="listener-interactions"></a>Interacciones del agente de escucha
El agente de escucha tiene cuatro interacciones con el servicio. Más adelante, en la sección de referencia de este artículo, explicaremos todos los detalles de conexión.

#### <a name="listen"></a>Escuchar
Para indicar al servicio que un agente de escucha está listo para aceptar conexiones, crea una conexión WebSocket de salida. El protocolo de enlace de la conexión incluye el nombre de una conexión híbrida configurada en el espacio de nombres de Relay y un token de seguridad que concede el derecho de "escucha" a dicho nombre.
Cuando el servicio acepta WebSocket, se completa el registro y el WebSocket web establecido se mantiene activo como "canal de control" para habilitar todas las interacciones posteriores. El servicio admite hasta 25 agentes de escucha simultáneos en una conexión híbrida. Si hay dos, o más, agentes de escucha activos, las conexiones entrantes se reparten entre ellos en orden aleatorio, por lo que no se garantiza que la distribución sea equitativa.

#### <a name="accept"></a>Accept
Cuando un remitente abre una nueva conexión en el servicio, este elige a uno de los agentes de escucha activos de la conexión híbrida y le envía una notificación. Esta se envía al agente de escucha a través del canal de control abierto en forma de mensaje JSON que contiene la dirección URL del punto de conexión de WebSocket al que debe conectarse el agente de escucha para aceptar la conexión.

La dirección URL puede, y debe, usarla directamente el agente de escucha sin tener que realizar ningún proceso adicional.
La información codificada solo es válida durante un breve período, básicamente, el tiempo que el remitente esté dispuesto a esperar a que se establezca la conexión de un extremo a otro, pero con un máximo de 30 segundos. La dirección URL solo puede utilizarse para realizar un intento de conexión correcta. En cuanto se establece la conexión de WebSocket con la dirección URL de encuentro, toda la actividad posterior de dicho WebSocket se retransmite desde y hacia el remitente, sin que el servicio realice ninguna intervención ni interpretación.

#### <a name="renew"></a>Renovación
El token de seguridad que se debe utilizar para registrar el agente de escucha y mantener el canal de control puede expirar mientras que el agente de escucha está activo. La expiración del token no afecta a las conexiones en curso, pero hace que el servicio anule el canal de control en cuanto expire, o poco después. La operación de "renovación" es un mensaje JSON que puede enviar el agente de escucha para reemplazar el token asociado al canal de control. De este modo, el canal de control se puede mantener durante períodos prolongados.

#### <a name="ping"></a>Ping
Si el canal de control permanece inactivo durante mucho tiempo, los intermediarios del proceso, como los equilibradores de carga o los NAT, pueden anular la conexión TCP. La operación "ping" evita que esto suceda mediante el envío de una pequeña cantidad de datos en el canal que recuerda a todos los usuarios de la ruta de red que la conexión está pensada para que se mantenga activa. Además, sirve al agente de escucha como prueba de que está "activo". Si el ping falla, el canal de control debe considerarse inutilizable y debe volver a conectar el agente de escucha.

### <a name="sender-interaction"></a>Interacción del remitente
El remitente tiene una única interacción con el servicio: la conexión.

#### <a name="connect"></a>Conectar
La operación de "conexión" abre un WebSocket en el servicio que proporciona el nombre de la conexión híbrida y un token de seguridad (opcional, pero obligatorio de manera predeterminada) que concede el permiso de "envío" a la cadena de consulta. Después, el servicio interactúa con el agente de escucha como se ha descrito anteriormente y este crea una conexión de encuentro que se combina con este WebSocket. Cuando el WebSocket se haya aceptado, las restantes interacciones de dicho WebSocket se realizarán con un agente de escucha conectado.

### <a name="interaction-summary"></a>Resumen de interacción
El resultado de este modelo de interacción es que el cliente remitente sale del protocolo de enlace con un WebSocket "limpio", que está conectado a un agente de escucha y que no necesita más preámbulos ni preparativos. Este modelo que casi todas las implementaciones existentes del cliente de WebSocket saquen provecho con facilidad del servicio Conexiones híbridas especificando una dirección URL con una estructura correcta en el nivel de cliente de su WebSocket.

El WebSocket de la conexión de encuentro que el agente de escucha obtiene a través de la interacción de aceptación también está "limpio". Además, se puede entregar a cualquier implementación de servidor de WebSocket existente con una abstracción adicional mínima que distinga entre las operaciones de "aceptación" de los agentes de escucha de red local de su marco y las de "aceptación" remotas de conexiones híbridas.

## <a name="protocol-reference"></a>Referencia de protocolos

En esta sección se describen los detalles de las interacciones de protocolo que se han descrito anteriormente.

Todas las conexiones WebSocket se realizan en el puerto 443 como una actualización de la versión 1.1 de HTTPS, que normalmente la abstrae alguna API o marco de WebSocket. La descripción que ofrecemos en este documento sirve para cualquier tipo de implementación; no sugerimos ningún marco concreto.

### <a name="listener-protocol"></a>Protocolo del agente de escucha
El protocolo del agente de escucha está formado por dos movimientos de conexión y tres operaciones de mensaje.

#### <a name="listener-control-channel-connection"></a>Conexión de canal de control del agente de escucha
El canal de control se abre al crearse una conexión de WebSocket en:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` es el nombre de dominio completo del espacio de nombres de Azure Relay que hospeda la conexión híbrida, normalmente del formulario `{myname}.servicebus.windows.net`.

Las opciones de los parámetros de cadena de consulta son las siguientes:

| . | Obligatorio | DESCRIPCIÓN |
| --- | --- | --- |
| `sb-hc-action` |Sí |Para el rol de agente de escucha, el parámetro debe ser **sb-hc-action=listen**. |
| `{path}` |Sí |La ruta de acceso del espacio de nombres codificado para URL de la conexión híbrida preconfigurada en la que se registrará este agente de escucha. Esta expresión se anexa a la parte de la ruta de acceso `$hc/` fija. |
| `sb-hc-token` |Sí\* |El agente de escucha debe proporcionar un token de acceso compartido de Service Bus válido y codificado para URL al espacio de nombres o a la conexión híbrida que concede el derecho de **escucha**. |
| `sb-hc-id` |Sin  |Este id. opcional especificado por el cliente permite realizar un seguimiento de diagnósticos completo. |

Si se produce un error en la conexión de WebSocket porque la ruta de acceso de la conexión híbrida no está registrada, porque que falta un token, o no es válido, o por cualquier otro error, se utilizará el modelo de comentarios de estado normal de HTTP 1.1. La descripción del estado contiene un identificador de seguimiento de errores que se pueda comunicar al equipo de soporte técnico de Azure:

| Código | Error | DESCRIPCIÓN |
| --- | --- | --- |
| 404 |No encontrado |La ruta de acceso de la conexión híbrida no es válida o el formato de la URL base no es correcto. |
| 401 |No autorizado |Falta el token de seguridad, tiene una estructura incorrecta o no es válido. |
| 403 |Prohibido |El token de seguridad no es válido para esta ruta de acceso en esta acción. |
| 500 |Error interno |Se ha producido un error en el servicio. |

Si el servicio cierra intencionadamente la conexión de WebSocket después de haberse configurado inicialmente, el motivo se comunica mediante un código de error adecuado de protocolo WebSocket, junto con un mensaje de error descriptivo, que también incluye un identificador de seguimiento. El servicio no cerrará el canal de control si no se produce una condición de error. Todos los cierres "limpios" los controla el cliente.

| Estado de WS | DESCRIPCIÓN |
| --- | --- |
| 1001 |La ruta de acceso de Conexión híbrida se ha eliminado o deshabilitado. |
| 1008 |El token de seguridad ha expirado, por lo que se ha infringido la directiva de autorización. |
| 1011 |Se ha producido un error en el servicio. |

### <a name="accept-handshake"></a>Protocolo de enlace de aceptación
La notificación de aceptación la envía el servicio al agente de escucha a través del canal de control establecido previamente en forma de mensaje JSON en un marco de texto de WebSocket. No hay ninguna respuesta a este mensaje.

El mensaje contiene un objeto JSON denominado "accept", que define las siguientes propiedades:

* **address**: la cadena de dirección URL que se usará para establecer que el WebSocket del servicio acepte una conexión entrante.
* **id**: el identificador único para esta conexión. Si ha sido el cliente remitente quien ha suministrado el id., será el valor que ha especificado este; de lo contrario, será uno generado por el sistema.
* **connectHeaders**: todos los encabezados HTTP que ha suministrado el remitente al punto de conexión de Relay, que también incluyen los encabezados Sec-WebSocket-Protocol y Sec-WebSocket-Extensions.

#### <a name="accept-message"></a>Mensaje accept

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",  
        "connectHeaders" : {                                         
            "Host" : "...",                                                
            "Sec-WebSocket-Protocol" : "...",                              
            "Sec-WebSocket-Extensions" : "..."                             
        }                                                            
     }                                                         
}
```

El agente de escucha usa la dirección URL especificada en el mensaje JSON para establecer el WebSocket con el objetivo de aceptar o rechazar el socket remitente.

#### <a name="accepting-the-socket"></a>Aceptación del socket
Para aceptarlo, el agente de escucha establece una conexión de WebSocket a la dirección proporcionada.

Si el mensaje "accept" contiene un encabezado `Sec-WebSocket-Protocol`, cabe esperar que el agente de escucha solo acepte el WebSocket si admite dicho protocolo. Además, establece el encabezado tal como se establece el WebSocket.

Lo mismo sucede con el encabezado `Sec-WebSocket-Extensions`. Si el marco admite una extensión, es necesario establecer el encabezado en la respuesta del lado servidor del protocolo de enlace `Sec-WebSocket-Extensions` que se necesita para la extensión.

La URL debe utilizarse tal cual para establecer el socket de aceptación, pero debe contener los parámetros siguientes:

| . | Obligatorio | DESCRIPCIÓN |
| --- | --- | --- |
| `sb-hc-action` |Sí |Para aceptar un socket, el parámetro tiene que ser `sb-hc-action=accept` |
| `{path}` |Sí |(vea el párrafo siguiente) |
| `sb-hc-id` |Sin  |Consulte la descripción anterior del **identificador**. |

`{path}` es la ruta de acceso del espacio de nombres con codificación URL de la conexión híbrida preconfigurada en la que se registra este agente de escucha. Esta expresión se anexa a la parte de la ruta de acceso `$hc/` fija. 

La expresión `path` se puede ampliar con un sufijo y una expresión de cadena de consulta que vaya después del nombre registrado después de una barra diagonal de separación. Esto permite que el cliente remitente pase los argumentos de envío al agente de escucha receptor cuando no sea posible incluir encabezados HTTP. La expectativa es que el marco del agente de escucha analice la parte fija y el nombre registrado de la ruta de acceso, y cree el resto, posiblemente sin ningún argumento de cadena de consulta con el prefijo `sb-`, disponible para la aplicación para decidir si acepta la conexión.

Para más información, consulte la sección "Protocolo del remitente", que encontrará a continuación.

Si hay un error, el servicio puede responder como se indica a continuación:

| Código | Error | DESCRIPCIÓN |
| --- | --- | --- |
| 403 |Prohibido |La URL no es válida. |
| 500 |Error interno |Se produjo un error en el servicio. |

Una vez que se establece la conexión, el servidor apaga el WebSocket cuando se apaga el WebSocket remitente, o cuando el estado es el siguiente:

| Estado de WS | DESCRIPCIÓN |
| --- | --- |
| 1001 |El cliente remitente cierra la conexión. |
| 1001 |La ruta de acceso de Conexión híbrida se ha eliminado o deshabilitado. |
| 1008 |El token de seguridad ha expirado, por lo que se ha infringido la directiva de autorización. |
| 1011 |Se ha producido un error en el servicio. |

#### <a name="rejecting-the-socket"></a>Rechazo del socket
Para rechazar el socket después de inspeccionar el mensaje "accept", se necesita un protocolo de enlace similar para que el código y la descripción del estado que comunican el motivo del rechazo pueden enviarse al remitente.

La opción de diseño de protocolo que presentamos en este documento es un protocolo de enlace WebSocket (que está pensado para que termine en un estado de error definido). De esta forma, las implementaciones del cliente del agente de escucha pueden seguir dependiendo de un cliente WebSocket y no hace falta utilizar otro cliente HTTP básico.

Para rechazar el socket, el cliente toma el URI de dirección del mensaje "accept" y le anexa dos parámetros de cadena de consulta como se muestra a continuación:

| Parámetro | Obligatorio | DESCRIPCIÓN |
| --- | --- | --- |
| statusCode |Sí |Código de estado HTTP numérico. |
| statusDescription |Sí |Motivo del rechazo en lenguaje natural. |

El URI resultante se utiliza luego para establecer una conexión WebSocket.

Cuando se completa correctamente, este protocolo de enlace genera un error de forma intencionada con el código HTTP 410, ya que no se ha establecido ningún WebSocket. Si algo falla, los siguientes códigos describen el error:

| Código | Error | DESCRIPCIÓN |
| --- | --- | --- |
| 403 |Prohibido |La URL no es válida. |
| 500 |Error interno |Se ha producido un error en el servicio. |

### <a name="listener-token-renewal"></a>Renovación del token del agente de escucha
Cuando el token del agente de escucha está a punto de expirar, puede reemplazarse con el envío de un mensaje en un marco de texto al servicio a través del canal de control establecido. El mensaje contiene un objeto JSON llamado `renewToken`, que define las siguientes propiedades:

* **token**: un token válido de acceso compartido de Service Bus y con codificación URL para el espacio de nombres o la conexión híbrida que concede el derecho de **escucha**.

#### <a name="renewtoken-message"></a>Mensaje renewToken

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Si se produce un error en la validación del token, se deniega el acceso y el servicio en la nube cierra el WebSocket del canal de control con un error. En caso contrario, no se produce ninguna respuesta.

| Estado de WS | DESCRIPCIÓN |
| --- | --- |
| 1008 |El token de seguridad ha expirado, por lo que se ha infringido la directiva de autorización. |

## <a name="sender-protocol"></a>Protocolo del remitente
El protocolo del remitente es idéntico a la forma en que se establece un agente de escucha.
El objetivo es dotar de la máxima transparencia al WebSocket de un extremo a otro. La dirección a la que conectarse es la misma que la del agente de escucha, pero el objeto "action" es diferente y el token necesita otro permiso:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*namespace-address* es el nombre de dominio completo del espacio de nombres de Azure Relay que hospeda la conexión híbrida, normalmente del formulario `{myname}.servicebus.windows.net`.

La solicitud puede contener más encabezados HTTP arbitrarios, incluidos los definidos por la aplicación. Todos los encabezados especificados se transmiten al agente de escucha y pueden encontrarse en el objeto `connectHeader` del mensaje de control **accept**.

Estas son las opciones de los parámetros de cadena de consulta:

| Parámetro | ¿Necesario? | DESCRIPCIÓN |
| --- | --- | --- |
| `sb-hc-action` |Sí |En el caso del rol de remitente, el parámetro debe ser `action=connect`. |
| `{path}` |Sí |(vea el párrafo siguiente) |
| `sb-hc-token` |Sí\* |El agente de escucha necesita proporcionar un token de acceso compartido de Service Bus válido y codificado para URL al espacio de nombres o a la conexión híbrida que concede el derecho de **envío**. |
| `sb-hc-id` |Sin  |Un id. opcional que permite realizar el seguimiento de diagnóstico completo y que puede usarlo el agente de escucha durante el protocolo de enlace de aceptación. |

`{path}` es la ruta de acceso del espacio de nombres codificado para la dirección URL de la conexión híbrida preconfigurada en la que se registrará este agente de escucha. La expresión `path` se puede ampliar con un sufijo y una expresión de cadena de consulta para comunicarse de nuevo. Si la conexión híbrida se registra en la ruta de acceso `hyco`, la expresión `path` puede ser `hyco/suffix?param=value&...`, seguida de los parámetros de cadena de consulta que se definen aquí. Una expresión completa puede ser como la siguiente:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

La expresión `path` se pasa al agente de escucha del identificador URI de dirección que se incluye en el mensaje de control "accept".

Si se produce un error en la conexión de WebSocket porque la ruta de acceso de la conexión híbrida no está registrada, porque que falta un token, o no es válido, o por cualquier otro error, se utilizará el modelo de comentarios de estado normal de HTTP 1.1. La descripción del estado contiene un identificador de seguimiento de errores que se pueda comunicar al equipo de soporte técnico de Azure:

| Código | Error | DESCRIPCIÓN |
| --- | --- | --- |
| 404 |No encontrado |La ruta de acceso de la conexión híbrida no es válida o el formato de la URL base no es correcto. |
| 401 |No autorizado |Falta el token de seguridad, tiene una estructura incorrecta o no es válido. |
| 403 |Prohibido |El token de seguridad no es válido para esta ruta de acceso y para esta acción. |
| 500 |Error interno |Se ha producido un error en el servicio. |

Si el servicio cierra intencionadamente la conexión de WebSocket después de haberse configurado inicialmente, el motivo se comunica mediante un código de error adecuado de protocolo WebSocket, junto con un mensaje de error descriptivo, que también incluye un identificador de seguimiento.

| Estado de WS | DESCRIPCIÓN |
| --- | --- |
| 1000 |El agente de escucha cierra el socket. |
| 1001 |La ruta de acceso de Conexión híbrida se ha eliminado o deshabilitado. |
| 1008 |El token de seguridad ha expirado, por lo que se ha infringido la directiva de autorización. |
| 1011 |Se ha producido un error en el servicio. |

## <a name="next-steps"></a>pasos siguientes
* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)

