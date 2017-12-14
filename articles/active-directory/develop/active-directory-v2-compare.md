---
title: "¿Qué hay de diferente en el punto de conexión de Azure AD v2.0? | Microsoft Docs"
description: "Una comparación entre el punto de conexión original de Azure AD y el punto de conexión v2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 4216c2215f494a81935cd4cdca52427b0cbac143
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="whats-different-about-the-v20-endpoint"></a>¿Qué hay diferente en el punto de conexión v2.0?
Si está familiarizado con Azure Active Directory o ha integrado aplicaciones con Azure AD en el pasado, puede que haya algunas diferencias en el punto de conexión v2.0 que no se espera.  Este documento describe esas diferencias para su comprensión.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0.  Para determinar si debe utilizar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Cuentas de Microsoft y cuentas de Azure AD
El punto de conexión v2.0 permite a los desarrolladores crear aplicaciones que aceptan el inicio de sesión tanto desde cuentas Microsoft como desde cuentas de Azure AD mediante un punto de conexión de autenticación único.  Esto le ofrece la posibilidad de crear su aplicación completamente independiente de la cuenta; puede no conocer el tipo de cuenta con el que el usuario inicia sesión.  Por supuesto, *puede* hacer que la aplicación reconozca el tipo de cuenta que se usa en una sesión determinada, aunque no es necesario.

