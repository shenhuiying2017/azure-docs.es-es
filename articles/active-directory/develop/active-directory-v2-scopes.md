---
title: "Ámbitos, permisos y consentimiento de Azure Active Directory v2.0 | Microsoft Docs"
description: "Descripción de la autorización del punto de conexión v2.0 de Azure AD, que incluye los ámbitos, los permisos y el consentimiento."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 04869a7627ecb3e6a0d11733fae7da2ecb04ed51
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Ámbitos, permisos y consentimiento en el punto de conexión v2.0 de Azure Active Directory
Las aplicaciones que se integran con Azure Active Directory (Azure AD) siguen un modelo de autorización que ofrece a los usuarios control sobre el modo en que una aplicación puede acceder a sus datos. La implementación v2.0 del modelo de implementación se ha actualizado, y cambia el modo en que una aplicación debe interactuar con Azure AD. En este artículo se tratan los conceptos básicos de este modelo de autorización, incluidos los ámbitos, los permisos y el consentimiento.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de esta versión](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Permisos y ámbitos
Azure AD implementa el protocolo de autorización [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 es un método a través del cual una aplicación de terceros puede acceder a recursos hospedados en Web en nombre de un usuario. Cualquier recurso hospedado en Web que se integre con Azure AD tiene un identificador de recursos o *URI de id. de aplicación*. Por ejemplo, algunos de los recursos de Microsoft hospedados en la web incluyen:

* La API de Correo unificado de Office 365: `https://outlook.office.com`
* La API Graph de Azure AD: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Ocurre lo mismo con cualquier recurso de terceros integrado con Azure AD. Cualquiera de estos recursos también puede definir un conjunto de permisos que se puede usar para dividir la funcionalidad de ese recurso en fragmentos menores. Por ejemplo, [Microsoft Graph](https://graph.microsoft.io) tiene permisos definidos para realizar, entre otras, las tareas siguientes:

* Leer el calendario de un usuario
* Escribir en el calendario de un usuario
* Enviar correo como un usuario

Mediante la definición de estos tipos de permisos, el recurso tiene control específico sobre sus datos y el modo en que se exponen. Una aplicación de terceros puede solicitar estos permisos a un usuario de la aplicación. El usuario de la aplicación debe aprobar los permisos para que la aplicación pueda actuar en su nombre. Al fragmentar la funcionalidad del recurso en conjuntos de permisos más pequeños, se pueden crear aplicaciones de terceros para solicitar solo los permisos concretos que necesitan para realizar sus tareas. Los usuarios de la aplicación pueden saber exactamente cómo una aplicación usará sus datos, así pueden estar más seguros de que la aplicación no se comporta de forma malintencionada.

En Azure AD y OAuth, estos tipos de permisos se denominan *ámbitos*. En ocasiones también se conocen como *oAuth2Permissions*. Un ámbito se representa en Azure AD como un valor de cadena. Al igual que en el ejemplo de Microsoft Graph, el valor de ámbito para cada permiso es:

* Leer el calendario de un usuario mediante `Calendar.Read`
* Escribir en el calendario de un usuario mediante `Mail.ReadWrite`
* Enviar correo electrónico con un usuario mediante `Mail.Send`

Una aplicación puede solicitar estos permisos mediante la especificación de los ámbitos en las solicitudes al punto de conexión v2.0.

## <a name="openid-connect-scopes"></a>Ámbitos de OpenID Connect
La implementación v2.0 de OpenID Connect tiene unos cuantos ámbitos bien definidos que no se aplican a ningún recurso determinado: `openid`, `email`, `profile` y `offline_access`.

### <a name="openid"></a>openid
Si una aplicación realiza el inicio de sesión mediante [OpenID Connect](active-directory-v2-protocols.md), debe solicitar el ámbito `openid`. El ámbito `openid` aparece en la pantalla de consentimiento de la cuenta profesional como el permiso "Sign you in" (Iniciar sesión) y en la pantalla de consentimiento de la cuenta personal de Microsoft como el permiso "View your profile and connect to apps and services using your Microsoft account" (Ver el perfil y conectarse a las aplicaciones y servicios con la cuenta de Microsoft). Este permiso permite que una aplicación reciba un identificador único para el usuario en forma de la notificación `sub` . También ofrece acceso de la aplicación al punto de conexión UserInfo. El ámbito `openid` se puede usar en el punto de conexión del token v.2.0 para adquirir tokens de identificador que se pueden emplear para proteger las llamadas HTTP entre distintos componentes de una aplicación.

### <a name="email"></a>email
El ámbito `email` puede usarse con el ámbito `openid` y cualquier otro. Proporciona acceso de la aplicación a la dirección de correo electrónico principal del usuario en forma de la notificación `email`. La notificación `email` solo se incluye en los tokens si hay una dirección de correo electrónico asociada a la cuenta de usuario, que no siempre es el caso. Si usa el ámbito `email`, la aplicación debe estar preparada para controlar los casos en los que la notificación `email` no exista en el token.

### <a name="profile"></a>Perfil
El ámbito `profile` puede usarse con el ámbito `openid` y cualquier otro. Proporciona acceso de la aplicación a una cantidad considerable de información sobre el usuario. Por ejemplo, el nombre propio del usuario, el apellido, el nombre de usuario preferido o el id. de objeto, entre otros datos. Para ver una lista completa de las notificaciones de perfil disponibles en el parámetro id_tokens para un usuario específico, consulte la [referencia de los tokens de v2.0](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
El ámbito [`offline_access` ](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) proporciona acceso de la aplicación a recursos en nombre del usuario durante un periodo de tiempo prolongado. En la página de consentimiento de la cuenta profesional, este ámbito aparecerá como el permiso "Acceder a sus datos en cualquier momento". En la página de consentimiento de la cuenta personal de Microsoft, aparece como el permiso "Obtener acceso a tu información en cualquier momento". Cuando un usuario aprueba el ámbito `offline_access`, la aplicación puede recibir tokens de actualización del punto de conexión del token v2.0. Los tokens de actualización tienen una duración larga. La aplicación puede obtener nuevos tokens de acceso cuando expiren los antiguos.

Si la aplicación no solicita el ámbito `offline_access`, no recibirá tokens de actualización. Esto significa que cuando se canjea un código de autorización del [flujo del código de autorización de OAuth 2.0](active-directory-v2-protocols.md), solo se recibirá un token de acceso del punto de conexión `/token`. El token de acceso es válido durante un breve período de tiempo. Normalmente, expira al cabo de una hora. En ese momento, la aplicación tiene que redirigir al usuario de vuelta al punto de conexión `/authorize` para que obtenga un nuevo código de autorización. Durante esta redirección y, en función del tipo de aplicación, puede que el usuario tenga que volver a escribir sus credenciales o dar de nuevo el consentimiento a permisos.

Para más información sobre cómo obtener y usar tokens de actualización, consulte la [referencia del protocolo v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Solicitud de consentimiento de usuario individual
En una solicitud de autorización de [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md), una aplicación puede solicitar los permisos que necesita mediante el parámetro de consulta `scope`. Por ejemplo, cuando un usuario inicia sesión en una aplicación, la aplicación envía una solicitud como la del ejemplo siguiente (se han agregado saltos de línea para una mayor legibilidad):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

El parámetro `scope` es una lista separada por espacios que incluye los ámbitos que solicita la aplicación. Cada ámbito individual se indica anexando el valor del ámbito al identificador del recurso (URI del id. de aplicación). En la solicitud de ejemplo, la aplicación necesita permiso para leer el calendario del usuario y enviar correo electrónico en nombre del usuario.

Después de que el usuario escribe sus credenciales, el punto de conexión v2.0 busca un registro que coincida con el *consentimiento del usuario*. Si el usuario no ha dado su consentimiento a ninguno de los permisos solicitados en el pasado, el punto de conexión v2.0 le pide al usuario que conceda los permisos solicitados.

![Consentimiento de la cuenta de trabajo](../../media/active-directory-v2-flows/work_account_consent.png)

Cuando el usuario aprueba el permiso, el consentimiento se registra, así el usuario no tiene que volver a dar su consentimiento en los sucesivos inicios de sesión.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitud de consentimiento para un inquilino al completo
A menudo, cuando una organización adquiere una licencia o una suscripción de una aplicación, quiere aprovisionar totalmente la aplicación para sus empleados. Como parte de este proceso, el administrador puede conceder consentimiento a la aplicación para que actúe en nombre de cualquier empleado. Si el administrador concede el consentimiento del inquilino entero, los empleados de la organización no verán una página de consentimiento para la aplicación.

Para solicitar el consentimiento de todos los usuarios de un inquilino, la aplicación puede usar el punto de conexión de consentimiento del administrador.

## <a name="admin-restricted-scopes"></a>Ámbitos restringidos para los administradores
Algunos permisos de privilegios elevados del ecosistema de Microsoft se pueden establecer en *restringido para los administradores*. Algunos ejemplos de estos tipos de ámbitos son los siguientes permisos:

* Leer datos del directorio de una organización mediante `Directory.Read`
* Escribir datos en el directorio de una organización mediante `Directory.ReadWrite`
* Leer grupos de seguridad del directorio de una organización mediante: `Groups.Read.All`

Aunque un usuario consumidor podría conceder acceso de la aplicación a este tipo de datos, los usuarios de la organización tienen la restricción de no conceder acceso al mismo conjunto de datos confidenciales de la empresa. Si la aplicación solicita acceso a uno de estos permisos desde un usuario de la organización, el usuario recibe un mensaje de error que indica que no está autorizado para dar el consentimiento a los permisos de la aplicación.

Si la aplicación necesita acceso a los ámbitos restringidos para los administradores en una organización, debe solicitarlos directamente a un administrador de la empresa, también mediante el punto de conexión de consentimiento del administrador, que se describe a continuación.

Cuando un administrador concede estos permisos mediante el punto de conexión de consentimiento del administrador, el consentimiento se concede a todos los usuarios del inquilino.

## <a name="using-the-admin-consent-endpoint"></a>Uso del punto de conexión de consentimiento del administrador
Si sigue estos pasos, la aplicación puede recopilar permisos para todos los usuarios de un inquilino, incluidos los ámbitos restringido para los administradores. Para ver un ejemplo de código que implementa lo pasos, consulte el [ejemplo de ámbitos restringidos para los administradores](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitud de los permisos en el portal de registro de aplicaciones
1. Vaya a la aplicación en el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o [cree una aplicación](active-directory-v2-app-registration.md), si todavía no tiene una.
2. Busque la sección de **permisos de Microsoft Graph** y agregue los permisos que necesite la aplicación.
3. No olvide **guardar** el registro de la aplicación.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendación: inicie la sesión del usuario en la aplicación
Normalmente, cuando se compila una aplicación que usa el punto de conexión de consentimiento del administrador, la aplicación necesita una página o una vista en la que el administrador pueda aprobar los permisos de la aplicación. Esta página puede ser parte del flujo de inicio de sesión de la aplicación o de la configuración de la aplicación, o bien puede ser un flujo de "conexión" dedicado. En muchos casos, tiene sentido que la aplicación muestre esta vista de conexión solo después de que un usuario haya iniciado sesión con una cuenta Microsoft profesional o educativa.

Cuando inicia la sesión del usuario en la aplicación, puede identificar la organización a la que pertenece el administrador antes de pedirle que apruebe los permisos necesarios. Aunque no es estrictamente necesario, puede ayudarle a crear una experiencia más intuitiva para los usuarios de la organización. Para iniciar la sesión del usuario, siga nuestros [tutoriales del protocolo de la versión 2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitud de los permisos de un administrador de directorios
Cuando esté listo para solicitar permisos al administrador de la organización, puede redirigir al usuario al *punto de conexión de consentimiento del administrador* v2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parámetro | Condición | Descripción |
| --- | --- | --- |
| tenant |Obligatorio |El inquilino de directorio al que quiere solicitar permiso. Se puede proporcionar en formato de nombre descriptivo o GUID. |
| client_id |Obligatorio |El identificador de aplicación que el [portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) asignó a la aplicación. |
| redirect_uri |Obligatorio |El URI de redirección adonde desea que se envíe la respuesta para que la controle la aplicación. Debe coincidir exactamente con uno de los identificadores URI de redirección que registró el Portal de registro de aplicaciones. |
| state |Recomendado |Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Use el estado para codificar información sobre el estado del usuario en la aplicación antes de que se produzca la solicitud de autenticación, por ejemplo, la página o vista en la que estaba. |

En este momento, Azure AD requiere que un administrador de inquilinos inicie sesión para completar la solicitud. Se pedirá al administrador que apruebe todos los permisos que ha solicitado para la aplicación en el Portal de registro de aplicaciones.

#### <a name="successful-response"></a>Respuesta correcta
Si el administrador aprueba los permisos para la aplicación, la respuesta correcta tendrá un aspecto similar al siguiente:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parámetro | Description |
| --- | --- | --- |
| tenant |El inquilino del directorio que concedió los permisos solicitados, en formato GUID. |
| state |Un valor incluido en la solicitud que también se devolverá en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. El estado se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| admin_consent |Se establecerá en **true**. |

#### <a name="error-response"></a>Respuesta de error
Si el administrador no aprueba los permisos de la aplicación, la respuesta de error tendrá el aspecto siguiente:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parámetro | Description |
| --- | --- | --- |
| error |Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error_description |Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error. |

Cuando haya recibido una respuesta correcta del punto de conexión de consentimiento del administrador, la aplicación habrá obtenido los permisos solicitados. Ahora puede solicitar un token para el recurso que desee.

## <a name="using-permissions"></a>Uso de permisos
Después de que el usuario da su consentimiento para los permisos de la aplicación, la aplicación puede obtener tokens de acceso que representan los permisos de la aplicación para acceder a un recurso de alguna manera. Un token de acceso solo se puede usar para un recurso, pero dentro del token de acceso están todos los permisos codificados que se han concedido a la aplicación para ese recurso. Para adquirir un token de acceso, la aplicación puede realizar una solicitud al punto de conexión del token v2.0, como esta:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Puede usar el token de acceso resultante en las solicitudes HTTP al recurso. Dicho token indica al recurso de forma confiable que la aplicación tiene el permiso apropiado para realizar una tarea específica.  

Para más información sobre el protocolo OAuth 2.0 y cómo obtener tokens de acceso, consulte la [referencia de protocolo del punto de conexión v2.0](active-directory-v2-protocols.md).
