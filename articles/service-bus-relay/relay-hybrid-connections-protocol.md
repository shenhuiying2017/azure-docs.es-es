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
ms.date: 02/14/2017
ms.author: sethm;clemensv
translationtype: Human Translation
ms.sourcegitcommit: 4a972b9b8b52a90f27afda98d8bdc661016d1fe1
ms.openlocfilehash: f5fd4c6c0b8db3fe91d8b57a68fe33dcff353a59
ms.lasthandoff: 02/28/2017


---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocolo de conexiones híbridas de Azure Relay
Relay de Azure es uno de los pilares básicos de las funcionalidades de la plataforma Azure Service Bus. La nueva función "Conexiones híbridas" de Relay es una evolución segura y de protocolo abierto basada en HTTP y WebSockets. Sustituye a la antigua característica de "BizTalk Services" con el mismo nombre que se basaba en un protocolo propietario. La integración de Conexiones híbridas en Azure App Service seguirá funcionando de la misma forma.

"Conexiones híbridas" permite establecer una comunicación de secuencia binaria y bidireccional entre dos aplicaciones en red con la que una de las partes, o ambas, pueden residir detrás de firewalls o mecanismos NAT. En este artículo se describen las interacciones del lado cliente con la característica de retransmisión de Conexiones híbridas para conectar clientes con los roles de agente de escucha y remitente. Además, se explica cómo los agentes de escucha aceptan nuevas conexiones.

## <a name="interaction-model"></a>Modelo de interacción
El servicio de retransmisión de Conexiones híbridas conecta dos partes mediante la creación de un punto de encuentro en la nube de Azure que ambas partes pueden detectar y a la que pueden conectarse desde su propia red. Ese punto de encuentro se denomina "Conexión híbrida" en esta documentación y en otros artículos, en las API y también en Azure Portal. Al punto de conexión de servicio de Conexiones híbridas nos referiremos como el "servicio" en el resto de este artículo. El modelo de interacción se basa en la nomenclatura que han establecido muchas otras API de red:

Hay un agente de escucha que, primero, indica que está preparado para controlar las conexiones entrantes y, después, las acepta a medida que llegan. Por otro lado, hay un cliente de conexión que se conecta con el agente de escucha, de modo que se espera que se acepte dicha conexión para establecer una ruta de comunicación bidireccional.
"Conectar", "escuchar" y "aceptar" son los mismos términos que encontrará en la mayoría de las API de socket.

En cualquier modelo de comunicación retransmitida, las partes establecen las conexiones salientes hacia un punto de conexión de servicio, lo que provoca que al "agente de escucha" también se le conozca como "cliente" en el uso coloquial, y también puede causar otras sobrecargas terminológicas. Por tanto, usaremos la siguiente terminología exacta para las conexiones híbridas:

Los programas de ambos extremos de la conexión se denominarán "cliente", ya que se trata de clientes del servicio. El cliente que espera y acepta conexiones es el "agente de escucha" o que asume el "rol de escucha". El cliente que inicia una nueva conexión hacia un agente de escucha a través del servicio se denominará "remitente" o que asume el "rol de remitente".

### <a name="listener-interactions"></a>Interacciones del agente de escucha
El agente de escucha tiene cuatro interacciones con el servicio. Más adelante, en la sección de referencia de este artículo, explicaremos todos los detalles de conexión.

#### <a name="listen"></a>Escuchar
Para indicar al servicio que un agente de escucha está listo para aceptar conexiones, crea una conexión de socket web de salida. El protocolo de enlace de conexión incluye el nombre de una conexión híbrida configurada en el espacio de nombres de Relay y un token de seguridad que concede el derecho de "escucha" a dicho nombre.
Cuando el servicio acepta el socket web, se completa el registro y el socket web establecido se mantiene activo como "canal de control" para habilitar todas las interacciones posteriores. El servicio admite hasta 25 agentes de escucha simultáneos en una conexión híbrida. Si hay dos o más agentes de escucha activos, se equilibrarán las conexiones entrantes en orden aleatorio; no se garantiza que se haga una distribución equitativa.

