---
title: "Autenticación de Azure Active Directory y Resource Manager | Microsoft Docs"
description: "Guía del desarrollador para la autenticación con la API de Azure Resource Manager y Azure Active Directory a fin de integrar una aplicación con otras suscripciones de Azure."
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2017
ms.author: dugill;tomfitz
ms.openlocfilehash: 0b7ddaa7e8a98cdff0e92c87f8a1f7e24efbd67e
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Uso de la API de autenticación de Resource Manager para acceder a suscripciones
## <a name="introduction"></a>Introducción
A los desarrolladores de software que necesitan crear una aplicación que administre los recursos de Azure de un cliente, este artículo les muestra cómo realizar la autenticación con las API de Azure Resource Manager y obtener acceso a los recursos de otras suscripciones.

Una aplicación puede acceder a las API de Resource Manager de cualquiera de estas dos formas:

1. **Acceso de usuario + aplicación**: para aplicaciones que acceden a los recursos en nombre de un usuario con una sesión iniciada. Este enfoque funciona con aplicaciones, como aplicaciones web y herramientas de línea de comandos, que se encargan solo de la "administración interactiva" de los recursos de Azure.
2. **Acceso de solo aplicación**: para las aplicaciones que ejecutan servicios de demonio y trabajos programados. A la identidad de la aplicación se le concede acceso directo a los recursos. Este enfoque funciona para aplicaciones que necesitan acceso desatendido a largo plazo a Azure.

En este artículo se proporciona instrucciones detalladas de cómo crear una aplicación que emplea ambos métodos de autorización. Muestra cómo realizar cada paso con la API de REST o C#. La aplicación ASP.NET MVC completa está disponible en [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Lo que hace la aplicación web
La aplicación web:

1. Inicia sesión de un usuario de Azure.
2. Pide al usuario que conceda a la aplicación web acceso a Resource Manager.
3. Obtiene un token de acceso de usuario + aplicación para acceder a Resource Manager.
4. Usa el token (del paso 3) para asignar la entidad de servicio de la aplicación a un rol en la suscripción. Este paso proporciona el acceso a largo plazo de la aplicación a la suscripción.
5. Obtiene un token de acceso de solo aplicación.
6. Utiliza el token (del paso 5) para administrar los recursos de la suscripción a través de Resource Manager.

Aquí está el flujo de la aplicación web.

