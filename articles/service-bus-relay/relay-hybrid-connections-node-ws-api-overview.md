---
title: "Introducción a las API de Node para Azure Relay | Microsoft Docs"
description: "Introducción a las API de Node para Relay"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 74e020992f5d841d9692dee2cb0bea97a9f27f8e
ms.lasthandoff: 03/27/2017

---

# <a name="relay-hybrid-connections-hyco-ws-node-api-overview"></a>Introducción a las API de Node hyco-ws para Conexiones híbridas de Relay

## <a name="overview"></a>Información general

El paquete de Node [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) para Conexiones híbridas de Azure Relay es una ampliación del paquete NPM ["ws"](https://www.npmjs.com/package/ws) en el que se basa. Este paquete vuelve a exportar todas las exportaciones de ese paquete base y agrega otras nuevas que habilitan la integración con la característica Conexiones híbridas del servicio Azure Relay. 

Las aplicaciones existentes con `require('ws')` pueden usar este paquete con `require('hyco-ws')` en su lugar, lo que también hace posibles escenarios híbridos en los que una aplicación puede escuchar conexiones WebSocket localmente desde "dentro del firewall", así como por medio de Conexiones híbridas, de forma simultánea.
  
## <a name="documentation"></a>Documentación

Las API están [documentadas en el paquete principal "ws"](https://github.com/websockets/ws/blob/master/doc/ws.md). En este documento se describe cómo este paquete difiere de esa línea de base. 

Las diferencias principales entre el paquete base y "hyco-ws" es que este último agrega una nueva clase de servidor, exportada por medio de `require('hyco-ws').RelayedServer`, y varios métodos auxiliares.

### <a name="package-helper-methods"></a>Métodos auxiliares del paquete

Hay varios métodos de utilidad disponibles en la exportación de paquete, a los que puede hacer referencia como sigue:

``` JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Los métodos auxiliares son para utilizarse con este paquete, pero también los puede usar un servidor de nodo para permitir que los clientes web o de dispositivo creen agentes de escucha o remitentes. El servidor utiliza estos métodos pasándoles URI que insertan tokens de corta duración. Estos URI también se pueden usar con pilas WebSocket comunes que no admiten que se establezcan encabezados HTTP para el protocolo de enlace WebSocket. Se admite la inserción de tokens de autorización en el URI principalmente para los escenarios de uso con biblioteca externa. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri
``` JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Crea un URI válido para el agente de escucha de conexión híbrida de Azure Relay para el espacio de nombres y la ruta de acceso dados. Este URI se puede usar con la versión de retransmisión de la clase WebSocketServer.

- **namespaceName** (obligatorio): el nombre de dominio completo del espacio de nombres de Azure Relay que se va a usar.
- **path** (obligatorio): el nombre de una conexión híbrida de Azure Relay existente en ese espacio de nombres.
- **token** (opcional): un token de acceso de Relay emitido antes que se inserta en el URI del agente de escucha (consulte el ejemplo siguiente).
- **id** (opcional): un identificador de seguimiento que habilita el seguimiento diagnóstico de solicitudes de principio a fin.

El valor **token** es opcional y solo debe usarse cuando no sea posible enviar encabezados HTTP junto con el protocolo de enlace WebSocket, como sucede con la pila WebSocket de W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri 
``` JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Crea un URI válido para envío de conexión híbrida de Azure Relay para el espacio de nombres y la ruta de acceso dados. Este URI se puede utilizar con cualquier cliente de WebSocket.

- **namespaceName** (obligatorio): el nombre de dominio completo del espacio de nombres de Azure Relay que se va a usar.
- **path** (obligatorio): el nombre de una conexión híbrida de Azure Relay existente en ese espacio de nombres.
- **token** (opcional): un token de acceso de Relay emitido antes que se inserta en el URI de envío (consulte el ejemplo siguiente).
- **id** (opcional): un identificador de seguimiento que habilita el seguimiento diagnóstico de solicitudes de principio a fin.

El valor **token** es opcional y solo debe usarse cuando no sea posible enviar encabezados HTTP junto con el protocolo de enlace WebSocket, como sucede con la pila WebSocket de W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 
``` JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Crea un token de firma de acceso compartido (SAS) de Azure Relay para el URI de destino, la regla de SAS y la clave de regla de SAS dados, el cual es válido para el número de segundos determinado o para una hora a partir del instante actual si se omite el argumento de expiración.

- **uri** (obligatorio): el URI para el que se emitirá el token. El URI se normaliza para usar el esquema HTTP y se quitará la información de cadena de consulta.
- **ruleName** (obligatorio): nombre de regla de SAS para la entidad representada por el URI dado o para el espacio de nombres representado por la parte de host del URI.
- **key** (obligatorio): clave válida para la regla de SAS. 
- **expirationSeconds** (opcional): número de segundos que transcurren hasta que el token generado deba expirar. 
                            El valor predeterminado es 1 hora (3600) si no se especifica.

El token emitido confiere los derechos asociados a la regla de SAS especificada para la duración dada.

#### <a name="appendrelaytoken"></a>appendRelayToken
``` JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Este método es funcionalmente equivalente al método **createRelayToken** documentado antes, pero devuelve el token anexado correctamente al URI de entrada.

### <a name="class-wsrelayedserver"></a>Clase ws.RelayedServer

La clase `hycows.RelayedServer` es una alternativa a la clase `ws.Server` que no escucha en la red local, sino que delega la escucha en el servicio Azure Relay.

Ambas clases son en su mayor parte compatibles con los contratos, lo que significa que una aplicación existente que use la clase `ws.Server` puede cambiarse para que use la versión retransmitida con bastante facilidad. Las diferencias principales residen en el constructor y en las opciones disponibles.

#### <a name="constructor"></a>Constructor  

``` JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server : ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

El constructor `RelayedServer` es compatible con un conjunto diferente de argumentos que `Server`, porque no es un agente de escucha independiente ni puede insertarse en un marco de trabajo de agente de escucha HTTP existente. Además, hay menos opciones disponibles porque la administración de WebSocket se delega en gran medida en el servicio Relay.

Argumentos del constructor:

- **server** (obligatorio): el URI completo para un nombre de conexión híbrida en la que se va a escuchar; normalmente se construye con el método auxiliar WebSocket.createRelayListenUri().
- **token** (obligatorio): este argumento contiene una cadena de token emitida previamente o una función de devolución de llamada a la que se puede llamar para obtener este tipo de cadena de token. Se prefiere la opción de devolución de llamada, puesto que permite la renovación del token.

#### <a name="events"></a>Eventos

Las instancias de `RelayedServer` emiten tres eventos que permiten controlar las solicitudes entrantes, establecer conexiones y detectar condiciones de error. Debe suscribirse al evento "connect" para controlar los mensajes. 

##### <a name="headers"></a>encabezados
``` JavaScript 
function(headers)
```

Se genera el evento "headers" justo antes de que se acepte una conexión entrante, lo que permite que se modifiquen los encabezados que se van a enviar al cliente. 

##### <a name="connection"></a>connection
``` JavaScript
function(socket)
```

Se emite cuando se acepta una nueva conexión de WebSocket. El objeto es de tipo `ws.WebSocket`, igual que con el paquete base.


##### <a name="error"></a>error
``` JavaScript
function(error)
```

Si el servidor subyacente emite un error, se reenvía aquí.  

#### <a name="helpers"></a>Aplicaciones auxiliares

Para simplificar el inicio de un servidor retransmitido y la suscripción inmediata a conexiones entrantes, el paquete expone una función auxiliar sencilla, que también se utiliza en los ejemplos, como se indica a continuación:

##### <a name="createrelayedlistener"></a>createRelayedListener

``` JavaScript
    var WebSocket = require('hyco-ws');

    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(JSON.parse(event.data));
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
``` 

var server = createRelayedServer([options], [connectCallback] )

Este método llama al constructor para crear una instancia de RelayedServer y después suscribe la devolución de llamada proporcionada al evento "connection".
 
##### <a name="relayedconnect"></a>relayedConnect

Con solo reflejar la función `createRelayedServer`, `relayedConnect` crea una conexión de cliente y se suscribe al evento "open" en el socket resultante.

``` JavaScript
    var uri = WebSocket.createRelaySendUri(ns, path);
    WebSocket.relayedConnect(
        uri,
        WebSocket.createRelayToken(uri, keyrule, key),
        function (socket) {
            ...
        }
    );
```

## <a name="next-steps"></a>Pasos siguientes
Para aprender más sobre Azure Relay, visite estos vínculos:
* [¿Qué es Relay de Azure?](relay-what-is-it.md)
* [Available Relay APIs ](relay-api-overview.md) (API de Relay disponibles)

