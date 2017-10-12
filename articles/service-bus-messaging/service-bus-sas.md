---
title: "Autenticación en Azure Service Bus con firmas de acceso compartido | Microsoft Docs"
description: "Información general sobre la autenticación en Service Bus con Firma de acceso compartido, detalles de la autenticación con SAS en Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Autenticación en Service Bus con Firmas de acceso compartido

Las *firmas de acceso compartido* (SAS) son el mecanismo de seguridad principal de la mensajería de Service Bus. En este artículo se tratan las firmas acceso compartido, cómo funcionan y cómo usarlas independientemente de la plataforma.

La autenticación con SAS permite a las aplicaciones autenticarse en Service Bus mediante una clave de acceso configurada en el espacio de nombres o en la entidad de mensajería (cola o tema) al que se asocian derechos específicos. A continuación, puede usar esta clave para generar un token SAS que a su vez, los clientes pueden usar para autenticarse en el Bus de servicio.

La compatibilidad con la autenticación SAS se incluye en el SDK de Azure 2.0 y versiones posteriores.

## <a name="overview-of-sas"></a>Información general de SAS

Firmas de acceso compartido son un mecanismo de autenticación basado en URI y valores hash seguros SHA-256. SAS es un mecanismo muy eficaz que se usa por todos los servicios del Bus de servicio. En el uso real, SAS tiene dos componentes: una *directiva de acceso compartido* y una *firma de acceso compartido* (a menudo denominada *token*).

La autenticación SAS en el Bus de servicio implica la configuración de una clave criptográfica con derechos asociados en un recurso del Bus de servicio. Los clientes reclaman acceso a los recursos del Bus de servicio mediante la presentación de un token SAS. Este token consta del URI del recurso al que se accede y una caducidad firmada con la clave configurada.

Puede configurar reglas de autorización de firma de acceso compartido en [retransmisiones](service-bus-fundamentals-hybrid-solutions.md#relays), [colas](service-bus-fundamentals-hybrid-solutions.md#queues) y [temas](service-bus-fundamentals-hybrid-solutions.md#topics) de Service Bus.

La autenticación SAS usa los siguientes elementos:

* [Regla de autorización de acceso compartido](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): clave criptográfica principal de 256 bits en representación Base64, una clave secundaria opcional y un nombre de clave y derechos asociados (una colección de derechos de *escucha*, *envío* o *administración*).
* [Firma de acceso compartido](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) : generado con el HMAC-SHA256 de una cadena de recursos, formada por el URI del recurso al que se accede y una caducidad, con la clave criptográfica. La firma y otros elementos que se describen en las secciones siguientes tienen formato de cadena para formar el token SAS.

## <a name="shared-access-policy"></a>Directiva de acceso compartido

Entre otras cosas, es importante entender que SAS empieza con una directiva. Para cada directiva, decida tres tipos de información: **nombre**, **ámbito** y **permisos**. El **nombre** es solo eso; un nombre único dentro de ese ámbito. El ámbito es bastante sencillo: es el URI del recurso en cuestión. Para un espacio de nombres de Service Bus, el ámbito es el nombre de dominio completo (FQDN), como `https://<yournamespace>.servicebus.windows.net/`.

Los permisos disponibles para una directiva son en gran parte explicativos:

* Los métodos Send
* Escuchar
* Administrar

Después de crear la directiva, se le asigna una *clave principal* y una *clave secundaria*. Son claves de alta seguridad criptográfica. No las pierda ni las revele; siempre estarán disponibles en [Azure Portal][Azure portal]. Puede usar cualquiera de las claves generadas y regenerarlas en cualquier momento. Sin embargo, si regenera o cambia la clave principal en la directiva, se invalidará cualquier firma de acceso compartido creada a partir de ella.

Cuando se crea un espacio de nombres de Service Bus, se crea automáticamente una directiva para todo el espacio de nombres denominado **RootManageSharedAccessKey** y esta directiva tiene todos los permisos. No inicia sesión como **raíz**; por tanto, no use esta directiva a menos que exista realmente una buena razón. Puede crear directivas adicionales en la pestaña **Configurar** para el espacio de nombres en el portal. Es importante tener en cuenta que un nivel de árbol único en Service Bus (espacio de nombres, cola, etc.) solo puede tener hasta 12 directivas asociadas.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuración de la autenticación de firma de acceso compartido
Puede configurar la regla [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) en los espacios de nombres, las colas o los temas del Bus de servicio. La configuración de una regla [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) en una suscripción del Bus de servicio no se admite, pero puede usar las reglas configuradas en un espacio de nombres o un tema para asegurar el acceso a las suscripciones. Para ver un ejemplo funcional que ilustra este procedimiento, consulte el ejemplo [Uso de la autenticación de firma de acceso compartido (SAS) con suscripciones del Bus de servicio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

Puede configurarse un máximo de 12 reglas en un espacio de nombres, una cola o un tema del Bus de servicio. Las reglas que se configuran en un espacio de nombres del Bus de servicio se aplican a todas las entidades en ese espacio de nombres.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

En esta ilustración, las reglas de autorización *manageRuleNS*, *sendRuleNS* y *listenRuleNS* se aplican tanto a la cola Q1 como al tema T1, mientras que *listenRuleQ* y *sendRuleQ* se aplican solo a la cola Q1 y *sendRuleT* solo al tema T1.

Los parámetros clave de un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) son los siguientes:

| Parámetro | Descripción |
| --- | --- |
| *KeyName* |Una cadena que describe la regla de autorización. |
| *PrimaryKey* |Una clave principal de 256 bits con codificación base64 para firmar y validar el token SAS. |
| *SecondaryKey* |Una clave secundaria de 256 bits con codificación base64 para firmar y validar el token SAS. |
| *AccessRights* |Una lista de derechos de acceso concedidos por la regla de autorización. Estos derechos pueden ser cualquier colección de derechos de escucha, envío y administración. |

Cuando se aprovisiona un espacio de nombres de Service Bus, se crea de forma predeterminada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), con [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) establecido en **RootManageSharedAccessKey**.