![Flujo de autenticación de Resource Manage](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como usuario, proporcione el identificador de suscripción para especificar la suscripción que quiere usar:

![proporcionar id. de suscripción](./media/resource-manager-api-authentication/sample-ux-1.png)

Seleccione la cuenta que se va a usar para iniciar sesión.

![seleccionar cuenta](./media/resource-manager-api-authentication/sample-ux-2.png)

Proporcione sus credenciales.

![proporcionar credenciales](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda a la aplicación acceso a las suscripciones de Azure:

![Conceder acceso](./media/resource-manager-api-authentication/sample-ux-4.png)

Administre las suscripciones conectadas:

![Conectar suscripción](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registre la aplicación
Antes de comenzar a codificar, registre la aplicación web con Azure Active Directory (AD). El registro de aplicación crea una identidad central para la aplicación en Azure AD. Contiene información básica acerca de la aplicación, como el Id. de cliente de OAuth, las direcciones URL de respuesta y las credenciales que utiliza la aplicación para autenticarse y acceder a las API de Azure Resource Manager. El registro de aplicación también registra los distintos permisos delegados que necesita la aplicación al acceder a las API de Microsoft en nombre del usuario.

Dado que la aplicación tiene acceso a otra suscripción, debe configurarla como una aplicación multiinquilino. Para aprobar la validación, proporcione un dominio asociado a Azure Active Directory. Para ver los dominios asociados a Azure Active Directory, inicie sesión en el portal.

En el ejemplo siguiente se muestra cómo registrar la aplicación con Azure PowerShell. Debe tener la versión más reciente (agosto de 2016) de Azure PowerShell para que funcione este comando.

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Para iniciar sesión como la aplicación de AD, necesita el identificador y la contraseña de la aplicación. Para ver el identificador de aplicación que se devuelve desde el comando anterior, use:

    $app.ApplicationId

En el ejemplo siguiente se muestra cómo registrar la aplicación con la CLI de Azure.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Los resultados incluyen AppId, que necesita para autenticarse como la aplicación.

### <a name="optional-configuration---certificate-credential"></a>Configuración opcional: credencial de certificado
Azure AD también admite credenciales de certificado para las aplicaciones: cree un certificado autofirmado, mantenga la clave privada y agregue la clave pública al registro de la aplicación Azure AD. Para la autenticación, la aplicación envía una carga pequeña a Azure AD firmada con su clave privada y Azure AD valida la firma mediante la clave pública que se ha registrado.

Para obtener más información sobre cómo crear una aplicación de AD con un certificado, consulte [Uso de Azure PowerShell para crear una entidad de servicio para acceder a recursos](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) o [Uso de la CLI de Azure para crear una entidad de servicio para acceder a recursos](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Obtención de un identificador de inquilino a partir del identificador de suscripción
Para solicitar un token que se pueda utilizar para llamar a Resource Manager, la aplicación necesita conocer el identificador del inquilino de Azure AD que hospeda la suscripción de Azure. Es muy probable que los usuarios conozcan sus identificadores de suscripción, pero quizá no sus identificadores de inquilino para Azure Active Directory. Para obtener el identificador de inquilino del usuario, solicítele el identificador de suscripción. Proporcione ese identificador de suscripción al enviar una solicitud acerca de la suscripción:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Se produce un error en la solicitud porque el usuario aún no ha iniciado sesión pero puede recuperar el identificador de inquilino en la respuesta. En esa excepción, recupere el identificador de inquilino en el valor de encabezado de la respuesta de **WWW-Authenticate**. Consulte esta implementación en el método [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obtención de un token de acceso de usuario + aplicación
La aplicación redirige al usuario a Azure AD con una solicitud de autorización de OAuth 2.0 (para autenticar las credenciales del usuario y obtener un código de autorización). La aplicación utiliza el código de autorización para obtener un token de acceso para Resource Manager. El método [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) crea la solicitud de autorización.

Este artículo muestra las solicitudes de la API de REST para autenticar al usuario. También puede utilizar bibliotecas auxiliares para realizar la autenticación en el código. Para obtener más información sobre estas bibliotecas, consulte [Bibliotecas de autenticación de Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Para obtener instrucciones sobre la integración de la administración de identidades en una aplicación, consulte la [Guía del desarrollador de Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Solicitud de autenticación (OAuth 2.0)
Emita una solicitud de autorización de Open ID Connect u OAuth2.0 para el punto de conexión de autorización de Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Los parámetros de la cadena de consulta disponibles para esta solicitud se describen en el artículo sobre [solicitud de un código de autorización](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code).

En el ejemplo siguiente se muestra cómo solicitar una autorización de OAuth2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica al usuario y, si es necesario, pide al usuario que conceda permiso a la aplicación. Devuelve el código de autorización a la dirección URL de respuesta de la aplicación. Dependiendo del parámetro response_mode solicitado, Azure AD devuelve los datos en la cadena de consulta o como datos POST.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Solicitud de autenticación (Open ID Connect)
Si no solo desea acceder a Azure Resource Manager en nombre del usuario, sino también permitirle usar su propia cuenta de Azure AD para iniciar sesión en la aplicación, emita una solicitud de autorización de Open ID Connect. Con Open ID Connect, la aplicación recibe también un id_token de Azure AD que puede utilizar para el inicio de sesión del usuario.

Los parámetros de la cadena de consulta disponibles para esta solicitud se describen en el artículo [Envío de la solicitud de inicio de sesión](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request).

Este es un ejemplo de solicitud de Open ID Connect:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica al usuario y, si es necesario, pide al usuario que conceda permiso a la aplicación. Devuelve el código de autorización a la dirección URL de respuesta de la aplicación. Dependiendo del parámetro response_mode solicitado, Azure AD devuelve los datos en la cadena de consulta o como datos POST.

Este es un ejemplo de respuesta de Open ID Connect:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Solicitud de token (flujo de concesión de códigos de OAuth2.0)
Una vez que la aplicación haya recibido el código de autorización de Azure AD, es el momento de obtener el token de acceso para Azure Resource Manager.  Publique una solicitud de token de concesión de códigos de OAuth2.0 en el punto de conexión del token de Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Los parámetros de la cadena de consulta disponibles para esta solicitud se describen en el artículo sobre [uso del código de autorización](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

El ejemplo siguiente muestra una solicitud de token de concesión de códigos con la credencial de contraseña:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Cuando trabaje con credenciales de certificado, cree un JSON Web Token (JWT) y una firma (RSA SHA256) mediante la clave privada de la credencial de certificado de la aplicación. Se muestran los tipos de notificación para el token en [Notificaciones de token JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Para referencia, consulte el [código de la biblioteca de autorización de Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para firmar tokens JWT de aserción de cliente.

Consulte la [especificación de Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para más información acerca de la autenticación de cliente.

El ejemplo siguiente muestra una solicitud de token de concesión de códigos con una credencial de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Un ejemplo de respuesta de token de concesión de códigos:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Control de la respuesta de token de concesión de códigos
Una respuesta correcta del token contiene el token de acceso (usuario + aplicación) para Azure Resource Manager. La aplicación utiliza este token de acceso para acceder a Resource Manager en nombre del usuario. Los tokens de acceso emitidos por Azure AD tienen una duración de una hora. Es poco probable que la aplicación web necesite renovar el token de acceso (usuario + aplicación). Si necesita renovar el token de acceso, utilice el token de actualización que recibe la aplicación en la respuesta del token. Publique una solicitud de token de OAuth2.0 en el punto de conexión del token de Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Los parámetros que se usan con la solicitud de actualización se describen en [Actualización de los tokens de acceso](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

En el ejemplo siguiente se muestra cómo utilizar el token de actualización:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Aunque se pueden usar tokens de actualización para obtener nuevos tokens de acceso para Azure Resource Manager, no son adecuados para el acceso sin conexión por parte de la aplicación. La duración de los tokens de actualización está limitada y los tokens de actualización están enlazados al usuario. Si el usuario abandona la organización, la aplicación que usa el token de actualización pierde el acceso. Este enfoque no es adecuado para las aplicaciones que se utilizan los equipos para administrar los recursos de Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Comprobación de que el usuario pueda asignar acceso a la suscripción
Su aplicación ahora tiene un token para acceder a Azure Resource Manager en nombre del usuario. El siguiente paso es conectar la aplicación con la suscripción. Después de conectarse, la aplicación puede administrar estas suscripciones incluso si el usuario no está presente (acceso sin conexión a largo plazo).

En cada suscripción que haya que conectar, llame a la API de [enumeración de permisos de Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) para determinar si el usuario tiene derechos de administración de acceso en la suscripción.

El método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

En el ejemplo siguiente se muestra cómo solicitar permisos de un usuario en una suscripción. 83cfe939-2402-4581-b761-4f59b0a041e4 es el identificador de la suscripción.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Un ejemplo de la respuesta para obtener los permisos del usuario en la suscripción es:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

La API de permisos devuelve varios permisos. Cada permiso consta de acciones permitidas (**actions**) y acciones no permitidas (**notactions**). Si una acción está presente en las acciones permitidas de cualquier permiso y no está presente en las acciones no permitidas de dicho permiso, el usuario puede realizar dicha acción. **microsoft.authorization/roleassignments/write** es la acción que concede derechos de administración de acceso. La aplicación debe analizar el resultado de los permisos para buscar una coincidencia de regex en esta cadena de acción en las **actions** y **notactions** de cada permiso.

## <a name="get-app-only-access-token"></a>Obtención de un token de acceso de solo aplicación
Ahora ya sabe si el usuario puede asignar acceso a la suscripción de Azure. Los pasos siguientes son:

1. Asignar el rol RBAC adecuado a la identidad de la aplicación en la suscripción.
2. Validar la asignación de acceso al consultar el permiso de la aplicación en la suscripción o acceder a Resource Manager con el token de solo aplicación.
3. Registrar la conexión en la estructura de datos de "suscripciones conectadas" de las aplicaciones (sin perder el identificador de la suscripción).

Examinemos más detenidamente el primer paso. Para asignar el rol RBAC adecuado a la identidad de la aplicación, es preciso determinar:

* El identificador de objeto de la identidad de la aplicación en el directorio de Azure Active Directory del usuario
* El identificador del rol RBAC que requiere la aplicación en la suscripción

Cuando la aplicación autentica a un usuario desde un directorio de Azure AD, crea un objeto de entidad de servicio para la aplicación en dicho directorio de Azure AD. Azure permite asignar roles RBAC a entidades de servicio, con el fin de conceder acceso directo a las aplicaciones correspondientes en los recursos de Azure. Esta acción es exactamente lo que desea hacer. Consulte la API de Azure AD Graph para determinar el identificador de la entidad de servicio de la aplicación en el directorio de Azure AD del usuario que inició sesión.

Solo tiene un token de acceso para Azure Resource Manager (se necesita un token de acceso nuevo para llamar a la API de Azure AD Graph). Todas las aplicaciones de Azure AD tienen permiso para consultar su propio objeto de entidad de servicio, por lo que bastará con un token de acceso de solo aplicación.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obtención de token de acceso de solo aplicación para la API de Azure AD Graph
Para autenticar una aplicación y obtener un token para la API de Azure AD Graph, emita una solicitud de token de flujo de OAuth2.0 de concesión de credenciales de cliente al punto de conexión del token de Azure AD (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

El método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) de la aplicación de ejemplo ASP.NET MVC obtiene un token de acceso de solo aplicación para la API Graph mediante la Biblioteca de autenticación de Active Directory para .NET.

Los parámetros de la cadena de consulta disponibles para esta solicitud se describen en el artículo [Solicitar un token de acceso](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token).

Una solicitud de ejemplo de token de concesión de credenciales de cliente:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Una respuesta de ejemplo de token de concesión de credenciales de cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtención del id. de objeto de entidad de servicio de aplicación en la instancia de Azure AD de usuario
Ahora use el token de acceso de solo aplicación para consultar la API de [entidades de servicio de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) para determinar el identificador de objeto de la entidad de servicio de la aplicación en el directorio.

El método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

En el ejemplo siguiente se muestra cómo solicitar una entidad de servicio de aplicación. a0448380-c346-4f9f-b897-c18733de9394 es el identificador de cliente de la aplicación.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

En el ejemplo siguiente se muestra una respuesta a la solicitud de una entidad de servicio de aplicación

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obtención del identificador del rol RBAC de Azure
Para asignar el rol RBAC adecuado a la entidad de servicio, debe determinar el identificador del rol RBAC de Azure.

El rol RBAC correcta para su aplicación:

* Si la aplicación solo supervisa la suscripción, sin realizar cambio alguno, solo necesita permisos de lector en la suscripción. Asigne el rol **Lector** .
* Si la aplicación administra Azure, la suscripción, la creación, modificación o eliminación de entidades, requiere uno de los permisos de colaborador.
  * Para administrar un tipo concreto de recurso, asigne los roles de colaborador específicos del recurso (colaborador de máquina virtual, colaborador de red virtual, colaborador de cuenta de almacenamiento, etc.).
  * Para administrar cualquier tipo de recurso, asigne el rol **Colaborador** .

La asignación de roles de la aplicación es visible para los usuarios, así que seleccione el mínimo privilegio necesario.

Llame a la [API de definición de roles de Resource Manager](https://docs.microsoft.com/rest/api/authorization/roledefinitions) para enumerar todos los roles RBAC de Azure y busque e itere en el resultado para buscar la definición de rol que desee por nombre.

El método [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

El siguiente ejemplo de solicitud muestra cómo obtener el identificador del rol RBAC de Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb es el identificador de la suscripción.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La respuesta está en el formato siguiente:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

No es preciso llamar a esta API de forma continua. Una vez que haya determinado el GUID conocido de la definición de rol, puede construir el identificador de la definición de rol como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Estos son los identificadores de los roles integrados que se usan habitualmente:

| Rol | GUID |
| --- | --- |
| Lector |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Colaborador |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Colaborador de la máquina virtual |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Colaborador de la red virtual |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Colaborador de la cuenta de almacenamiento |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Colaborador de sitio web |de139f84-1756-47ae-9be6-808fbbe84772 |
| Colaborador de plan web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Colaborador de SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Colaborador de SQL DB |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Asignación de un rol RBAC a una aplicación
Ya tiene todo lo que necesita para asignar el rol RBAC adecuado a la entidad de servicio mediante la API de [creación de asignación de roles de Resource Manager](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

El método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

Un ejemplo de solicitud para asignar el rol RBAC a una aplicación:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

En la solicitud, se utilizan los siguientes valores:

| Guid | Description |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |el identificador de la suscripción |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |el identificador de objeto de la entidad de servicio de la aplicación |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |el identificador del rol Lector |
| 4f87261d-2816-465d-8311-70a27558df4c |un guid nuevo creado para la nueva asignación de rol |

La respuesta está en el formato siguiente:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obtención de token de acceso de solo aplicación para Azure Resource Manager
Para validar que la aplicación tenga el acceso deseado en la suscripción, realice una tarea de prueba en la suscripción mediante un token de aplicación.

Para obtener un token de acceso de solo aplicación, siga las instrucciones de la sección [Obtención de token de acceso de solo aplicación para la API de Azure AD Graph](#app-azure-ad-graph), con un valor diferente para el parámetro del recurso:

    https://management.core.windows.net/

El método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de la aplicación de ejemplo ASP.NET MVC obtiene un token de acceso de solo aplicación para Azure Resource Manager mediante la Biblioteca de autenticación de Active Directory para .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Obtención de permisos de la aplicación en la suscripción
Para comprobar que la aplicación tenga el acceso deseado en una suscripción de Azure, puede llamar también a la API de [permisos de Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) . Este enfoque es similar al modo en que se determina si el usuario tiene derechos de administración de acceso en la suscripción. Sin embargo, esta vez, llame a la API de permisos con el token de acceso de solo aplicación que recibió en el paso anterior.

El método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

## <a name="manage-connected-subscriptions"></a>Administración de suscripciones conectadas
Cuando se asigna el rol RBAC adecuado a la entidad de servicio de la aplicación de la suscripción, la aplicación puede seguir supervisándola o administrándola mediante tokens de acceso de solo aplicación para Azure Resource Manager.

Si el propietario de la suscripción quita la asignación de rol de la aplicación mediante el portal o las herramientas de la línea de comandos, la aplicación ya no puede acceder a dicha suscripción. En ese caso, debe notificar al usuario que se ha roto la conexión con la suscripción desde fuera de la aplicación y darle la opción de "reparar" la conexión. La "reparación" volvería a crear la asignación de roles que se eliminó sin conexión.

Igual que permitió que el usuario conectara sus suscripciones a la aplicación, también debe permitirle que las desconecte. Desde el punto de vista de la administración de acceso, desconectar significa quitar la asignación de roles que la entidad de servicio de la aplicación tiene en la suscripción. Opcionalmente, también se pueden quitar todos los estados de la aplicación para la suscripción.
Solo los usuarios con permiso de administración de acceso en la suscripción pueden desconectar la suscripción.

El método [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

Eso es todo: los usuarios ya pueden conectarse y administrar fácilmente sus suscripciones de Azure con su aplicación.
