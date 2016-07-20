<properties
   pageTitle="Creación de una aplicación que puede iniciar la sesión de cualquier usuario de Azure Active Directory | Microsoft Azure"
   description="Instrucciones paso a paso para crear una aplicación que pueda iniciar la sesión de un usuario desde cualquier inquilino de Azure Active Directory, lo que se conoce también como aplicación multiempresa."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/05/2016"
   ms.author="skwan;bryanla"/>

# Inicio de sesión de cualquier usuario de Azure Active Directory (AD) mediante el patrón de aplicación multiempresa
Si ofrece una aplicación de software como servicio a muchas organizaciones, puede configurar la aplicación para que acepte inicios de sesión de cualquier inquilino de Azure AD. En Azure AD, esto se conoce como convertir su aplicación en una aplicación multiempresa. Los usuarios de cualquier inquilino de Azure AD podrán iniciar sesión en su aplicación después de dar su consentimiento al uso de su cuenta con ella.

Si ya tiene una aplicación con su propio sistema de cuentas, o que admite otra clase de inicio de sesión de otros proveedores de nube, agregar el inicio de sesión de Azure AD es tan sencillo como registrar la aplicación, agregar código de inicio de sesión mediante los protocolos OAuth2, OpenID Connect o SAML y colocar un botón Iniciar sesión con Microsoft en la aplicación.

![Botón de inicio de sesión][AAD-Sign-In]

En este artículo se da por supuesto que ya está familiarizado con la creación de una aplicación de un solo inquilino para Azure AD. Si no lo está, vuelva a la [página principal de la guía del desarrollador][AAD-Dev-Guide] y pruebe una de nuestras guías de inicio rápido.

Para convertir la aplicación en una aplicación multiempresa de Azure AD, siga estos cuatro sencillos pasos:

1.	Actualice el registro de aplicación para que sea multiempresa
2.	Actualice el código para enviar solicitudes al punto de conexión /common
3.	Actualice el código para administrar varios valores issuer
4.	Comprenda el consentimiento de administrador y usuario y realice los cambios apropiados en el código

Vamos a examinar cada paso con detalle. También puede ir directamente a [esta lista de ejemplos de multiempresa][AAD-Samples-MT].

## Actualización del registro para que sea multiempresa
De forma predeterminada, los registros de API y de aplicación web en Azure son de un solo inquilino. Para convertir su registro en multiempresa, busque el modificador "La aplicación es multiempresa" en la página de configuración del registro de la aplicación en el [Portal de Azure clásico][AZURE-classic-portal] y establézcalo en "Sí".

Nota: Para que la aplicación pueda convertirse en multiempresa, Azure AD requiere que el URI de id. de aplicación sea único a nivel global. El URI de id. de aplicación es una de las maneras en que una aplicación se identifica en los mensajes de protocolo. Cuando la aplicación es de un solo inquilino, es suficiente con que el URI de id. de aplicación sea único en dicho inquilino. En el caso de una aplicación multiempresa, debe ser único a nivel global de forma que Azure AD pueda encontrar la aplicación entre todos los inquilinos. El carácter globalmente único viene impuesto por la necesidad de que el URI de id. de aplicación tenga un nombre de host que coincida con un dominio comprobado del inquilino de Azure AD. Por ejemplo, si el nombre del inquilino era contoso.onmicrosoft.com, un URI de id. de aplicación válido sería `https://contoso.onmicrosoft.com/myapp`. Si el inquilino tenía un dominio comprobado de `contoso.com`, entonces un URI de id. de aplicación válido también sería `https://contoso.com/myapp`. La configuración de una aplicación como multiempresa dará error si el URI de id. de aplicación no sigue este patrón.

Los registros de cliente nativo son multiempresa de forma predeterminada. No es necesario realizar ninguna acción para convertir un registro de aplicación cliente nativa en multiempresa.

## Actualización del código para enviar solicitudes a /common
En una aplicación de un solo inquilino, las solicitudes de inicio de sesión se envían al punto de conexión de inicio de sesión del inquilino. Por ejemplo, para contoso.onmicrosoft.com, el punto de conexión sería:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Las solicitudes enviadas al punto de conexión de un inquilino pueden iniciar la sesión de los usuarios (o invitados) de ese inquilino en las aplicaciones de dicho inquilino. Con una aplicación multiempresa, la aplicación no sabe de antemano de qué inquilino procede el usuario, así que no puede enviar solicitudes al punto de conexión de uno de los inquilinos. En su lugar, las solicitudes se envían a un punto de conexión que las multiplexa en todos los inquilinos de Azure AD:

    https://login.microsoftonline.com/common