## <a name="generate-a-shared-access-signature-token"></a>Generación de firmas de acceso compartido (tokens)

La propia directiva no es el token de acceso para el Bus de servicio. Es el objeto desde el que se genera el token de acceso, mediante la clave principal o la clave secundaria. Cualquier cliente que tenga acceso a las claves de firma especificadas en la regla de autorización de acceso compartido puede generar el token SAS. El token se genera elaborando cuidadosamente una cadena con el siguiente formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Donde `signature-string` es el hash SHA-256 del ámbito del token (**ámbito** como se describe en la sección anterior) con un CRLF anexado y una hora de expiración (en segundos desde la época: `00:00:00 UTC` el 1 de enero de 1970). 

> [!NOTE]
> Para evitar una hora de caducidad próxima del token, se recomienda que codifique el valor de hora de caducidad como mínimo como un entero de 32 bits sin signo o, preferiblemente, un entero largo (64 bits).  
> 
> 

El hash es similar al siguiente pseudocódigo y devuelve 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Los valores que no son de hash se encuentran en la cadena **SharedAccessSignature** para que el destinatario puede calcular el hash con los mismos parámetros, para asegurarse de que devuelve el mismo resultado. El URI especifica el ámbito y el nombre de la clave identifica la directiva que se usará para calcular el hash. Esto es importante desde una perspectiva de seguridad. Si la firma no coincide con la que el destinatario calcula (Bus de servicio), se denegará el acceso. En este punto puede estar seguro de que el remitente ha tenido acceso a la clave y que se le deberían haber concedido los derechos especificados en la directiva.

Tenga en cuenta que debe usar el URI de recurso codificado para esta operación. El URI de recurso es el URI completo del recurso del Bus de servicio al que se solicita el acceso. Por ejemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` o `sb://<namespace>.servicebus.windows.net/<entityPath>`; es decir, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

La regla de autorización de acceso compartido usada para firmar debe configurarse en la entidad especificada por este URI, o uno de sus primarios jerárquicos. Por ejemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` o `http://contoso.servicebus.windows.net` en el ejemplo anterior.