#### <a name="accept"></a>Accept
Cada vez que un remitente abra una nueva conexión en el servicio, este elegirá a uno de los agentes de escucha activos de la conexión híbrida y le enviará una notificación. Esta se transmite al agente de escucha a través del canal de control abierto como un mensaje JSON que contiene la URL del punto de conexión del socket web al que debe conectarse el agente de escucha para aceptar la conexión.

La dirección URL puede y debe usarla directamente el agente de escucha sin ningún proceso adicional; la información codificada solo es válida durante un breve periodo, básicamente, siempre y cuando el remitente esté dispuesto a esperar a que se establezca la conexión de extremo a extremo, pero con un máximo de 30 segundos. La dirección URL solo puede utilizarse para realizar un intento de conexión correcta. En cuanto se establece la conexión de socket web con la dirección URL de encuentro, toda la actividad posterior de dicho socket web se retransmite desde y hacia el remitente, sin que el servicio realice ninguna intervención ni interpretación.

#### <a name="renew"></a>Renovación
El token de seguridad que se debe utilizar para registrar el agente de escucha y mantener el canal de control puede expirar mientras que el agente de escucha está activo. La expiración del token no afectará a las conexiones salientes, pero provocará que el servicio anule el canal de control en cuanto expire o poco después. La operación de "renovación" es un mensaje JSON que puede enviar el agente de escucha para reemplazar el token asociado al canal de control. De este modo, el canal de control se puede mantener durante períodos prolongados.

#### <a name="ping"></a>Ping
Si el canal de control permanece inactivo durante mucho tiempo, los intermediarios del proceso, como los equilibradores de carga o los NAT, pueden anular la conexión TCP. La operación "ping" evita que esto suceda con el envío de una pequeña cantidad de datos en el canal que recuerda a todos los usuarios de la ruta de red que la conexión está pensada para que se mantenga activa. Además, sirve como prueba de "actividad" del agente de escucha. Si el ping falla, el canal de control debe considerarse inutilizable y debe volver a conectar el agente de escucha.

### <a name="sender-interaction"></a>Interacción del remitente
El remitente solo tiene una única interacción con el servicio: la conexión.

#### <a name="connect"></a>Conectar
La operación de "conexión" abre un socket web en el servicio, de modo que se proporciona el nombre de la conexión híbrida y un token de seguridad (opcional, pero obligatorio de manera predeterminada) que concede el permiso de "envío" en la cadena de consulta. Después, el servicio interactúa con el agente de escucha de la forma descrita anteriormente y consigue que el agente de escucha cree una conexión de encuentro que se combinará con este socket web. Cuando se haya aceptado el socket web, todas las demás interacciones del socket web serán, por lo tanto, con un agente de escucha conectado.

### <a name="interaction-summary"></a>Resumen de interacción
El resultado de este modelo de interacción es que el cliente remitente se extrae del protocolo de enlace con un socket web "limpio" que está conectado a un agente de escucha y que no necesita más preámbulos ni preparativos. Esto permite que prácticamente todas las implementaciones existentes del cliente de socket web aprovechan con facilidad el servicio Conexiones híbridas simplemente especificando una dirección URL con una estructura correcta en el nivel de cliente de su socket web.

El socket web de conexión de encuentro que el agente de escucha obtiene a través de la interacción de aceptación también está "limpio". Además, se puede entregar a cualquier implementación de servidor de socket web existente con una abstracción adicional mínima, que distingue entre las operaciones de "aceptación" de los agentes de escucha de red local de su marco y las de "aceptación" remotas de conexiones híbridas.

## <a name="protocol-reference"></a>Referencia de protocolos

En esta sección se describen los detalles de las interacciones de protocolo descritas anteriormente.

Todas las conexiones de socket web se realizan en el puerto 443 como una actualización de la versión 1.1 de HTTPS, que normalmente la abstrae alguna API o marco de socket web. La descripción que ofrecemos en este documento sirve para cualquier tipo de implementación; no sugerimos ningún marco concreto.

### <a name="listener-protocol"></a>Protocolo del agente de escucha
El protocolo del agente de escucha está formado por dos movimientos de conexión y tres operaciones de mensaje.

