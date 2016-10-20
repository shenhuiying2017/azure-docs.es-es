<properties
	pageTitle="Restricciones y limitaciones del punto de conexión v2.0 | Microsoft Azure"
	description="Una lista de limitaciones y restricciones con el punto de conexión v2.0 de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="dastrock"/>

# ¿Debo usar el punto de conexión v2.0?

Al crear aplicaciones que se integran con Azure Active Directory, tendrá que decidir si los protocolos de autenticación y el punto de conexión v2.0 cubrirán sus necesidades. El punto de conexión de Azure AD original sigue siendo totalmente compatible y, en algunos aspectos, tiene más características que v2.0. Sin embargo, el punto de conexión v2.0 [introduce importantes ventajas](active-directory-v2-compare.md) para los desarrolladores que pueden convencerle de usar el nuevo modelo de programación.

En este momento, recomendamos usar el punto de conexión v2.0 de la siguiente forma:

- Si desea que se admitan cuentas Microsoft en su aplicación, debería usar el punto de conexión v2.0. Sin embargo, asegúrese de comprender las limitaciones que aparecen en este artículo, sobre todo con las relacionadas específicamente con las cuentas profesionales y educativas.
- Si la aplicación solo requiere cuentas profesionales o educativas, debe usar [los puntos de conexión de Azure AD originales](active-directory-developers-guide.md).

Con el tiempo, desarrollaremos el punto de conexión v2.0 para eliminar las restricciones que mencionamos en este artículo, por lo que siempre debe usar este punto de conexión. Mientras tanto, este artículo está pensado para ayudarle a determinar si el punto de conexión v2.0 es para usted. Seguiremos actualizando este artículo a lo largo del tiempo para reflejar el estado actual del punto de conexión v2.0, así que consúltelo de nuevo para volver a evaluar sus requisitos en relación con las funcionalidades de v2.0.

Si tiene una aplicación existente con Azure AD que no utiliza el punto de conexión v2.0, no es necesario empezar desde cero. En el futuro, proporcionaremos una forma de habilitar las aplicaciones de Azure AD existentes para usarlas con el punto de conexión v2.0.

## Restricciones en las aplicaciones
Los siguientes tipos de aplicaciones no son compatibles actualmente con el punto de conexión v2.0. Para obtener una descripción de los tipos de aplicaciones admitidos, consulte [este artículo](active-directory-v2-flows.md).