Cuando Azure AD recibe una solicitud en el punto de conexión /common, inicia la sesión del usuario y, como consecuencia, detecta cuál es el inquilino del que procede. El punto de conexión /common funciona con todos los protocolos de autenticación compatibles con Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 y WS-Federation.

La respuesta de inicio de sesión a la aplicación contiene un token que representa al usuario. El valor issuer del token indica a una aplicación el inquilino del que procede el usuario. Cuando el punto de conexión /common devuelva una respuesta, el valor issuer del token corresponderá al inquilino del usuario. Es importante advertir que el punto de conexión /common no es un inquilino ni un emisor, sino un multiplexador. Para tener esto en cuenta, la lógica de la aplicación para validar los tokens debe estar actualizada al utilizar /common.

Vamos a detenernos en este punto un poco más.

## Actualización del código para administrar varios valores issuer
Las aplicaciones web y las API web reciben y validan los tokens desde Azure AD.

> [AZURE.NOTE] Si bien las aplicaciones cliente nativas solicitan y reciben tokens de Azure AD, lo hacen para enviarlos a las API, donde se validan. Las aplicaciones nativas no validan los tokens y deben tratarlos como opacos.

Veamos cómo una aplicación valida los tokens que recibe de Azure AD. Una aplicación de un solo inquilino tomará normalmente un valor de punto de conexión como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