#### <a name="listener-control-channel-connection"></a>Conexión de canal de control del agente de escucha
El canal de control se abre al crearse una conexión de socket web para:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` es el nombre de dominio completo del espacio de nombres de Azure Relay que hospeda la conexión híbrida, normalmente del formulario `{myname}.servicebus.windows.net`.

Las opciones de los parámetros de cadena de consulta son las siguientes:

| Parámetro | Obligatorio | Descripción |
| --- | --- | --- |
| sb-hc-action |Sí |Para el rol de agente de escucha, el parámetro debe ser **sb-hc-action=listen**. |
| {path} |Sí |La ruta de acceso del espacio de nombres codificado para URL de la conexión híbrida preconfigurada en la que se registrará este agente de escucha. Esta expresión se anexa a la parte de la ruta de acceso `$hc/` fija. |
| sb-hc-token |Sí\* |El agente de escucha debe proporcionar un token de acceso compartido de Service Bus válido y codificado para URL al espacio de nombres o a la conexión híbrida que concede el derecho de **escucha**. |
| sb-hc-id |No |Este id. opcional especificado por el cliente permite realizar un seguimiento de diagnósticos completo. |

Si se produce un error en la conexión de socket web porque la ruta de acceso de conexión híbrida no está registrada, a que falta un token o no es válido, o a algún otro error, se proporcionarán los comentarios de los errores con el modelo de comentarios de estado normal de HTTP 1.1. La descripción de estado contendrá un id. de seguimiento de errores que se pueda comunicar al equipo de soporte técnico de Azure:

| Código | Error | Descripción |
| --- | --- | --- |
| 404 |No encontrado |La **ruta de acceso** de conexiones híbridas no es válida o la dirección URL base es incorrecta. |
| 401 |No autorizado |Falta el token de seguridad, tiene una estructura incorrecta o no es válido. |
| 403 |Prohibido |El token de seguridad no es válido para esta ruta de acceso en esta acción. |
| 500 |Error interno |Se ha producido un error en el servicio. |

Si el servicio cierra intencionadamente la conexión de socket web después de haberse configurado inicialmente, el motivo se comunicará con un código de error adecuado de protocolo de socket web y un mensaje de error descriptivo, que también incluye un id. de seguimiento. El servicio no cerrará el canal de control si no se produce una condición de error. Todos los cierres "limpios" los controla el cliente.

| Estado de WS | Descripción |
| --- | --- |
| 1001 |La ruta de acceso de Conexión híbrida se ha eliminado o deshabilitado. |
| 1008 |El token de seguridad ha expirado y, por tanto, se ha infringido la directiva de autorización. |
| 1011 |Se ha producido un error en el servicio. |

### <a name="accept-handshake"></a>Protocolo de enlace de aceptación
La notificación de aceptación la envía el servicio al agente de escucha a través del canal de control establecido previamente como un mensaje JSON en un marco de texto de socket web. No hay ninguna respuesta a este mensaje.

El mensaje contiene un objeto JSON denominado "accept", que define las siguientes propiedades:

* **address**: la cadena de dirección URL que se usará para establecer el socket web en el servicio para aceptar una conexión entrante.
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

La URL de dirección especificada en el mensaje JSON la usa el agente de escucha para establecer el socket web con el objetivo de aceptar o rechazar el socket remitente.

#### <a name="accepting-the-socket"></a>Aceptación del socket
Para aceptarlo, el agente de escucha establece una conexión de socket web a la dirección especificada.

Tenga en cuenta que, durante el período de versión preliminar, el URI de dirección puede usar una dirección IP básica y se producirá un error cuando el certificado TLS que ha proporcionado el servidor valide esa dirección.
Esto se corregirá durante el periodo de versión preliminar.

Si el mensaje "accept" contiene un encabezado "Sec-WebSocket-Protocol", se prevé que el agente de escucha solo acepte el socket web si admite dicho protocolo y que defina el encabezado como el socket web establecido.

Lo mismo ocurre con el encabezado "Sec-WebSocket-Extensions". Si el marco admite una extensión, es necesario establecer el encabezado en la respuesta del lado *servidor* del protocolo de enlace "Sec-WebSocket-Extensions" que se necesita para la extensión.

La URL debe utilizarse tal cual para establecer el socket de aceptación, pero debe contener los parámetros siguientes:

| Parámetro | Obligatorio | Descripción |
| --- | --- | --- |
| sb-hc-action |yes |Para aceptar un socket, el parámetro tiene que ser `sb-hc-action=accept`. |
| {path} |Sí |(vea el párrafo siguiente) |
| sb-hc-id |No |Vea la descripción anterior del **identificador**. |

`{path}` es la ruta de acceso del espacio de nombres codificado para la dirección URL de la conexión híbrida preconfigurada en la que se registrará este agente de escucha. Esta expresión se anexa a la parte de la ruta de acceso `$hc/` fija. 

La expresión de ruta de acceso puede ampliarse con un sufijo y una expresión de cadena de consulta que siga el nombre registrado después de una barra diagonal de separación. Esto permite que el cliente remitente pase los argumentos de envío al agente de escucha receptor cuando no es posible incluir encabezados HTTP. La expectativa es que el marco del agente de escucha analizará la parte de ruta de acceso fija y el nombre registrado de la ruta de acceso, y hará el resto, posiblemente sin ningún argumento de cadena de consulta con el prefijo "sb-"; disponible para la aplicación para decidir si acepta la conexión.

Para más información, vea la siguiente sección "Protocolo del remitente".

Si hay un error, el servicio puede responder de esta forma:

| Código | Error | Descripción |
| --- | --- | --- |
| 403 |Prohibido |La URL no es válida. |
| 500 |Error interno |Se produjo un error en el servicio. |

Cuando se establece la conexión, el servidor apagará el socket web cuando se apague el socket web remitente, o con el siguiente estado:

| Estado de WS | Descripción |
| --- | --- |
| 1001 |El cliente remitente cierra la conexión. |
| 1001 |La ruta de acceso de Conexión híbrida se ha eliminado o deshabilitado. |
| 1008 |El token de seguridad ha expirado y, por tanto, se ha infringido la directiva de autorización. |
| 1011 |Se ha producido un error en el servicio. |

#### <a name="rejecting-the-socket"></a>Rechazo del socket
Para rechazar el socket después de inspeccionar el mensaje "accept", se necesita un protocolo de enlace similar para que el código y la descripción del estado que comunican el motivo del rechazo pueden enviarse al remitente.

La opción de diseño de protocolo que presentamos en este documento es un protocolo de enlace de socket web (que está pensado para que termine en un estado de error definido). De esta forma, las implementaciones del cliente del agente de escucha pueden seguir dependiendo de un cliente de socket web y no hace falta usar otro cliente HTTP básico.

Para rechazar el socket, el cliente toma el URI de dirección del mensaje "accept" y le anexa dos parámetros de cadena de consulta:

| Parámetro | Obligatorio | Descripción |
| --- | --- | --- |
| statusCode |yes |Código de estado HTTP numérico. |
| statusDescription |Sí |Motivo del rechazo en lenguaje natural. |

El URI que se obtiene se usa para establecer una conexión WebSocket; una vez más, tenga en cuenta que el certificado TLS puede no corresponderse con la dirección durante la versión preliminar, por lo que puede que se deshabilite la validación.

Cuando se complete correctamente, este protocolo de enlace producirá un error intencionadamente con un código de error HTTP 410, ya que no se ha establecido ningún socket web. Si se produce un error, estas son las opciones:

| Código | Error | Descripción |
| --- | --- | --- |
| 403 |Prohibido |La URL no es válida. |
| 500 |Error interno |Se ha producido un error en el servicio. |

### <a name="listener-token-renewal"></a>Renovación del token del agente de escucha
Cuando el token del agente de escucha está a punto de expirar, puede reemplazarse con el envío de un mensaje en un marco de texto al servicio a través del canal de control establecido. El mensaje contiene un objeto JSON denominado "renewToken", que define las siguientes propiedades:

* **token**: un token de acceso compartido de Service Bus válido y codificado para URL al espacio de nombres o a la conexión híbrida que concede el derecho de **escucha**.

#### <a name="renewtoken-message"></a>Mensaje renewToken
```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Si se produce un error en la validación del token, se deniega el acceso y el servicio en la nube apaga el socket web del canal de control con un error; de lo contrario, no se obtiene ninguna respuesta.