##### API web independiente
Con el punto de conexión v2.0, tiene la capacidad de [crear una API web protegida mediante OAuth 2.0](active-directory-v2-flows.md#web-apis). Sin embargo, esa API web solo podrá recibir tokens de una aplicación que comparta el mismo identificador de aplicación. No se admite la creación de una API web a la que se acceda con otro identificador de aplicación. Ese cliente no podrá solicitar u obtener permisos para su API web.

Para ver cómo se crea una API web que acepte tokens de un cliente con el mismo identificador de aplicación, consulte los ejemplos de API web del punto de conexión v2.0 en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

##### Flujo en nombre de la API web
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante el punto de conexión v2.0. Este escenario es común en los clientes nativos que tienen una API web de back-end, que, a su vez, llama a un servicio de Microsoft Online u otra API web creada a medida que admite Azure AD.

Este escenario puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no se admite actualmente para el punto de conexión v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de código "en nombre de" en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restricciones en los registros de aplicaciones
En este momento, todas las aplicaciones que deseen integrarse en el punto de conexión v2.0 deben crear un nuevo registro de aplicaciones en [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Ninguna aplicación de Azure AD o de cuenta de Microsoft existente será compatible con el punto de conexión v2.0, ni tampoco las aplicaciones registradas en cualquier portal que no sea el nuevo portal de registro de aplicaciones. Tenemos planeado proporcionar una manera de habilitar aplicaciones existentes para usarse como una aplicación de la versión 2.0. Sin embargo, en este momento, hay ninguna ruta de migración para esto.

De forma similar, las aplicaciones registradas en el nuevo portal de registro de aplicaciones no funcionarán en el punto de conexión de autenticación de Azure AD original. Sin embargo, puede usar las aplicaciones creadas en el portal de registro de aplicaciones para una integración correcta con el punto de conexión de autenticación de cuentas de Microsoft, `https://login.live.com`.

Además, los registros de aplicación creados en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) tienen las siguientes observaciones:

- No se admite la propiedad **homepage** también se conoce como "**dirección URL de inicio de sesión**". Sin una página de inicio, estas aplicaciones no se mostrarán en el panel de Office MyApps.
- En este momento, solo se permiten dos secretos de aplicación por cada identificador de aplicación.
- Un registro de aplicación solo puede verse y administrarse mediante una cuenta de desarrollador única. No se puede compartir entre varios desarrolladores.
- Existen varias restricciones del formato de redirect\_uri permitido. Vea la siguiente sección para obtener más información.

## Restricciones en los URI de redireccionamiento
Las aplicaciones que se registran en el nuevo portal de registro de aplicaciones están restringidas actualmente a un conjunto limitado de valores de parámetro redirect\_uri. El URI de redireccionamiento de aplicaciones y servicios web debe comenzar por el esquema `https`, y todos los valores de URI de redireccionamiento deben compartir un único dominio DNS. Por ejemplo, no es posible registrar una aplicación web que tiene varios redirect\_uri:

`https://login-east.contoso.com` `https://login-west.contoso.com`

El sistema de registro compara el nombre DNS completo de los elementos redirect\_uri existentes con el nombre DNS del redirect\_uri que va a agregar. Se producirá un error en la solicitud para agregar el nombre DNS si se cumple algunas de las condiciones siguientes:

- Si el nombre DNS completo del nuevo redirect\_uri no coincide con el nombre DNS del redirect\_uri existente
- Si el nombre DNS completo del nuevo redirect\_uri no es un subdominio del redirect\_uri existente

Por ejemplo, si la aplicación tiene el redirect\_uri:

`https://login.contoso.com`

A continuación, es posible agregar:

`https://login.contoso.com/new`

que coincide exactamente con el nombre DNS, o:

`https://new.login.contoso.com`

que es un subdominio DNS de login.contoso.com. Si desea que una aplicación tenga login-east.contoso.com y login-west.contoso.com como redirect\_uris, debe agregar los redirect\_uris siguientes en orden:

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

Los dos últimos se pueden agregar porque son subdominios del primer redirect\_uri, contoso.com. Esta limitación se eliminará en una próxima versión.

Para obtener información sobre cómo registrar una aplicación en el nuevo portal de registro de aplicaciones, consulte [este artículo](active-directory-v2-app-registration.md).

## Restricciones en los servicios y API
Actualmente, el punto de conexión v2.0 admite el inicio de sesión en cualquier aplicación registrada en el nuevo portal de registro de aplicaciones, siempre que se encuentre en la lista de [flujos de autenticación admitidos](active-directory-v2-flows.md). Sin embargo, estas aplicaciones sólo podrán adquirir tokens de acceso de OAuth 2.0 para un conjunto de recursos muy limitado. El extremo v2.0 solo emitirá access\_tokens para:

- La aplicación que solicita el token. Una aplicación puede adquirir un elemento access\_token por sí misma si la aplicación lógica se compone de varios componentes o niveles diferentes. Para ver este escenario en acción, consulte nuestros tutoriales de [Introducción](active-directory-appmodel-v2-overview.md#getting-started).
- El correo electrónico de Outlook, el calendario y los contactos de las API de REST se encuentran en https://outlook.office.com. Para obtener información sobre cómo compilar una aplicación que tenga acceso a estas API, consulte estos tutoriales de [Introducción a Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- Las API de Microsoft Graph. Para más información acerca de Microsoft Graph y todos los datos que está disponibles, consulte [https://graph.microsoft.io](https://graph.microsoft.io).

No hay otros servicios compatibles en este momento. Se agregarán más servicios de Microsoft Online en el futuro, así como la compatibilidad con sus propios servicios y API web.

## Restricciones en las bibliotecas y SDK
En este momento, la compatibilidad con bibliotecas del punto de conexión v2.0 es bastante limitada. Si desea utilizar el punto de conexión v2.0 en una aplicación de producción, tiene las siguientes opciones:

- Si está creando una aplicación web, puede utilizar sin ningún riesgo nuestro software intermedio del lado del servidor disponible con carácter general, para realizar el inicio de sesión y la validación de tokens. Incluye el software intermedio OWIN Open ID Connect para ASP.NET y nuestro complemento NodeJS Passport. También hay ejemplos de código con este middleware en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).
- Para otras plataformas y aplicaciones nativas y móviles, también puede integrarse con el punto de conexión v2.0 directamente enviando y recibiendo mensajes de protocolo en el código de su aplicación. Los protocolos v2.0 OpenID Connect y OAuth [se han documentado explícitamente](active-directory-v2-protocols.md) para ayudarle a realizar dicha integración.
- Por último, puede usar las bibliotecas de código abierto de Open ID Connect y OAuth para integrarse con el punto de conexión v2.0. El protocolo v2.0 debe ser compatible con muchas bibliotecas de código abierto de los protocolos sin cambios importantes. La disponibilidad de estas bibliotecas varía según la plataforma y el lenguaje, y en los sitios web de [OpenID Connect](http://openid.net/connect/) y [OAuth 2.0](http://oauth.net/2/) se mantiene una lista de las implementaciones más populares. Consulte [Azure Active Directory (AD) v2.0 and authentication libraries](active-directory-v2-libraries.md) (Azure Active Directory (AD) v2.0 y bibliotecas de autenticación) para obtener más información, así como la lista de bibliotecas de cliente de código abierto y ejemplos que se han probado con el punto de conexión v2.0.

También hemos publicado una vista preliminar inicial de la [biblioteca de autenticación de Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) solo para .NET. Si lo desea, puede probar esta biblioteca en aplicaciones .NET de cliente y servidor, pero como se trata de una biblioteca de versión preliminar, no dispone de soporte con calidad GA.

## Restricciones en los protocolos
El punto de conexión v2.0 solo es compatible con OpenID Connect y OAuth 2.0. Sin embargo, no se han incorporado todas las características y funcionalidades de cada protocolo en el punto de conexión v2.0:

- El punto de conexión `end_session_endpoint` de OpenID Connect, lo que permite que una aplicación finalice la sesión del usuario con el punto de conexión v2.0.
- Los elementos id\_tokens que emite el punto de conexión v2.0 contienen un identificador en pares del usuario. Es decir, dos aplicaciones diferentes recibirán identificadores diferentes para el mismo usuario. Tenga en cuenta que al consultar el punto de conexión `/me` de Microsoft Graph, podrá obtener un identificador correlacionable del usuario que puede utilizarse en diferentes aplicaciones.
- En estos momentos, los elementos id\_tokens que emite el punto de conexión v2.0 no contienen una notificación `email` del usuario, aunque adquiera el permiso del usuario para ver su correo electrónico.
- El punto de conexión de información de usuario de OpenID Connect. En estos momentos, el punto de conexión de información de usuario no está implementado en el punto de conexión v2.0. Sin embargo, todos los datos de perfil de usuario que recibiría posiblemente en este punto de conexión están disponible en el punto de conexión `/me` de Microsoft Graph.
- Notificaciones de grupos y roles. En este momento, el punto de conexión v2.0 no admite la emisión de notificaciones de roles o grupos en id\_tokens.

Para comprender mejor el alcance de la funcionalidad del protocolo compatible con el punto de conexión v2.0, lea nuestra [referencia de protocolos de v2.0: OAuth 2.0 y OpenID Connect](active-directory-v2-protocols.md).

## Restricciones de las cuentas profesionales y educativas
Hay algunas características específicas de los usuarios de empresa u organización de Microsoft que todavía no admite el punto de conexión v2.0.

##### Acceso condicional basado en dispositivos, aplicaciones nativas y móviles, y Microsoft Graph
El punto de conexión v2.0 no admite aún la autenticación de dispositivos para aplicaciones móviles y nativas, como las que se ejecutan en iOS o Android, ya que podría impedir que la aplicación nativa llame a Microsoft Graph en determinadas organizaciones. La autenticación de dispositivos se requiere cuando un administrador establece una directiva de acceso condicional basado en dispositivos en una aplicación. Para el punto de conexión v2.0, el escenario más probable para el acceso condicional basado en dispositivos es un administrador que configura una directiva en un recurso de Microsoft Graph, como la API de Outlook. Si un administrador establece esta directiva y su aplicación nativa solicita un token a Microsoft Graph, se producirá un error en la solicitud debido a que todavía no se admite la autenticación de dispositivos. No obstante, las aplicaciones web que solicitan token a Microsoft Graph se admiten cuando se configuran directivas basadas en dispositivos. En el escenario de las aplicaciones web, la autenticación de dispositivos se realiza a través del explorador web del usuario.

Como desarrollador, probablemente no pueda controlar cuándo se establecen las directivas en los recursos de Microsoft Graph ni estar al tanto de cuándo esto sucede. Si está creando una aplicación para usuarios profesionales o educativos, debe usar [el punto de conexión de Azure AD original](active-directory-developers-guide.md) hasta que el punto de conexión v2.0 admita la autenticación de dispositivo. Para obtener más información sobre el acceso condicional basado en dispositivos, consulte [este artículo](active-directory-conditional-access.md#device-based-conditional-access).

##### Autenticación integrada de Windows para inquilinos federados
Si ha usado ADAL (y, por tanto, el punto de conexión de Azure AD original) en aplicaciones Windows, puede que haya utilizado lo que se conoce como "concesión de aserción SAML". Esta concesión permite a los inquilinos de Azure AD federado autenticarse de manera silenciosa con su instancia local de Active Directory sin tener que escribir las credenciales. La concesión de aserción SAML no se admite actualmente en el punto de conexión v2.0.

<!---HONumber=AcomDC_0928_2016-->