Por ejemplo, si su aplicación llama a [Microsoft Graph](https://graph.microsoft.io), los usuarios de empresa tendrán a su disposición algunos datos y funciones adicionales, como los sitios de SharePoint o datos de Directory.  Pero para muchas acciones, como [leer el correo de un usuario](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), el código se puede crear exactamente de la misma manera tanto para las cuentas de Microsoft como para las cuentas de Azure AD.  

La integración de su aplicación con las cuentas de Azure AD y las cuentas de Microsoft ahora es un proceso sencillo.  Puede usar un conjunto único de extremos, una sola biblioteca y un registro de aplicaciones único para obtener acceso tanto al mundo empresarial como al de los consumidores.  Para obtener más información sobre el punto de conexión v2.0, consulte [la información general](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nuevo portal de registro de aplicaciones
Para registrar una aplicación que funciona con el punto de conexión v2.0, debe usar un nuevo portal de registro de aplicaciones: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  En este portal podrá obtener un identificador de aplicación y personalizar la apariencia de la página de inicio de sesión de la aplicación, entre otras cuestiones.  Todo lo que necesita para acceder al portal es una cuenta Microsoft, ya sea personal o profesional/educativa.

## <a name="one-app-id-for-all-platforms"></a>Un solo identificador de aplicación para todas las plataformas
Si ha usado Azure Active Directory, es posible que haya registrado varias aplicaciones diferentes para un único proyecto.  Por ejemplo, si creó un sitio web y una aplicación de iOS, debía registrarlas por separado, con dos identificadores de aplicación diferentes. El portal de registro de aplicaciones de Azure AD le obliga a hacer esta distinción durante el registro:

![Interfaz de usuario de registro de aplicación antigua](../../media/active-directory-v2-flows/old_app_registration.PNG)

De manera similar, si tenía un sitio web y una API web de back-end, puede que haya registrado cada uno como una aplicación independiente en Azure AD.  O bien, si tenía una aplicación de iOS y una aplicación Android, puede que también haya registrado dos aplicaciones diferentes.  El registro de cada componente de una aplicación ha provocado algunos comportamientos inesperados para los desarrolladores y sus clientes:

* Cada componente aparecía como una aplicación distinta en el inquilino de Azure Active Directory de cada cliente.
* Cuando el administrador de un inquilino intentase aplicar una directiva a la aplicación, administrar el acceso a la aplicación o eliminar la aplicación, tendría que hacerlo para cada componente de la aplicación.
* Cuando los clientes den su consentimiento a una aplicación, cada componente aparecería en la pantalla de consentimiento como una aplicación distinta.

Con el punto de conexión v2.0, ahora puede registrar todos los componentes del proyecto como un único registro de aplicaciones y usar un único identificador de aplicación para todo el proyecto.  Puede agregar varias "plataformas" a cada proyecto y ofrece los datos adecuados para cada plataforma que agregue.  Por supuesto, puede crear tantas aplicaciones como desee, en función de sus requisitos, pero para la mayoría de los casos solo será necesario un id. de aplicación.

Nuestro objetivo es que esto dará lugar a una experiencia de desarrollo y administración de aplicaciones más simplificada y que creará una vista más consolidada de un proyecto único en el que podría trabajar.

## <a name="scopes-not-resources"></a>Ámbitos, no recursos
En Azure Active Directory, una aplicación puede comportarse como **recurso** o como destinatario de tokens.  Un recurso puede definir varios **ámbitos** o **oAuth2Permissions** que comprende, lo que permite a las aplicaciones cliente solicitar tokens para ese recurso para un conjunto determinado de ámbitos.  Piense en la API de Azure AD Graph como ejemplo de un recurso:

* Identificador de recursos o `AppID URI`: `https://graph.windows.net/`
* Ámbitos o `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etc.  

Todo esto se cumple para el punto de conexión v2.0.  Una aplicación todavía se puede comportar como recurso, definir ámbitos y ser identificada por un URI.  Las aplicaciones cliente todavía pueden solicitar acceso a esos ámbitos.  Sin embargo, ha cambiado la manera en que un cliente solicita esos permisos.  En el pasado, una solicitud de autorización de OAuth 2.0 para Azure AD podría haber tenido un aspecto similar al siguiente:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

donde el parámetro **resource** indicaba para qué recurso la aplicación cliente solicitaba autorización.  Azure AD calculaba los permisos requeridos por la aplicación en función de la configuración estática en Azure Portal y emitía tokens en consecuencia.  Ahora, la misma solicitud de autorización de OAuth 2.0 tiene un aspecto similar al siguiente:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

donde el parámetro **scope** indica para qué recursos y permisos está solicitando autorización la aplicación. El recurso deseado sigue estando muy presente en la solicitud; simplemente está incluido en cada uno de los valores del parámetro scope.  El uso del parámetro scope de esta manera permite que el punto de conexión v2.0 respete en mayor medida la especificación de OAuth 2.0 y se alinee más estrechamente con prácticas comunes del sector.  También permite que las aplicaciones realicen [consentimiento incremental](#incremental-and-dynamic-consent), que se describe en la siguiente sección.

## <a name="incremental-and-dynamic-consent"></a>Consentimiento incremental y dinámico
Las aplicaciones registradas anteriormente en Azure AD tenían que especificar sus permisos de OAuth 2.0 necesarios en Azure Portal, en el momento de la creación de las aplicaciones:

![Interfaz de usuario de registro de permisos](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

Los permisos que una aplicación requerían se configuraban **estáticamente**.  Aunque esto permitía que la configuración de la aplicación existiera en Azure Portal y mantenía bien el aspecto del código, presentaba algunos problemas para los desarrolladores:

* Una aplicación debía conocer todos los permisos que consumiría alguna vez durante la creación de la aplicación.  Agregar permisos con el tiempo era un proceso difícil.
* Una aplicación debía conocer todos los recursos a los que tendría acceso alguna vez durante por adelantado.  Era difícil crear aplicaciones que podrían tener acceso a un número arbitrario de recursos.
* Una aplicación tenía que solicitar todos los permisos que podría necesitar alguna vez tras el primer inicio de sesión del usuario.  En algunos casos, esto llevaría a una lista muy larga de permisos, lo que desanimaría a los usuarios finales de aprobar el acceso de la aplicación en el inicio de sesión inicial.

Con el punto de conexión v2.0, puede especificar los permisos que necesita su aplicación **dinámicamente**en tiempo de ejecución durante el uso normal de su aplicación.  Para ello, puede especificar los ámbitos que necesita su aplicación en un momento determinado en el tiempo incluyéndolos en el parámetro `scope` de una solicitud de autorización:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Lo anterior solicita permiso para que la aplicación lea los datos de directorio de un usuario de Azure AD, así como escribir datos en su directorio.  Si el usuario ha dado su consentimiento para esos permisos en el pasado para esta aplicación concreta, simplemente introducirán sus credenciales y habrán iniciado sesión en la aplicación.  Si el usuario no ha dado su consentimiento a cualquiera de estos permisos, el extremo de v2.0 pedirá al usuario su consentimiento para esos permisos.  Para obtener más información, puede leer sobre [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md).

Permitir que una aplicación solicite permisos dinámicamente mediante el parámetro `scope` le da un control total sobre la experiencia del usuario.  Si lo desea, puede elegir adelantar su experiencia de consentimiento y pedir todos los permisos en una solicitud de autorización inicial.  O bien, si su aplicación requiere un gran número de permisos, puede elegir recopilarlos del usuario de forma incremental, a medida que intentan usar determinadas características de la aplicación con el tiempo.

## <a name="well-known-scopes"></a>Ámbitos conocidos
#### <a name="offline-access"></a>Acceso sin conexión
Es posible que las aplicaciones que usan el punto de conexión v2.0 requieran el uso de un nuevo permiso conocido en las aplicaciones: el ámbito `offline_access`.  Todas las aplicaciones deberán solicitar este permiso si necesitan tener acceso a los recursos en nombre de un usuario durante un período de tiempo prolongado, incluso cuando es posible que el usuario no haya estado usando la aplicación activamente.  El ámbito `offline_access` le aparecerá al usuario en cuadros de diálogo de consentimiento como "Obtener acceso a los datos sin conexión", que el usuario debe aceptar.  Solicitar el permiso `offline_access` permitirá a su aplicación web recibir refresh_tokens de OAuth 2.0 desde el extremo de v2.0.  Los refresh_tokens son de larga duración y se pueden intercambiar por nuevos access_tokens de OAuth 2.0 durante largos períodos de acceso.  

Si la aplicación no solicita el ámbito `offline_access`, no recibirá tokens de actualización (refresh_tokens).  Esto significa que cuando se canjea un código de autorización (authorization_code) del flujo del código de autorización de OAuth 2.0, solo se recibirá un token de acceso (access_token) desde el punto de conexión `/token`.  Ese token de acceso seguirá siendo válido durante un breve período de tiempo (normalmente una hora), pero finalmente caducará.  En ese momento, la aplicación tendrá que redirigir al usuario de nuevo al extremo de `/authorize` para recuperar un nuevo authorization_code.  Durante esta redirección, es posible o no que el usuario necesite escribir sus credenciales de nuevo o volver a dar el consentimiento a permisos, según el tipo de aplicación.

Para más información acerca de OAuth 2.0, refresh_tokens y access_tokens, consulte la [referencia del protocolo v2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile-and-email"></a>OpenID, perfil y correo electrónico
Tradicionalmente, el flujo más básico de inicio de sesión de OpenID Connect en Azure Active Directory ofrecería gran cantidad de información sobre el usuario en el id_token resultante.  Las notificaciones de un id_token pueden incluir el nombre de usuario, el nombre de usuario preferido, la dirección de correo electrónico, el id. de objeto, etc.

Ahora restringimos la información a la que el ámbito `openid` permite acceder a la aplicación.  El ámbito "openid" solo permitirá que el usuario inicie sesión en la aplicación y que esta reciba un identificador específico de aplicación para el usuario.  Si desea obtener información personal identificable (PII) acerca del usuario en la aplicación, esta tendrá que solicitar permisos adicionales al usuario.  Estamos introduciendo dos nuevos ámbitos (`email` y `profile`) que permiten hacerlo.

El ámbito `email` es muy sencillo: permite que la aplicación acceda a la dirección de correo electrónico principal del usuario a través de la notificación `email` en id_token.  El ámbito `profile` ofrece a la aplicación acceso a toda la demás información básica sobre el usuario: su nombre, el nombre de usuario preferido, identificador de objeto y demás.

Esto le permite codificar la aplicación en un modo de divulgación mínima, puede pedir al usuario solo el conjunto de información que la aplicación necesita para hacer su trabajo.  Para obtener más información sobre estos ámbitos, consulte [la referencia de los ámbitos de la versión 2.0](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Notificaciones de token
Las notificaciones en tokens emitidas por el extremo de v2.0 no serán idénticas a los tokens emitidos por los extremos de Azure AD de disponibilidad general; las aplicaciones que migran al nuevo servicio no deben suponer que existirá una notificación concreta en id_tokens o access_tokens. Para obtener información sobre las notificaciones específicas emitidas en tokens v2.0, consulte la [referencia del token v2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Limitaciones
Es necesario tener en cuenta algunas restricciones cuando se usa el punto v2.0.  Consulte el [documento de limitaciones de v2.0](active-directory-v2-limitations.md) para ver si alguna de estas restricciones se aplica a su escenario concreto.