| Estado de WS | Descripción |
| --- | --- |
| 1008 |El token de seguridad ha expirado y, por tanto, se ha infringido la directiva de autorización. |

## <a name="sender-protocol"></a>Protocolo del remitente
El protocolo del remitente es idéntico a la forma en que se establece un agente de escucha.
El objetivo es dotar de la máxima transparencia al socket web de extremo a extremo. La dirección a la que conectarse es la misma que la del agente de escucha, pero el objeto "action" es diferente y el token necesita otro permiso:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*namespace-address* es el nombre de dominio completo del espacio de nombres de Azure Relay que hospeda la conexión híbrida, normalmente del formulario `{myname}.servicebus.windows.net`.

La solicitud puede contener más encabezados HTTP arbitrarios, incluidos los definidos por la aplicación. Todos los encabezados especificados se transmiten al agente de escucha y pueden encontrarse en el objeto "connectHeader" del mensaje de control "accept".

Las opciones de los parámetros de cadena de consulta son las siguientes:

| Parámetro | ¿Necesario? | Descripción |
| --- | --- | --- |
| sb-hc-action |Sí |Para el rol de remitente, el parámetro tiene que ser `action=connect`. |
| {path} |Sí |(vea el párrafo siguiente) |
| sb-hc-token |Sí\* |El agente de escucha necesita proporcionar un token de acceso compartido de Service Bus válido y codificado para URL al espacio de nombres o a la conexión híbrida que concede el derecho de **envío**. |
| sb-hc-id |No |Un id. opcional que permite realizar el seguimiento de diagnóstico completo y que puede usarlo el agente de escucha durante el protocolo de enlace de aceptación. |