Un token SAS es válido para todos los recursos de `<resourceURI>` que se usen en `signature-string`.

[KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) en los token SAS se refiere al **keyName** de la regla de autorización de acceso compartido usado para generar el token.

El *URL-encoded-resourceURI* debe ser el mismo que el URI usado en la cadena para firmar durante el cálculo de la firma. Debe estar [codificado por porcentaje](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Se recomienda regenerar periódicamente las claves usadas en el objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Normalmente, las aplicaciones deben usar [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) para generar un token SAS. Al volver a generar las claves, debe reemplazar [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) por la antigua clave principal y generar una nueva clave como nueva clave principal. Esto le permite seguir usando tokens para la autorización que se emitieron con la clave principal anterior y que aún no hayan caducado.

Si se ve comprometida una clave y es necesario revocar las claves, puede volver a generar los valores de [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) y [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) de un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), y reemplazarlos por las nuevas claves. Este procedimiento invalida todos los tokens firmados con las claves antiguas.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Uso de la autenticación con firma de acceso compartido en el Bus de servicio

Los siguientes escenarios incluyen la configuración de reglas de autorización, la generación de tokens SAS y la autorización del cliente.

Para un ejemplo funcional completo de una aplicación del Bus de servicio que ilustra la configuración y usa la autorización SAS, consulte [Autenticación con firma de acceso compartido en Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Hay un ejemplo relacionado que ilustra el uso de reglas de autorización SAS configuradas en espacios de nombres o temas para proteger las suscripciones del Bus de servicio disponible aquí: [Uso de la autenticación de firma de acceso compartido (SAS) con suscripciones del Bus de servicio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Acceso a las reglas de autorización de acceso compartido en un espacio de nombres

Las operaciones en la raíz del espacio de nombres del Bus de servicio requieren la autenticación con certificados. Debe cargar un certificado de administración para la suscripción de Azure. Para cargar un certificado de administración, siga [estos pasos](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) con [Azure Portal][Azure portal]. Para obtener más información sobre los certificados de administración de Azure, consulte [Introducción a los certificados para los servicios en la nube de Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

El extremo para acceder a las reglas de autorización de acceso compartido en un espacio de nombres del Bus de servicio es el siguiente:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Para crear un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) en un espacio de nombres del Bus de servicio, ejecute una operación POST en este punto de conexión con la información de la regla serializada como JSON o XML. Por ejemplo:

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Del mismo modo, use una operación GET en el extremo para leer las reglas de autorización configuradas en el espacio de nombres.

Para actualizar o eliminar una regla de autorización específica, use el siguiente extremo:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Acceso a las reglas de autorización de acceso compartido en una entidad

Puede acceder a un objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado en una cola o un tema de Service Bus mediante la colección [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) en los objetos [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) o [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) correspondientes.

El código siguiente muestra cómo agregar reglas de autorización para una cola.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Uso de la autorización de la firma de acceso compartido

Las aplicaciones que usan el SDK de .NET de Azure con las bibliotecas .NET del Bus de servicio pueden usar la autorización SAS mediante la clase [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . El código siguiente muestra el uso del proveedor de token para enviar mensajes a una cola del Bus de servicio.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Las aplicaciones también pueden usar SAS para la autenticación mediante una cadena de conexión SAS en métodos que acepten cadenas de conexión.

Tenga en cuenta que para usar la autorización SAS con retransmisiones del Bus de servicio, puede usar claves SAS configuradas en el espacio de nombres del Bus de servicio. Si crea explícitamente una retransmisión en el espacio de nombres [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) con un objeto [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription), puede establecer las reglas SAS para dicha retransmisión. Para usar la autorización SAS con suscripciones del Bus de servicio, puede usar claves SAS configuradas en un espacio de nombres del Bus de servicio o en un tema.

## <a name="use-the-shared-access-signature-at-http-level"></a>Uso de la firma de acceso compartido (en el nivel HTTP)

Ahora que sabe cómo crear firmas de acceso compartido para cualquier entidad del Bus de servicio, estará listo para llevar a cabo una solicitud HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Recuerde que esto funciona para todo. Puede crear SAS para una cola, un tema o una suscripción. 

Si le da un token de SAS a un remitente o cliente, no tiene la clave directamente y no pueden invertir el hash para obtenerla. Como tal, tiene control sobre a lo que pueden tener acceso y durante cuánto tiempo. Algo importante de recordar es que si cambia la clave principal de la directiva, se invalidará cualquier firma de acceso compartido creada a partir de ella.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Uso de la firma de acceso compartido (en el nivel AMQP)

En la sección anterior, vimos cómo usar el token SAS con una solicitud HTTP POST para enviar datos al Bus de servicio. Como sabe, puede obtener acceso a Bus de servicio mediante Advanced Message Queuing Protocol (AMQP), que es el protocolo preferido por motivos de rendimiento en muchos escenarios. El uso de tokens SAS con AMQP se describe en el documento [Seguridad basada en notificaciones de AMQP, versión 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está en fase de borrador desde 2013, pero que es compatible con Azure actualmente.

Antes de comenzar a enviar datos a Service Bus, el publicador debe enviar el token de SAS de un mensaje de AMQP a un nodo de AMQP bien definido con el nombre **$cbs** (puede verlo como una cola especial usada por el servicio para adquirir y validar todos los tokens de SAS). El publicador debe especificar el campo **ReplyTo** en el mensaje de AMQP. Este es el nodo en el que el servicio contestará al publicador con el resultado de la validación del token (un patrón sencillo de solicitud/respuesta entre el publicador y el servicio). Este nodo de respuesta se crea "sobre la marcha" en lo que respecta a la "creación dinámica de nodo remoto", tal como describe la especificación de AMQP 1.0. Después de comprobar que el token SAS es válido, el publicador puede avanzar y comenzar a enviar datos al servicio.

Los pasos siguientes muestran cómo enviar el token de SAS con protocolo AMQP mediante la biblioteca [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Esto es útil si no puede usar el SDK oficial de Service Bus (por ejemplo, en WinRT, .Net Compact Framework, .Net Micro Framework y Mono) al desarrollar en C\#. Por supuesto, esta biblioteca es útil para comprender cómo funciona la seguridad basada en notificaciones en el nivel AMQP, como pudo observar en el nivel HTTP (con una solicitud HTTP POST y el token de SAS enviado en el encabezado "Autorización"). Si no necesita un conocimiento tan profundo sobre AMQP, puede usar el SDK oficial de Bus de servicio con aplicaciones de .NET Framework, que lo hará por usted.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

El método `PutCbsToken()` recibe la *conexión* (instancia de clase de conexión de AMQP, proporcionada por la [biblioteca .NET Lite de AMQP](https://github.com/Azure/amqpnetlite)) que representa la conexión TCP al servicio y el parámetro *sasToken* que es el token de SAS que se va a enviar. 

> [!NOTE]
> Es importante que la conexión se cree con el **mecanismo de autenticación SASL establecido en EXTERNAL** (y no con el valor PLAIN predeterminado con el nombre de usuario y la contraseña usados cuando no tiene que enviar el token de SAS).
> 
> 

A continuación, el publicador crea dos vínculos de AMQP para el envío del token de SAS y la recepción de la respuesta (resultado de validación del token) del servicio.

El mensaje de AMQP contiene una serie de propiedades y más información que un mensaje simple. El token de SAS es el cuerpo del mensaje (mediante su constructor). La propiedad **"ReplyTo"** se establece en el nombre del nodo para recibir el resultado de la validación en el vínculo del receptor (puede cambiar su nombre si lo desea y el servicio lo creará dinámicamente). El servicio usa las últimas tres propiedades personalizadas/de aplicación para indicar qué tipo de operación tiene que ejecutar. Como describe la especificación del borrador de CBS, deben ser el **nombre de operación** ("put-token"), el **tipo de token** (en este caso, "servicebus.windows.net:sastoken") y el **"nombre" de la audiencia** a la que se aplica el token (toda la entidad).

Después de enviar el token de SAS en el vínculo del remitente, el publicador debe leer la respuesta en el vínculo del receptor. La respuesta es un mensaje de AMQP simple con una propiedad de la aplicación denominada **"status-code"** que puede contener los mismos valores que un código de estado HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Derechos necesarios para realizar operaciones del Bus de servicio

La siguiente tabla muestra los derechos de acceso necesarios para realizar diversas operaciones en recursos del Bus de servicio.

| Operación | Solicitud necesaria | Ámbito de solicitud |
| --- | --- | --- |
| **Espacio de nombres** | | |
| Configurar la regla de autorización en un espacio de nombres |administración |Cualquier dirección de espacio de nombres |
| **Registro del servicio** | | |
| Enumerar directivas privadas |administración |Cualquier dirección de espacio de nombres |
| Empezar a escuchar en un espacio de nombres |Escuchar |Cualquier dirección de espacio de nombres |
| Enviar mensajes a un agente de escucha en un espacio de nombres |Los métodos Send |Cualquier dirección de espacio de nombres |
| **Cola** | | |
| Creación de una cola |administración |Cualquier dirección de espacio de nombres |
| Eliminación de una cola |administración |Cualquier dirección de cola válida |
| Enumerar colas |administración |/$Resources/Queues |
| Obtener la descripción de la cola |Administrar |Cualquier dirección de cola válida |
| Configurar la regla de autorización para una cola |administración |Cualquier dirección de cola válida |
| Enviar a la cola |Los métodos Send |Cualquier dirección de cola válida |
| mensajes de una cola |Escuchar |Cualquier dirección de cola válida |
| Abandone o complete los mensajes después de recibir el mensaje en el modo de bloqueo de información |Escuchar |Cualquier dirección de cola válida |
| Aplazar un mensaje para su recuperación posterior |Escuchar |Cualquier dirección de cola válida |
| Mensaje fallido |Escuchar |Cualquier dirección de cola válida |
| Obtener el estado asociado a una sesión de cola de mensajes |Escuchar |Cualquier dirección de cola válida |
| Establecer el estado asociado a una sesión de cola de mensajes |Escuchar |Cualquier dirección de cola válida |
| **Tema.** | | |
| de un tema |administración |Cualquier dirección de espacio de nombres |
| Eliminación de un tema |administración |Cualquier dirección de tema válida |
| Enumerar temas |administración |/$Resources/Topics |
| Obtener la descripción del tema |Administrar |Cualquier dirección de tema válida |
| Configurar la regla de autorización para un tema |administración |Cualquier dirección de tema válida |
| Enviar al tema |Los métodos Send |Cualquier dirección de tema válida |
| **Suscripción** | | |
| una suscripción |administración |Cualquier dirección de espacio de nombres |
| Eliminar suscripción |administración |../myTopic/Subscriptions/mySubscription |
| Enumerar suscripciones |administración |../myTopic/Subscriptions |
| Obtener la descripción de la suscripción |Administrar |../myTopic/Subscriptions/mySubscription |
| Abandone o complete los mensajes después de recibir el mensaje en el modo de bloqueo de información |Escuchar |../myTopic/Subscriptions/mySubscription |
| Aplazar un mensaje para su recuperación posterior |Escuchar |../myTopic/Subscriptions/mySubscription |
| Mensaje fallido |Escuchar |../myTopic/Subscriptions/mySubscription |
| Obtener el estado asociado a una sesión de tema |Escuchar |../myTopic/Subscriptions/mySubscription |
| Establecer el estado asociado a una sesión de tema |Escuchar |../myTopic/Subscriptions/mySubscription |
| **Reglas** | | |
| Crear una regla |administración |../myTopic/Subscriptions/mySubscription |
| Eliminar una regla |administración |../myTopic/Subscriptions/mySubscription |
| Enumerar reglas |Administrar o escuchar |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas.

* [Elementos fundamentales del Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Utilización de las colas del Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com