<properties
   pageTitle="Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory | Microsoft Azure"
   description="Obtenga más información sobre la implementación del flujo de concesión implícita de OAuth2 de Azure Active Directory y conozca si es adecuado para su aplicación."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2016"
   ms.author="vittorib;bryanla"/>

# Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory (AD)

La concesión implícita de OAuth2 es conocida por tratarse de la lista más amplia de problemas de seguridad de la especificación de OAuth2. Además, es el enfoque que ha implementado ADAL JS y el que recomendamos a la hora de crear aplicaciones SPA. ¿Qué ventajas aporta? Se trata de analizar los pros y los contras: cuando se adopta, la concesión implícita es el mejor enfoque que puede aplicarse a las aplicaciones que usan una API web a través de JavaScript en un explorador.

## ¿Qué es la concesión implícita de OAuth2?

La [concesión de código de autorización de OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) por antonomasia es la concesión de autorización que utilizan dos puntos de conexión independientes. El punto de conexión de autorización se utiliza para la fase de interacción del usuario, lo que genera un código de autorización; el punto de conexión de token lo utiliza después el cliente para intercambiar el código de un token de acceso; a menudo también se genera un token de actualización. Las aplicaciones web son necesarias para presentar sus propias credenciales de aplicación al punto de conexión de token para que el servidor de autorización pueda autenticar al cliente.

La [concesión implícita de OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) se define como una variante en la que un cliente puede obtener un token de acceso (y, en el caso de [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html), un id\_token) directamente desde el punto de conexión de autorización, sin necesidad de ponerse en contacto con el punto de conexión de token ni autenticar la aplicación cliente. Esta variante se diseñó expresamente para las aplicaciones basadas en JavaScript que se ejecutan en un explorador web: en la especificación de OAuth2 original, los tokens se devuelven en un fragmento de URI. De este modo, los bits de token estarán disponibles en el código JavaScript del cliente, pero garantiza que no se incluirán en las redirecciones hacia el servidor. Devolver tokens a través de redirecciones del explorador directamente desde los puntos de conexión de autorización tiene también la ventaja de que se eliminan los requisitos de las llamadas de origen cruzado, que sería necesario si la aplicación JavaScript tuviese que ponerse en contacto con el punto de conexión de token.

Una característica importante de la concesión implícita de OAuth2 es el hecho de que los flujos nunca devuelven tokens de actualización al cliente. Como veremos en la sección siguiente, esto no es realmente necesario y, de hecho, supondría un problema de seguridad.

## Escenarios adecuados para la concesión implícita de OAuth2

Tal y como declara la propia especificación de OAuth2, la concesión implícita se ha diseñado para habilitar aplicaciones de agente de usuario, es decir, aplicaciones JavaScript que se ejecutan en un explorador. La característica definitoria de esas aplicaciones es que el código JavaScript se utiliza para acceder a los recursos del servidor (normalmente, una API web) y actualizar en consecuencia la experiencia de usuario de la aplicación. Piense en aplicaciones como Gmail o Outlook Web Access: al seleccionar un mensaje de la bandeja de entrada, solo cambia el panel de visualización de mensajes para mostrar la nueva selección, mientras que el resto de la página permanece sin modificar. A diferencia de las aplicaciones web basadas en redireccionamientos tradicionales, donde cada interacción del usuario produce un postback de página completa y una representación de página completa de la nueva respuesta del servidor.

Las aplicaciones que aplican el enfoque basado en JavaScript en su punto de conexión se denominan "aplicaciones de una sola página" o "SPA": la idea es que estas aplicaciones solo publican una página HTML inicial y el código JavaScript asociado; las interacciones subsiguientes que se controlan mediante llamadas a API web realizadas a través de JavaScript. Sin embargo, los enfoques híbridos —donde la aplicación se basa fundamentalmente en eventos postback, pero realiza llamadas de JavaScript ocasionales para implementar algunas experiencias— no son habituales. El debate sobre el uso del flujo implícito es pertinente para este tipo de enfoques.

Normalmente, las aplicaciones basadas en redireccionamientos protegen sus solicitudes a través de cookies; sin embargo, este enfoque no es igual de eficaz con las aplicaciones JavaScript, ya que las cookies solo funcionan en el dominio en que se han generado, aunque las llamadas de JavaScript podrían dirigirse a otros dominios. De hecho, esto pasará muy a menudo. Piense en las aplicaciones que invocan la API Graph de Microsoft, la API de Office o la API de Azure: todas residen fuera del dominio desde donde se publica la aplicación. Una tendencia que se está volviendo más popular en las aplicaciones JavaScript es no tener ningún back-end y depender exclusivamente de API web de terceros para implementar su función empresarial.

En este momento, el método preferido para proteger las llamadas a la API web consiste en usar el enfoque de token de portador de OAuth2. Aquí, cada llamada va acompañada de un token de acceso de OAuth2; la API web examina el token de acceso entrante y, si encuentra en él los ámbitos necesarios, concede acceso a la operación solicitada. El flujo implícito proporciona un cómodo mecanismo para que las aplicaciones JavaScript obtengan tokens de acceso para una API web, con lo que ofrece numerosas ventajas con respecto a las cookies:

- Pueden obtenerse tokens de forma confiable sin necesidad de realizar llamadas de origen cruzado: gracias al registro obligatorio del URI de redirección en el que se devuelven los tokens, se garantiza que estos no van a sustituirse.
- Las aplicaciones JavaScript pueden obtener tantos tokens de acceso como necesiten para la cantidad de API web que tengan como destino; sin restricciones de dominios.
- Las características de HTML5 como el almacenamiento local o de sesión conceden un control total sobre el almacenamiento en caché de tokens y administración de la duración, mientras que la administración de cookies es opaca para la aplicación.
- Los tokens de acceso no son susceptibles a los ataques de falsificación de solicitud entre sitios (CSRF).

El flujo de concesión implícita no emite tokens de actualización, principalmente por motivos de seguridad. Los tokens de actualización no tienen tantas limitaciones como los de acceso, que conceden muchos más permisos y, por tanto, pueden producir más daños en caso de que se filtren. En el flujo implícito, los tokens se entregan en la dirección URL, por lo tanto, el riesgo de interceptación es mayor que en la concesión de código de autorización.

Sin embargo, tenga en cuenta que una aplicación JavaScript posee otro mecanismo para renovar los tokens de acceso sin preguntar las credenciales al usuario repetidamente. La aplicación puede usar un elemento iframe oculto para realizar nuevas solicitudes de token en el punto de conexión de autorización de Azure AD: siempre y cuando el explorador siga teniendo una sesión activa (es decir, una cookie de sesión) en el dominio de Azure AD, la solicitud de autenticación podrá realizarse correctamente sin necesidad de que tenga que interactuar el usuario.

Este modelo concede a la aplicación JavaScript la capacidad de renovar los tokens de acceso de manera independiente e, incluso, adquirir nuevos para una nueva API (siempre que el usuario haya dado previamente su consentimiento) sin la carga adicional que supone obtener, mantener y proteger un artefacto de gran valor como un token de actualización. El artefacto que posibilita la renovación silenciosa, la cookie de sesión de Azure AD, se administra fuera de la aplicación. Otra ventaja que tiene este enfoque es que, cuando un usuario cierra una sesión de Azure AD desde cualquiera de las aplicaciones que usen Azure AD en cualquiera de las pestañas del explorador, se elimina la cookie de sesión de Azure AD y la aplicación JavaScript perderá automáticamente la capacidad de renovar los tokens para el usuario que ha cerrado sesión.

## ¿Es adecuada la concesión implícita para mi aplicación?

La concesión implícita presenta más riesgos que otros tipos de concesión. Hay bastante documentación sobre las áreas a las que debe prestar atención. Consulte, por ejemplo, [Misuse of Access Token to Impersonate Resource Owner in Implicit Flow][OAuth2-Spec-Implicit-Misuse] (Uso indebido del token de acceso para suplantar propietarios de recursos en el flujo implícito) y [OAuth 2.0 Threat Model and Security Considerations][OAuth2-Threat-Model-And-Security-Implications] (Consideraciones de seguridad y modelo de amenazas de OAuth 2.0). Sin embargo, el perfil de mayor riesgo se debe, en gran medida, al hecho de que este tipo de concesión está diseñada para permitir aplicaciones que ejecutan código activo servido por un recurso remoto en un explorador. Si se decanta por una arquitectura de SPA, no dispone de ningún componente de back-end o, en general, tiene planeado invocar una API web a través de JavaScript, se recomienda utilizar el flujo implícito para obtener tokens.

Si su aplicación es un cliente nativo, el flujo implícito no es muy adecuado. La ausencia de la cookie de sesión de Azure AD en el contexto de un cliente nativo priva a la aplicación de los medios necesarios para mantener una sesión de larga duración y obtener los tokens de acceso para los nuevos recursos sin preguntar al usuario repetidamente.

Si está desarrollando una aplicación web que incluye un back-end y que está diseñada para usar API desde el código de back-end, el flujo implícito tampoco es una buena elección. Otras concesiones ofrecen más posibilidades: por ejemplo, la concesión de credenciales de cliente de OAuth2 proporciona la capacidad de obtener los tokens que reflejan los permisos asignados a la aplicación (a diferencia de las delegaciones de usuario), de mantener el acceso mediante programación a los recursos (incluso cuando un usuario no está participando activamente en la sesión), etc. Y no solo eso, sino que dichas concesiones ofrecen mayores garantías de seguridad: los tokens de acceso nunca pasan por el explorador del usuario, así que no hay riesgo de que se guardan en el historial del explorador o, también, la aplicación cliente puede realizar una autenticación sólida cuando se solicita un token.

## Pasos siguientes

- Para obtener una lista completa de recursos para desarrolladores, incluida la información de referencia del conjunto completo de protocolos y flujos de concesión de autorización de OAuth2 compatibles con Azure AD, consulte la [Guía del desarrollador de Azure AD][AAD-Developers-Guide].
- Consulte [Integración con Azure Active Directory][ACOM-How-To-Integrate] para obtener información más detallada sobre el proceso de integración de aplicaciones.

<!--Image references-->
[Scenario-Topology]: ./media/active-directory-devhowto-auth-using-any-aad/multi-tenant-aad-components.png

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

<!---HONumber=AcomDC_0601_2016-->