{path} es el espacio de nombres codificado para URL de la conexión híbrida preconfigurada en la que se registrará este agente de escucha. La expresión de ruta de acceso puede ampliarse con un sufijo y una expresión de cadena de consulta para comunicarse de nuevo. Si la conexión híbrida se registra en la ruta de acceso "hyco", la expresión de ruta de acceso puede ser `hyco/suffix?param=value&...` seguida de los parámetros de cadena de consulta que se definen aquí. Una expresión completa puede ser:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

La expresión de ruta de acceso se pasa mediante el agente de escucha en el URI de dirección que se incluye en el mensaje de control "accept".

Si se produce un error en la conexión de socket web porque la ruta de acceso de conexión híbrida no está registrada, a que falta un token o no es válido, o a algún otro error, se proporcionarán los comentarios de los errores con el modelo de comentarios de estado normal de HTTP 1.1. La descripción de estado contendrá un id. de seguimiento de errores que se pueda comunicar al equipo de soporte técnico de Azure:

| Código | Error | Descripción |
| --- | --- | --- |
| 404 |No encontrado |`path` de la conexión híbrida no es válida o la dirección URL base es incorrecta. |
| 401 |No autorizado |Falta el token de seguridad, tiene una estructura incorrecta o no es válido. |
| 403 |Prohibido |El token de seguridad no es válido para esta ruta de acceso en esta acción. |
| 500 |Error interno |Se ha producido un error en el servicio. |

Si el servicio cierra intencionadamente la conexión de socket web después de haberse configurado inicialmente, el motivo se comunicará con un código de error adecuado de protocolo de socket web y un mensaje de error descriptivo, que también incluye un id. de seguimiento.

| Estado de WS | Descripción |
| --- | --- |
| 1000 |El agente de escucha cierra el socket. |
| 1001 |La ruta de acceso de Conexión híbrida se ha eliminado o deshabilitado. |
| 1008 |El token de seguridad ha expirado y, por tanto, se ha infringido la directiva de autorización. |
| 1011 |Se ha producido un error en el servicio. |

## <a name="next-steps"></a>Pasos siguientes
* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)