y lo usará para crear una URL de metadatos (en este caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para descargar dos trozos esenciales de información que se usan para validar los tokens: las claves de firma del inquilino y el valor issuer. Cada inquilino de Azure AD tiene un valor issuer único con la forma:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

donde el valor de GUID es la versión segura de cambio de nombre del id. del inquilino. Si hace clic en el vínculo de metadatos más arriba en `contoso.onmicrosoft.com`, puede ver este valor issuer en el documento.

Cuando una aplicación de un solo inquilino valida el token, compara la firma de este con las claves de firma del documento de metadatos y se asegura de que el valor issuer en el token coincida con el que se ha encontrado en el documento de metadatos.

Como el punto de conexión /common no corresponde a un inquilino y no es un emisor, cuando examine el valor issuer en los metadatos de /common tendrá una URL con plantilla en lugar de un valor real:

    https://sts.windows.net/{tenantid}/

Por lo tanto, una aplicación multiempresa no puede validar los tokens con solo buscar la correspondencia del valor issuer en los metadatos con el valor `issuer` en el token. Una aplicación multiempresa necesita una lógica para decidir qué valores issuer son válidos y cuáles no, según la parte del id. de inquilino del valor issuer.

Por ejemplo, si una aplicación multiempresa solo permite el inicio de sesión desde inquilinos específicos que se han registrado para su servicio, debe comprobar el valor issuer o el valor de notificación `tid` en el token para asegurarse de que el inquilino se encuentra en su lista de suscriptores. Si una aplicación multiempresa solo trata con individuos y no toma ninguna decisión de acceso basada en los inquilinos, puede omitir completamente el valor issuer.

En los ejemplos de multiempresa que encontrará en la sección [Contenido relacionado](#related-content) al final de este artículo, la validación del emisor está deshabilitada para permitir que cualquier inquilino de Azure AD inicie la sesión.

Ahora veamos la experiencia para los usuarios que inician sesión en aplicaciones multiempresa.

## Comprensión del consentimiento de usuario y administrador
Para que un usuario inicie sesión en una aplicación en Azure AD, la aplicación debe estar representada en el inquilino del usuario. Esto permite que la organización realice cosas como aplicar directivas únicas cuando los usuarios de su inquilino inician sesión en la aplicación. Para una aplicación de un solo inquilino, este registro es sencillo; es lo que sucede cuando registra la aplicación en el [Portal de Azure clásico][AZURE-classic-portal].

Para una aplicación multiempresa, el registro inicial de la aplicación reside en el inquilino de Azure AD utilizado por el desarrollador. Cuando usuarios de otro inquilino inician sesión en la aplicación por primera vez, Azure AD les pide que den su consentimiento a los permisos solicitados por ella. Si aceptan, se crea una representación de la aplicación llamada *entidad de servicio* en el inquilino del usuario y el inicio de sesión puede continuar. También se crea una delegación en el directorio que registra el consentimiento del usuario a la aplicación. Para más información sobre los objetos Application y ServicePrincipal y cómo se relacionan entre sí, consulte [Objetos Application y objetos ServicePrincipal][AAD-App-SP-Objects].

Esta experiencia de consentimiento depende de los permisos solicitados por la aplicación. Azure AD admite dos clases de permisos, solo de aplicación o delegado:

- Un permiso delegado concede a una aplicación la posibilidad de actuar como un usuario que inicia sesión para un subconjunto de las cosas que el usuario puede hacer. Por ejemplo, puede conceder a una aplicación el permiso delegado para leer el calendario del usuario que ha iniciado la sesión.
- Un permiso de aplicación se concede directamente a la identidad de la aplicación. Por ejemplo, puede conceder a una aplicación el permiso de solo aplicación para leer la lista de usuarios de un inquilino, y esto lo podrá hacer con independencia de quién haya iniciado sesión en la aplicación.

Algunos permisos pueden tener el consentimiento de un usuario normal, mientras que otros necesitan el del administrador del inquilino.

### Consentimiento de administrador
Los permisos de solo aplicación siempre requieren el consentimiento del administrador de inquilinos. Si la aplicación solicita un permiso de solo aplicación y un usuario normal intenta iniciar sesión en la aplicación, esta obtendrá un mensaje de error que indica que el usuario no puede dar su consentimiento.

Algunos permisos delegados también requieren el consentimiento del administrador de inquilinos. Por ejemplo, la posibilidad de reescribir en Azure AD como el usuario que ha iniciado la sesión requiere el consentimiento del administrador de inquilinos. Al igual que los permisos de solo aplicación, si un usuario ordinario intenta iniciar sesión en una aplicación que solicita un permiso delegado que requiere el consentimiento del administrador, la aplicación recibirá un error. Que un permiso requiera o no el consentimiento del administrador viene determinado por el desarrollador que publica el recurso, y se puede encontrar en la documentación del recurso. En la sección [Contenido relacionado](#related-content) de este artículo se pueden encontrar vínculos que describen los permisos disponibles para la API de Azure AD Graph.

Si la aplicación usa permisos que requieren el consentimiento del administrador, deberá tener un gesto en su aplicación, como un botón o un vínculo donde el administrador pueda iniciar la acción. La solicitud que la aplicación envía para esta acción es una solicitud de autorización habitual de OAuth2 o OpenID Connect, pero también incluye el parámetro de cadena de consulta `prompt=admin_consent`. Una vez que el administrador ha dado su consentimiento y la entidad de servicio se crea en el inquilino del cliente, las posteriores solicitudes de inicio de sesión no necesitan el parámetro `prompt=admin_consent`. Dado que el administrador ha decido que los permisos solicitados son aceptables, en adelante no se solicitará consentimiento a ningún otro usuario.

El parámetro `prompt=admin_consent` también puede utilizarse con aplicaciones que solicitan permisos que no requieren el consentimiento del administrador, pero querrá proporcionar una experiencia en la que el administrador de inquilinos se "registre" para la aplicación una vez y, a partir de aquí, a ningún otro usuario se le pida consentimiento.

Si una aplicación requiere el consentimiento del administrador y el administrador inicia sesión en la aplicación, pero el parámetro `prompt=admin_consent` no se envía, el administrador podrá dar su consentimiento a la aplicación pero solo la dará para su cuenta de usuario. Los usuarios normales seguirán sin poder iniciar sesión y dar su consentimiento a la aplicación. Esto resulta útil si quiere dar al administrador de inquilinos la posibilidad de explorar la aplicación antes de permitir el acceso a otros usuarios.

Un administrador de inquilinos puede deshabilitar la posibilidad de que los usuarios normales den su consentimiento a las aplicaciones. En este caso, para que la aplicación se instale en el inquilino siempre se solicitará el consentimiento del administrador. Si quiere probar la aplicación con el consentimiento de usuarios normales deshabilitado, puede encontrar el modificador de configuración en la sección de configuración de inquilinos de Azure AD del [Portal de Azure clásico][AZURE-classic-portal].

> [AZURE.NOTE] En algunas aplicaciones se busca una experiencia en la que los usuarios normales puedan dar su consentimiento inicialmente; más tarde, se puede hacer partícipe al administrador y solicitar permisos que requieran su consentimiento. Esto no hay forma de hacerlo actualmente con un solo registro de aplicación en Azure AD. El punto de conexión de Azure AD v2 que se lanzará próximamente permitirá que las aplicaciones soliciten permisos en tiempo de ejecución, en lugar de en el momento del registro, lo que permitirá este escenario. Para más información, consulte la [Guía del desarrollador para el modelo de aplicaciones v2 de Azure AD][AAD-V2-Dev-Guide].

### Consentimiento y aplicaciones de niveles múltiples
La aplicación puede tener varios niveles, cada uno representado por su propio registro en Azure AD. Por ejemplo, una aplicación nativa que llama a una API web o una aplicación web que llama a una API web. En ambos casos, el cliente (aplicación nativa o aplicación web) solicita permisos para llamar al recurso (API web). Para que la aplicación nativa o la aplicación web se acepten correctamente como inquilinos del cliente, todos los recursos a los que solicitan permisos deben ya existir en el inquilino del cliente. Si no se cumple esta condición, Azure AD devolverá un error indicando que primero se debe agregar el recurso.

Esto puede ser un problema si la aplicación lógica consta de dos o más registros de aplicación, por ejemplo, un cliente y un recurso independientes. ¿Cómo se convierte primero el recurso en el inquilino del cliente? Para resolver este caso, Azure AD permite que el cliente y el recurso se acepten en un único paso, donde el usuario ve la suma total de los permisos solicitados por el cliente y el recurso en la página de consentimiento. Para permitir este comportamiento, el registro de aplicación del recurso debe incluir el id. de aplicación del cliente como un elemento `knownClientApplications` en su manifiesto de aplicación. Por ejemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Esta propiedad se puede actualizar mediante el [manifiesto de aplicación][AAD-App-Manifest] del recurso y se demuestra en un ejemplo de llamada a la API web de un cliente nativo de niveles múltiples en la sección [Contenido relacionado](#related-content) al final de este artículo.

Un caso parecido tiene lugar si los diferentes niveles de una aplicación se registran en distintos inquilinos. Por ejemplo, considere el caso de la creación de una aplicación cliente nativa que llama a la API de Office 365 Exchange Online. Para desarrollar la aplicación nativa y, más tarde, para que se ejecute en el inquilino de un cliente, la entidad de servicio de Exchange Online debe existir. En este caso, el cliente tiene que comprar Exchange Online para que la entidad de servicio se cree en su inquilino. En el caso de una API creada por una organización que no sea Microsoft, el desarrollador de la API debe proporcionar una forma de que sus clientes acepten dar su consentimiento a su aplicación como inquilinos del cliente; por ejemplo, una página web que controle el consentimiento mediante los mecanismos descritos en este artículo. Después de crear la entidad de servicio en el inquilino, la aplicación nativa puede obtener tokens para la API.

### Revocación del consentimiento
Los usuarios y administradores pueden revocar el consentimiento a la aplicación en cualquier momento:

- Los usuarios revocan el acceso a aplicaciones individuales quitándolas de su lista [Aplicaciones del panel de acceso][AAD-Access-Panel].
- Los administradores revocan el acceso a las aplicaciones quitándolas de Azure AD mediante la sección de administración de Azure AD del [Portal de Azure clásico][AZURE-classic-portal].

Si un administrador da su consentimiento a una aplicación que incluye a todos los usuarios de un inquilino, los usuarios no pueden revocar el acceso de forma individual. Solo el administrador puede revocar el acceso y solo para la aplicación entera.

### Compatibilidad con el consentimiento y los protocolos
El consentimiento se admite en Azure AD mediante los protocolos OAuth, OpenID Connect, WS-Federation y SAML. Los protocolos SAML y WS-Federation no admiten el parámetro `prompt=admin_consent`, de modo que el consentimiento solo es posible mediante OAuth y OpenID Connect.

## Aplicaciones multiempresa y almacenamiento en caché de los tokens de acceso
Las aplicaciones multiempresa también pueden obtener tokens de acceso para llamar a las API que están protegidas por Azure AD. Un error común al usar la biblioteca de autenticación de Active Directory (ADAL) con una aplicación multiempresa es solicitar inicialmente un token para un usuario que utiliza /common, recibir una respuesta y, luego, solicitar posteriormente un token para ese mismo usuario también mediante /common. Dado que la respuesta de Azure AD proviene de un inquilino, y no de /common, ADAL almacena en caché el token como si fuera el inquilino. La posterior llamada a /common para obtener un token de acceso para el usuario carece de la entrada de caché, y se pide al usuario que inicie la sesión de nuevo. Para evitar la pérdida de la caché, asegúrese de que las posteriores llamadas para un usuario que ya ha iniciado sesión se realizan al punto de conexión del inquilino.

## Contenido relacionado

- [Ejemplos de aplicación multiempresa][AAD-Samples-MT]
- [Guía del desarrollador de Azure AD][AAD-Dev-Guide]
- [Objetos Application y objetos ServicePrincipal][AAD-App-SP-Objects]
- [Integración de aplicaciones con Azure Active Directory][AAD-Integrating-Apps]
- [Información general sobre el marco de consentimiento][AAD-Consent-Overview]
- [Ámbitos de permiso de la API Graph de Microsoft][MSFT-Graph-AAD]
- [Ámbitos de permiso de la API Graph de Azure AD][AAD-Graph-Perm-Scopes]

Use la siguiente sección de comentarios DISQUS para proporcionar sus opiniones y ayudarnos a refinar y remodelar nuestro contenido.

<!--Reference style links -->
[AAD-Access-Panel]: https://myapps.microsoft.com
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/es-ES/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/es-ES/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png




<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipalentity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#userentity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/es-ES/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

<!---HONumber=AcomDC_0706_2016-->