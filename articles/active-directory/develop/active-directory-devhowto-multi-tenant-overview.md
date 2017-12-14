---
title: "Creación de una aplicación que pueda iniciar sesión cualquier usuario de Azure AD"
description: "Instrucciones paso a paso para crear una aplicación que pueda iniciar la sesión de un usuario desde cualquier inquilino de Azure Active Directory, lo que se conoce también como aplicación multiempresa."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: f6d8d2c07c2860059c4e9deb75d0bc4a876e057b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Inicio de sesión de cualquier usuario de Azure Active Directory (AD) mediante el patrón de aplicación multiempresa
Si ofrece una aplicación de software como servicio a muchas organizaciones, puede configurar la aplicación para que acepte inicios de sesión de cualquier inquilino de Azure AD.  En Azure AD, esta configuración se conoce como convertir su aplicación en una aplicación multiinquilino.  Los usuarios de cualquier inquilino de Azure AD podrán iniciar sesión en su aplicación después de dar su consentimiento al uso de su cuenta con ella.  

Si tiene una aplicación existente que tiene su propio sistema de cuenta o es compatible con otros tipos de inicio de sesión de otros proveedores de nube, es sencillo agregar el inicio de sesión de Azure AD desde cualquier inquilino. Solo tiene que registrar la aplicación, agregar el código de inicio de sesión a través de OAuth2, OpenID Connect o SAML e incluir el botón "Iniciar sesión con Microsoft" en la aplicación. Haga clic en el botón siguiente para más información sobre la personalización de marca de la aplicación.

[![Botón de inicio de sesión][AAD-Sign-In]][AAD-App-Branding]

En este artículo se da por supuesto que ya está familiarizado con la creación de una aplicación de un solo inquilino para Azure AD.  Si no lo está, vuelva a la [página principal de la guía del desarrollador][AAD-Dev-Guide] y pruebe una de nuestras guías de inicio rápido.

Para convertir la aplicación en una aplicación multiempresa de Azure AD, siga estos cuatro sencillos pasos:

1. Actualice el registro de aplicación para que sea multiempresa
2. Actualice el código para enviar solicitudes al punto de conexión /common 
3. Actualice el código para administrar varios valores issuer
4. Comprenda el consentimiento de administrador y usuario y realice los cambios apropiados en el código

Vamos a examinar cada paso con detalle. También puede ir directamente a [esta lista de ejemplos de multiinquilino][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Actualización del registro para que sea multiempresa
De forma predeterminada, los registros de API y de aplicación web en Azure son de un solo inquilino.  Para convertir su registro en multiinquilino, busque el conmutador “Multiinquilino” en la página de propiedades del registro de la aplicación en [Azure Portal][AZURE-portal] y establézcalo en “Sí”.

Tenga en cuenta que, para que la aplicación pueda convertirse en multiempresa, Azure AD requiere que el URI de id. de aplicación sea único a nivel global. El URI de id. de aplicación es una de las maneras en que una aplicación se identifica en los mensajes de protocolo.  Cuando la aplicación es de un solo inquilino, es suficiente con que el URI de id. de aplicación sea único en dicho inquilino.  En el caso de una aplicación multiempresa, debe ser único a nivel global de forma que Azure AD pueda encontrar la aplicación entre todos los inquilinos.  El carácter globalmente único viene impuesto por la necesidad de que el URI de id. de aplicación tenga un nombre de host que coincida con un dominio comprobado del inquilino de Azure AD.  Por ejemplo, si el nombre del inquilino era contoso.onmicrosoft.com, un identificador URI de id. de aplicación válido sería `https://contoso.onmicrosoft.com/myapp`.  Si el inquilino tenía el dominio comprobado `contoso.com`, también sería un URI de id. de aplicación válido `https://contoso.com/myapp`.  Si el URI de id. de aplicación no sigue este patrón, la configuración de una aplicación como multiinquilino dará error.

Los registros de cliente nativo son multiempresa de forma predeterminada.  No es necesario realizar ninguna acción para convertir un registro de aplicación cliente nativa en multiempresa.

## <a name="update-your-code-to-send-requests-to-common"></a>Actualización del código para enviar solicitudes a /common
En una aplicación de un solo inquilino, las solicitudes de inicio de sesión se envían al punto de conexión de inicio de sesión del inquilino. Por ejemplo, para contoso.onmicrosoft.com, el punto de conexión sería:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Las solicitudes enviadas al punto de conexión de un inquilino pueden iniciar la sesión de los usuarios (o invitados) de ese inquilino en las aplicaciones de dicho inquilino.  Con una aplicación multiempresa, la aplicación no sabe de antemano de qué inquilino procede el usuario, así que no puede enviar solicitudes al punto de conexión de uno de los inquilinos.  En su lugar, las solicitudes se envían a un punto de conexión que las multiplexa en todos los inquilinos de Azure AD:

    https://login.microsoftonline.com/common

Cuando Azure AD recibe una solicitud en el punto de conexión /common, inicia la sesión del usuario y, como consecuencia, detecta cuál es el inquilino del que procede.  El punto de conexión /common funciona con todos los protocolos de autenticación compatibles con Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 y WS-Federation.

La respuesta de inicio de sesión a la aplicación contiene un token que representa al usuario.  El valor issuer del token indica a una aplicación el inquilino del que procede el usuario.  Cuando el punto de conexión /common devuelva una respuesta, el valor issuer del token corresponderá al inquilino del usuario.  Es importante advertir que el punto de conexión /common no es un inquilino ni un emisor, sino un multiplexador.  Para tener esto en cuenta, la lógica de la aplicación para validar los tokens debe estar actualizada al utilizar /common. 

Como se mencionó anteriormente, las aplicaciones multiinquilino también deben proporcionar una experiencia coherente de inicio de sesión para los usuarios, que se ajuste a las directrices de personalización de marca de la aplicación de Azure AD. Haga clic en el botón siguiente para más información sobre la personalización de marca de la aplicación.

[![Botón de inicio de sesión][AAD-Sign-In]][AAD-App-Branding]

Echemos un vistazo detenidamente a la utilización del punto de conexión más común y a la implementación de código.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Actualice el código para administrar varios valores issuer
Las aplicaciones web y las API web reciben y validan los tokens desde Azure AD.  

> [!NOTE]
> Si bien las aplicaciones cliente nativas solicitan y reciben tokens de Azure AD, lo hacen para enviarlos a las API, donde se validan.  Las aplicaciones nativas no validan los tokens y deben tratarlos como opacos.
> 
> 

Veamos cómo una aplicación valida los tokens que recibe de Azure AD.  Una aplicación de un solo inquilino normalmente toma un valor de punto de conexión como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

y lo usará para crear una URL de metadatos (en este caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para descargar dos tipos de información esenciales que se usan para validar los tokens: las claves de firma del inquilino y el valor issuer.  Cada inquilino de Azure AD tiene un valor issuer único con la forma:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

donde el valor de GUID es la versión segura de cambio de nombre del id. del inquilino.  Si hace clic en el vínculo de metadatos anterior en `contoso.onmicrosoft.com`, puede ver este valor issuer en el documento.

Cuando una aplicación de un solo inquilino valida un token, comprueba la firma del token con las claves de firma del documento de metadatos. Esta prueba permite asegurarse de que el valor issuer del token coincide con el que se encuentran en el documento de metadatos.

Como el punto de conexión /common no corresponde a un inquilino y no es un emisor, cuando examine el valor issuer en los metadatos de /common tendrá una URL con plantilla en lugar de un valor real:

    https://sts.windows.net/{tenantid}/

Por lo tanto, una aplicación multiempresa no puede validar los tokens simplemente haciendo coincidir el valor issuer de los metadatos con el valor `issuer` del token.  Una aplicación multiempresa necesita una lógica para decidir qué valores issuer son válidos y cuáles no, según la parte del id. de inquilino del valor issuer.  

Por ejemplo, si una aplicación multiinquilino solo permite el inicio de sesión desde inquilinos específicos que se han registrado para su servicio, debe comprobar el valor issuer o el valor de notificación `tid` en el token para asegurarse de que el inquilino se encuentra en su lista de suscriptores.  Si una aplicación multiempresa solo trata con individuos y no toma ninguna decisión de acceso basada en los inquilinos, puede omitir completamente el valor issuer.

En los ejemplos de multiinquilino de la sección [Contenido relacionado](#related-content) al final de este artículo, la validación del emisor está deshabilitada para permitir que cualquier inquilino de Azure AD inicie sesión.

Ahora veamos la experiencia para los usuarios que inician sesión en aplicaciones multiempresa.

## <a name="understanding-user-and-admin-consent"></a>Comprensión del consentimiento de usuario y administrador
Para que un usuario inicie sesión en una aplicación en Azure AD, la aplicación debe estar representada en el inquilino del usuario.  Esto permite que la organización realice cosas como aplicar directivas únicas cuando los usuarios de su inquilino inician sesión en la aplicación.  Para una aplicación de un solo inquilino, este registro es sencillo; es lo que sucede cuando registra la aplicación en [Azure Portal][AZURE-portal].

Para una aplicación multiempresa, el registro inicial de la aplicación reside en el inquilino de Azure AD utilizado por el desarrollador.  Cuando usuarios de otro inquilino inician sesión en la aplicación por primera vez, Azure AD les pide que den su consentimiento a los permisos solicitados por ella.  Si aceptan, se crea una representación de la aplicación llamada *entidad de servicio* en el inquilino del usuario, y el inicio de sesión puede continuar. También se crea una delegación en el directorio que registra el consentimiento del usuario a la aplicación. Para más información sobre los objetos Application y ServicePrincipal y cómo se relacionan entre sí, consulte [Objetos Application y objetos ServicePrincipal][AAD-App-SP-Objects].

![Consentimiento para aplicaciones de nivel sencillo][Consent-Single-Tier] 

Esta experiencia de consentimiento depende de los permisos solicitados por la aplicación.  Azure AD admite dos clases de permisos, solo de aplicación o delegado:

* Un permiso delegado concede a una aplicación la posibilidad de actuar como un usuario que inicia sesión para un subconjunto de las cosas que el usuario puede hacer.  Por ejemplo, puede conceder a una aplicación el permiso delegado para leer el calendario del usuario que ha iniciado la sesión.
* Un permiso de aplicación se concede directamente a la identidad de la aplicación.  Por ejemplo, puede conceder a una aplicación el permiso de solo aplicación para leer la lista de usuarios de un inquilino, con independencia de quién haya iniciado sesión en la aplicación.

Algunos permisos pueden tener el consentimiento de un usuario normal, mientras que otros necesitan el del administrador del inquilino. 

### <a name="admin-consent"></a>Consentimiento de administrador
Los permisos de solo aplicación siempre requieren el consentimiento del administrador de inquilinos.  Si la aplicación solicita un permiso de solo aplicación y un usuario intenta iniciar sesión en la aplicación, aparece un mensaje de error que indica que el usuario no puede dar su consentimiento.

Algunos permisos delegados también requieren el consentimiento del administrador de inquilinos.  Por ejemplo, la posibilidad de reescribir en Azure AD como el usuario que ha iniciado la sesión requiere el consentimiento del administrador de inquilinos.  Al igual que los permisos de solo aplicación, si un usuario ordinario intenta iniciar sesión en una aplicación que solicita un permiso delegado que requiere el consentimiento del administrador, la aplicación recibe un error.  Que un permiso requiera o no el consentimiento del administrador viene determinado por el desarrollador que publica el recurso, y se puede encontrar en la documentación del recurso.  En la sección [Contenido relacionado](#related-content) de este artículo se pueden encontrar vínculos que describen los permisos disponibles para la API de Azure AD Graph.

Si la aplicación usa permisos que requieren el consentimiento del administrador, necesita tener un gesto, como un botón o un vínculo donde el administrador pueda iniciar la acción.  La solicitud que la aplicación envía para esta acción es una solicitud de autorización habitual de OAuth2 o OpenID Connect, pero también incluye el parámetro de cadena de consulta `prompt=admin_consent` .  Una vez que el administrador ha dado su consentimiento y la entidad de servicio se crea en el inquilino del cliente, las posteriores solicitudes de inicio de sesión no necesitan el parámetro `prompt=admin_consent`. Dado que el administrador ha decido que los permisos solicitados son aceptables, en adelante no se solicitará consentimiento a ningún otro usuario.

El parámetro `prompt=admin_consent` también se puede utilizar en las aplicaciones que solicitan permisos que no requieren el consentimiento del administrador. Esto se hace cuando el aplicador requiere una experiencia en la que el administrador del inquilino se “registra” una vez, y no se solicita a otros usuarios que den su consentimiento a partir de entonces.

Si una aplicación requiere el consentimiento del administrador y un administrador inicia sesión, pero no se envía el parámetro `prompt=admin_consent`, el administrador da su consentimiento correctamente a la aplicación **solo para su cuenta de usuario**.  Los usuarios normales seguirán sin poder iniciar sesión y dar su consentimiento a la aplicación.  Esta característica resulta útil si quiere dar al administrador de inquilinos la posibilidad de explorar la aplicación antes de permitir el acceso a otros usuarios.

Un administrador de inquilinos puede deshabilitar la posibilidad de que los usuarios normales den su consentimiento a las aplicaciones.  En este caso, para que la aplicación se instale en el inquilino siempre se solicitará el consentimiento del administrador.  Si quiere probar la aplicación con el consentimiento de usuarios normales deshabilitado, puede encontrar el conmutador de configuración en la sección de configuración de inquilinos de Azure AD de [Azure Portal][AZURE-portal].

> [!NOTE]
> En algunas aplicaciones se busca una experiencia en la que los usuarios normales puedan dar su consentimiento inicialmente; más tarde, se puede hacer partícipe al administrador y solicitar permisos que requieran su consentimiento.  Esto no hay forma de hacerlo actualmente con un solo registro de aplicación en Azure AD.  El punto de conexión del modelo de implementación de Resource Manager de Azure AD que se lanzará próximamente permitirá que las aplicaciones soliciten permisos en tiempo de ejecución, en lugar de en el momento del registro, lo que permitirá este escenario.  Para más información, consulte la [Guía para desarrolladores para el modelo de implementación de Resource Manager para el modelo de aplicaciones de Azure AD][AAD-V2-Dev-Guide].
> 
> 

### <a name="consent-and-multi-tier-applications"></a>Consentimiento y aplicaciones de niveles múltiples
La aplicación puede tener varios niveles, cada uno representado por su propio registro en Azure AD.  Por ejemplo, una aplicación nativa que llama a una API web o una aplicación web que llama a una API web.  En ambos casos, el cliente (aplicación nativa o aplicación web) solicita permisos para llamar al recurso (API web).  Para que la aplicación nativa o la aplicación web se acepten correctamente como inquilinos del cliente, todos los recursos a los que solicitan permisos deben ya existir en el inquilino del cliente.  Si no se cumple esta condición, Azure AD devuelve un error indicando que primero se debe agregar el recurso.

**Múltiples niveles en un solo inquilino**

Esto puede ser un problema si la aplicación lógica consta de dos o más registros de aplicación, por ejemplo, un cliente y un recurso independientes.  ¿Cómo se convierte primero el recurso en el inquilino del cliente?  Azure AD aborda este caso al habilitar el consentimiento del cliente y del recurso en un solo paso. El usuario ve la suma total de los permisos solicitados por el cliente y el recurso en la página de consentimiento.  Para permitir este comportamiento, el registro de la aplicación del recurso debe incluir el id. de aplicación del cliente como un elemento `knownClientApplications` en su manifiesto de aplicación.  Por ejemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Esta propiedad se puede actualizar a través del [manifiesto de la aplicación][AAD-App-Manifest] del recurso. Esto se demuestra en un ejemplo de llamada a la API web de un cliente nativo de niveles múltiples en la sección [Contenido relacionado](#related-content) al final de este artículo. En el diagrama siguiente se ofrece información general sobre el consentimiento de una aplicación de niveles múltiples registrada en un solo inquilino:

![Consentimiento para aplicaciones cliente conocidas con niveles múltiples][Consent-Multi-Tier-Known-Client] 

**Múltiples niveles en varios inquilinos**

Un caso parecido tiene lugar si los diferentes niveles de una aplicación se registran en distintos inquilinos.  Por ejemplo, considere el caso de la creación de una aplicación cliente nativa que llama a la API de Office 365 Exchange Online.  Para desarrollar la aplicación nativa y, más tarde, para que se ejecute en el inquilino de un cliente, la entidad de servicio de Exchange Online debe existir.  En este caso, el desarrollador y el cliente tienen que comprar Exchange Online para que la entidad de servicio se cree en sus inquilinos.  

En el caso de una API creada por una organización que no sea Microsoft, el desarrollador de la API debe proporcionar una forma de que sus clientes acepten dar su consentimiento a la aplicación en los inquilinos del cliente. El diseño recomendado tiene la finalidad de que el desarrollador de terceros cree la API de tal forma que también pueda funcionar como un cliente web para implementar el registro:

1. Consulte las secciones anteriores para asegurarse de que la API implementa los requisitos de código y el registro de aplicaciones multiinquilino.
2. Además de exponer los roles y ámbitos de la API, asegúrese de que el registro incluye el permiso predeterminado de Azure AD "Iniciar sesión y leer el perfil del usuario".
3. Implemente una página de inicio de sesión o registro en el cliente web, según las instrucciones para el [consentimiento del administrador](#admin-consent) expuestas anteriormente. 
4. Una vez que el usuario da su consentimiento a la aplicación, se crean los vínculos a la delegación de consentimiento y a la entidad de servicio en el inquilino, y la aplicación nativa puede obtener tokens para la API.

En el diagrama siguiente se ofrece información general sobre el consentimiento de una aplicación de múltiples niveles registrada en diferentes inquilinos:

![Consentimiento para aplicaciones de terceros con niveles múltiples][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revocación del consentimiento
Los usuarios y administradores pueden revocar el consentimiento a la aplicación en cualquier momento:

* Los usuarios revocan el acceso a aplicaciones individuales quitándolas de su lista [Aplicaciones del panel de acceso][AAD-Access-Panel].
* Los administradores revocan el acceso a las aplicaciones quitándolas de Azure AD mediante la sección de administración de Azure AD de [Azure Portal][AZURE-portal].

Si un administrador da su consentimiento a una aplicación que incluye a todos los usuarios de un inquilino, los usuarios no pueden revocar el acceso de forma individual.  Solo el administrador puede revocar el acceso y solo para la aplicación entera.

### <a name="consent-and-protocol-support"></a>Compatibilidad con el consentimiento y los protocolos
El consentimiento se admite en Azure AD mediante los protocolos OAuth, OpenID Connect, WS-Federation y SAML.  Los protocolos SAML y WS-Federation no admiten el parámetro `prompt=admin_consent` , de modo que el consentimiento solo es posible mediante OAuth y OpenID Connect.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicaciones multiempresa y almacenamiento en caché de los tokens de acceso
Las aplicaciones multiempresa también pueden obtener tokens de acceso para llamar a las API que están protegidas por Azure AD.  Un error común al usar la biblioteca de autenticación de Active Directory (ADAL) con una aplicación multiempresa es solicitar inicialmente un token para un usuario que utiliza /common, recibir una respuesta y, luego, solicitar posteriormente un token para ese mismo usuario también mediante /common.  Dado que la respuesta de Azure AD proviene de un inquilino, y no de /common, ADAL almacena en caché el token como si fuera el inquilino. La posterior llamada a /common para obtener un token de acceso para el usuario carece de la entrada de caché, y se pide al usuario que inicie la sesión de nuevo.  Para evitar la pérdida de la caché, asegúrese de que las posteriores llamadas para un usuario que ya ha iniciado sesión se realizan al punto de conexión del inquilino.

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha aprendido a crear una aplicación que puede hacer que un usuario inicie sesión desde cualquier inquilino de Azure Active Directory. Después de habilitar el inicio de sesión único entre la aplicación y Azure Active Directory, también puede actualizar la aplicación para acceder a las API expuestas por recursos de Microsoft como Office 365. Por tanto, puede ofrecer una experiencia personalizada en su aplicación, por ejemplo, que muestre información contextual a los usuarios, como su imagen de perfil o su próxima cita de calendario. Para más información sobre cómo realizar llamadas de API a Azure Active Directory y servicios de Office 365 como Exchange, SharePoint, OneDrive, OneNote, Planner, Excel y muchos más, visite [Información general de Microsoft Graph][MSFT-Graph-overview].


## <a name="related-content"></a>Contenido relacionado
* [Ejemplos de aplicación multiinquilino][AAD-Samples-MT]
* [Directrices de personalización de marca para aplicaciones][AAD-App-Branding]
* [Guía del desarrollador de Azure AD][AAD-Dev-Guide]
* [Objetos Application y objetos ServicePrincipal][AAD-App-SP-Objects]
* [Integración de aplicaciones con Azure Active Directory][AAD-Integrating-Apps]
* [Información general sobre el marco de consentimiento][AAD-Consent-Overview]
* [Ámbitos de permiso de la API Graph de Microsoft][MSFT-Graph-permision-scopes]
* [Ámbitos de permiso de la API Graph de Azure AD][AAD-Graph-Perm-Scopes]

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














