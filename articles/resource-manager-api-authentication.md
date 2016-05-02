<properties 
   pageTitle="Autorización con la API de Resource Manager | Microsoft Azure"
   description="Guía para desarrolladores para la autorización con la API de Azure Resource Manager y Active Directory para integrar una aplicación en Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/18/2016"
   ms.author="dugill;tomfitz" />


# Guía para desarrolladores para la autorización con la API de Azure Resource Manager

A los desarrolladores de software que deseen integrar una aplicación en Azure o administrar los recursos de Azure de un cliente, este tema les mostrará cómo realizar la autenticación con las API de Azure Resource Manager.

Una aplicación puede acceder a las API de Resource Manager de cualquiera de estas dos formas:

1. **Acceso de usuario + aplicación**: este método se usa para aquellas aplicaciones que acceden a los recursos en nombre de un usuario con una sesión iniciada. Funciona con las aplicaciones, como las aplicaciones web y las herramientas de línea de comandos, que se encargan solo de la "administración interactiva" de los recursos de Azure.
1. **Acceso solo de aplicación**: este método se usa cuando hay que conceder a la identidad de la aplicación acceso directo a los recursos. Funciona con servicios demonio y trabajos programados que necesitan "acceso sin conexión" a largo plazo a Azure.

En este tema proporciona una descripción detallada de cómo crear una aplicación que emplea ambos métodos de autorización.

Va a crear una aplicación web que:

1. Inicie sesión de un usuario de Azure
2. Consulta Resource Manager en nombre del usuario (acceso de usuario + aplicación) para obtener una lista de las suscripciones a Azure que el usuario posee
3. Permite al usuario "conectar" suscripciones a la aplicación; con lo que se concede acceso directo en la suscripción a la aplicación
4. Accede a Resource Manager como la aplicación para realizar operaciones sin conexión (acceso solo de aplicación)

Este es el flujo de un extremo a otro de la aplicación web que va a escribir.

![Autenticación ARM - registro de la aplicación 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

Todo el código de este tema se ejecuta en forma de aplicación web, que puede probar en [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense).

El usuario selecciona el tipo de cuenta que va a utilizar para iniciar sesión:

![seleccionar inicio de sesión](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

Proporcione sus credenciales.

![proporcionar credenciales](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

Conceda a la aplicación acceso a las suscripciones de Azure:
 
 ![Conceder acceso](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
Conecte sus suscripciones a la aplicación para supervisarlas:

![Conectar suscripción](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

Desconecte o repare la conexión a la aplicación:

![Desconectar suscripción](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Registro de la aplicación con Azure Active Directory

Empiece por registrar la aplicación web en Azure Active Directory (AD). El registro de aplicación crea una identidad central para la aplicación en Azure AD. Contiene información básica acerca de la aplicación, como el Id. de cliente de OAuth, las direcciones URL de respuesta y las credenciales que utilizará la aplicación para autenticar y acceder a las API de Azure Resource Manager. El registro de aplicación también registra los distintos permisos delegados que necesitan las aplicaciones al acceder a las API de Microsoft en nombre del usuario.

El tema [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](resource-group-create-service-principal-portal.md) muestra todos los pasos necesarios para configurar una aplicación. Consulte dicho tema cuando cree una aplicación con las siguientes propiedades:

- Aplicación web denominada **CloudSense**
- Dirección URL de inicio de sesión e identificador URI de la aplicación con el formato **http://{domain_name_of_your_directory}/{name_of_the_app}**.
- Clave de autenticación para iniciar sesión en la aplicación
- Permiso delegado **Acceder a administración de servicios de Azure** para **API de administración de servicios de Azure**. Deje el valor predeterminado **Habilitar inicio de sesión único y leer el perfil del usuario** en **Azure Active Directory**.
- Aplicación multiinquilino

### Configuración opcional: credencial de certificado

Azure AD también admite credenciales de certificado para las aplicaciones: cree un certificado autofirmado, mantenga la clave privada y agregue la clave pública al registro de la aplicación Azure AD. Para la autenticación, la aplicación envía una carga pequeña a Azure AD firmada con su clave privada y Azure AD valida la firma mediante la clave pública que se ha registrado.

Para más información acerca de cómo configurar el certificado, consulte [Build service and daemon apps in Office 365](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365) (Crear aplicaciones demonio y de servicio en Office 365). La sección "Configuring a X.509 public cert for your application" (Configuración de un certificado público X.509 para una aplicación) contiene instrucciones detalladas para configurar el certificado. O bien, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md) para obtener ejemplos de configuración de un certificado a través de Azure PowerShell o la CLI de Azure.

## Autenticación de usuario y obtención de token de acceso

Ya dispone de todo lo necesario para empezar a trabajar a codificar la aplicación. Este tema proporciona ejemplos de la API de REST para cada paso del flujo de un extremo a otro y vínculos al código C# relevante para cada paso. La aplicación de ejemplo ASP.NET MVC completa está disponible en [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Comience en el momento en que el usuario decide conectar su suscripción de Azure a su aplicación.

Debe pedir la siguiente información al usuario:

1. **Nombre de dominio de directorio**: el nombre de dominio de Azure Active Directory asociado a la suscripción de Azure del usuario. La solicitud de autorización de OAuth 2.0 se debe enviar a este Azure AD. Para averiguar el nombre de dominio de su Azure AD, el usuario debe navegar hasta el Portal de Azure y seleccionar la cuenta de la esquina superior derecha. Puede proporcionar instrucciones visuales al usuario como: 

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Cuenta de Microsoft frente a cuenta profesional**: determine si el usuario administra su suscripción a Azure con una cuenta de Microsoft (también conocida como Live ID) o una cuenta profesional (también conocido como cuenta de organización). Si usa una cuenta de Microsoft, la aplicación redirigirá al usuario a la página de inicio de sesión de Azure Active Directory con un parámetro de cadena de consulta (&domain\_hint=live.com) que indicará a Azure AD que lleve al usuario directamente a la página de inicio de sesión de la cuenta de Microsoft. El código de autorización y los tokens que recibe para cualquiera de los tipos de cuenta se procesarán de la misma manera.

Luego, la aplicación redirige al usuario a Azure AD con una solicitud de autorización de OAuth 2.0 (para autenticar las credenciales del usuario y obtener un código de autorización). La aplicación utilizará el código de autorización para obtener un token de acceso para Resource Manager.

### Solicitud de autenticación (OAuth 2.0)

Emita una solicitud de autorización de Open ID Connect o OAuth2.0 para el punto de conexión de autorización de Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

Los parámetros de la cadena de consulta disponibles para esta solicitud se describen en el tema [Flujo de concesión de códigos de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx).

En el ejemplo siguiente se muestra cómo solicitar una autorización de OAuth2.0:

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica al usuario y, si se requiere, pide al usuario que conceda permiso a la aplicación. Devuelve el código de autorización a la dirección URL de respuesta de la aplicación. Dependiendo del parámetro response\_mode solicitado Azure AD devuelve los datos en la cadena de consulta o como datos POST.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Solicitud de autenticación (Open ID Connect)

Si no solo desea acceder a Azure Resource Manager en nombre del usuario, sino también permitir que el usuario inicie sesión en su aplicación con su cuenta de Azure AD, emita una solicitud de autorización de Open ID Connect. Con Open ID Connect, la aplicación recibirá también un id\_token de Azure AD que puede utilizar para el inicio de sesión del usuario.

Los parámetros de la cadena de consulta de solicitud de autorización de OAuth2.0 son:

| Parámetro de cadena de consulta | Valor
|----|----
| client\_id | Id. de cliente de la aplicación
| response\_mode | **form\_post** o **query**
| response\_type | **code+id\_token**
| redirect\_uri | La dirección URL codificó la dirección URL de respuesta de la aplicación. Por ejemplo: http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | La dirección URL codificó el identificador de las API de administración de servicios de Azure: https://management.core.windows.net/ |
| ámbito | openid+perfil
| valor de seguridad | Fragmento de datos para enlazar la solicitud de autorización al id\_token devuelto para asegurarse de que se solicita la respuesta de la autorización y no se repite.
| domain\_hint | live.com <br />** Nota **: utilice el parámetro domain\_hint solo si el usuario administra su suscripción a Azure con una cuenta de Microsoft.
| state | Opcionalmente, especifique los datos de estado que desea que Azure AD devuelva con la respuesta.

Este es un ejemplo de solicitud de Open ID Connect:

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica al usuario y, si se requiere, pide al usuario que conceda permiso a la aplicación. Devuelve el código de autorización a la dirección URL de respuesta de la aplicación. Dependiendo del parámetro response\_mode solicitado Azure AD devuelve los datos en la cadena de consulta o como datos POST.

Este es un ejemplo de respuesta de Open ID Connect:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Validación de id\_token

Antes de que la aplicación inicie la sesión del usuario debe validar el id\_token. La validación de token es un tema implicado y se recomiendo usar una biblioteca estándar de controlador de JSON Web Token para la plataforma de desarrollo (consulte el [código de origen del controlador de JWT de .NET Azure AD](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs)). Dicho esto, la seguridad de la aplicación es responsabilidad suya, así que asegúrese de que la biblioteca que utiliza para controlar el id\_token correctamente valida los siguientes aspectos del token:

- **Tiempo del token**: compruebe las notificaciones nbf y exp para asegurarse de que el token no es demasiado actual ni demasiado obsoleto. Es habitual tener cierta demora (5 minutos) para acomodar los desfases temporales.
- **Emisor**: compruebe la notificación ISS para asegurarse de que el emisor del token es Azure Active Directory: https://sts.windows.net/{tenant_id_of_the_directory}
- **Audiencia**: compruebe la notificación aud para asegurarse de que se ha creado el token de la aplicación. El valor debe ser el Id. de cliente de la aplicación.
- **Valor de seguridad**: compruebe la notificación del valor de seguridad que se va a comprobar con los datos del valor de seguridad que envió en la solicitud de autorización, con el fin de asegurarse de que la respuesta la ha solicitado la aplicación y de que el token no se repite.
- **Firma**: la aplicación debe comprobar que Azure Active Directory ha formado el token. Las claves de firma de Azure AD se resumen con frecuencia, por lo que la aplicación debe sondear las claves actualizadas a diario o buscar errores en las claves actualizadas si no se puede realizar la validación de la firma. Para más información, [consulte Información importante acerca de la sustitución de la clave de firma en Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Una vez que el **id\_token** se ha validado, utilizar el valor de notificación oid como identificador inmutable y no reutilizable del usuario. Use la notificación **unique\_name** o la notificación de upn o correo electrónico como nombre para mostrar en lenguaje natural del usuario . También puede usar las notificaciones opcionales given\_name/family\_name con fines de presentación.

### Solicitud de token (flujo de concesión de códigos de OAuth2.0)

Una vez que la aplicación haya recibido el código de autorización de Azure AD, es el momento de obtener el token de acceso para Azure Resource Manager. Publique una solicitud de token de concesión de códigos de OAuth2.0 en el punto de conexión del token de Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Los parámetros de la cadena de consulta disponibles para esta solicitud se describen en el tema [Flujo de concesión de códigos de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx).

El ejemplo siguiente muestra una solicitud de token de concesión de códigos con la credencial de contraseña:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Cuando trabaje con credenciales de certificado, cree un JSON Web Token (JWT) y una firma (RSA SHA256) mediante la clave privada de la credencial de certificado de la aplicación. Los tipos de notificación del token se muestran en [Flujo de concesión de códigos de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx). Para referencia, consulte el [código de la biblioteca de autorización de Active Directory (.NET) ](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs) para firmar tokens JWT de aserción de cliente.

Consulte la [especificación de Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para más información acerca de la autenticación del cliente. Este es un [token JWT de aserción de cliente de ejemplo](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA).

El ejemplo siguiente muestra una solicitud de token de concesión de códigos con una credencial de certificado:

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Un ejemplo de respuesta de token de concesión de códigos:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### Control de la respuesta de token de concesión de códigos

Una respuesta correcta del token contendrá el token de acceso (usuario + aplicación) para Azure Resource Manager. La aplicación utilizará este token de acceso para acceder a Resource Manager en nombre del usuario. Los tokens de acceso emitidos por Azure AD tienen una duración de una hora. Es improbable que una aplicación web necesite renovar el token de acceso (usuario + aplicación; sin embargo, si lo necesita, puede usar el token de actualización que recibe la aplicación en la respuesta del token. Publique una solicitud de token de OAuth2.0 en el punto de conexión del token de Azure AD:

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Los parámetros que se usan con la solicitud de actualización se describen en [Flujo de concesión de códigos de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx).

En el ejemplo siguiente se muestra cómo utilizar el token de actualización:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Tenga en cuenta que aunque se pueden usar tokens de actualización para obtener nuevos tokens de acceso para Azure Resource Manager, no son adecuados para el acceso sin conexión por parte de la aplicación. La duración de los tokens de actualización está limitada y los tokens de actualización están enlazados al usuario. Si el usuario abandona la organización, la aplicación que usa el token de actualización perderá el acceso. Este enfoque no es adecuado para las aplicaciones que utilizan los equipos para administrar sus recursos de Azure.

## Enumeración de suscripciones disponibles

Su aplicación ahora tiene un token para acceder a Azure Resource Manager en nombre del usuario.

El siguiente paso de la experiencia es permitir al usuario conectar su suscripción de Azure a su aplicación, con el fin de que la aplicación pueda administrar dichas suscripciones, aun cuando el usuario no esté presente (acceso sin conexión a largo plazo). Muestre al usuario la lista de suscripciones de Azure en las que el usuario puede administrar el acceso y deje que el usuario asigne un rol RBAC directamente a la identidad de la aplicación.

![Autenticación ARM- aplicación de ejemplo Ux 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### Enumeración de suscripciones a las que el usuario tiene acceso

En primer lugar, llamaremos a la API de [suscripciones de la lista de Resource Manager](https://msdn.microsoft.com/library/azure/dn790531.aspx) para enumerar todas las suscripciones a las que el usuario tiene algún tipo de acceso. A continuación, identificaremos aquellas en las que el usuario puede administrar el acceso.

El método [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) de la aplicación de ejemplo de ASP.net MVC implementa esta llamada.

Una solicitud de ejemplo para enumerar suscripciones es:

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Una respuesta de ejemplo para enumerar suscripciones es:

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### Obtención de permisos de usuario en la suscripción

La acción de conexión y desconexión solo se debe mostrar en las suscripciones en las que el usuario puede administrar el acceso. En cada suscripción, llamará a la API de [permisos de lista de Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx) para determinar si el usuario tiene derechos de administración de acceso en la suscripción.

El método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) de la aplicación de ejemplo ASP.net MVC implementa esta llamada.

En el ejemplo siguiente se muestra cómo solicitar permisos de un usuario en una suscripción. 83cfe939-2402-4581-b761-4f59b0a041e4 es el identificador de la suscripción.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Un ejemplo de la respuesta para obtener los permisos del usuario en la suscripción es:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

La API de Permissions devuelve varios permisos. Cada permiso consta de acciones permitidas (actions) y acciones no permitidas (notactions). Si una acción está presente en la lista de acciones permitidas de cualquier permiso y no está presente en la lista notactions de dicho permiso, el usuario puede realizar dicha acción. **microsoft.authorization/roleassignments/write** es la acción que concede derechos de administración de acceso. La aplicación debe analizar el resultado de los permisos para buscar una coincidencia de regex en esta cadena de acción en las actions y notactions de cada permiso.

### Opcional: directorios de la lista en los que la cuenta de usuario está presente

Una cuenta de usuario puede estar presente en varios directorios de Azure Active Directory. Es posible que el usuario no especificara el nombre de directorio correcto inicialmente, en ese caso la suscripción deseada no se mostrará en la lista.

La API de [inquilinos de lista de Resource Manager](https://msdn.microsoft.com/library/azure/dn790536.aspx) enumera los identificadores de todos los directorios en los que la cuenta de usuario está presente. ¿Puede llamar a la API para determinar si la cuenta de usuario está en más de un directorio y, opcionalmente, mostrar al usuario un mensaje como el siguiente: "No encontró la suscripción que buscaba? Puede estar en el otro directorio de Azure Active Directory del que es miembro. Haga clic aquí para cambiar de directorio."

El método [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) de la aplicación de ejemplo de ASP.NET MVC implementa esta llamada.

Una solicitud de ejemplo para enumerar directorios:

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

Una respuesta de ejemplo para enumerar directorios:

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## Conexión de una suscripción a una aplicación

Ya tiene una lista de las suscripciones de Azure que el usuario puede conectar a la aplicación. El siguiente paso es proporcionar al usuario un comando para crear la conexión. Cuando el usuario selecciona **connect**, la aplicación:

1. Asigna el rol RBAC apropiado a la identidad de la aplicación en la suscripción.
2. Valida la asignación de acceso, ya que consulta el permiso de la aplicación en la suscripción o accede a Resource Manager con el token de solo aplicación.
1. Registra la conexión en la estructura de datos de "suscripciones conectadas" de las aplicaciones (sin perder el identificador de la suscripción).

Examinemos más detenidamente el primer paso: para asignar el rol RBAC adecuado a la identidad de la aplicación, es preciso determinar:

- El identificador de objeto de la identidad de la aplicación en el directorio de Azure Active Directory del usuario
- El identificador del rol RBAC que requiere la aplicación en la suscripción

Examinemos la primera parte: la primera vez que la aplicación autentica a un usuario desde un directorio de Azure AD, se crea un objeto de entidad de servicio para la aplicación en dicho directorio de Azure AD. Azure permite asignar roles RBAC a entidades de servicio, con el fin de conceder acceso directo a las aplicaciones correspondientes en los recursos de Azure. Esto es exactamente lo que queremos hacer, así que comenzamos consultando la API de Azure AD Graph para determinar el identificador de la entidad de servicio de la aplicación en el directorio de Azure AD del usuario que inició sesión.

Solo tiene un token de acceso para Azure Resource Manager (se necesita un token de acceso nuevo para llamar a la API de Azure AD Graph). Todas las aplicaciones de Azure AD tienen permiso para consultar su propio objeto de entidad de servicio, por lo que no se necesita un token de acceso usuario + aplicación para ello, bastará con un token de acceso de solo aplicación.

<a id="app-azure-ad-graph">
### Obtención de token de acceso de solo aplicación para la API de Azure AD Graph

Para autenticar una aplicación y obtener un token para la API de Azure AD Graph, emita una solicitud de token de flujo de OAuth2.0 de concesión de credenciales de cliente al punto de conexión del token de Azure AD (**http://login.microsoftonline.com/ {directory\_domain\_name}/ OAuth2/Token**).

Las líneas 73-77 del método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) de la aplicación de ejemplo ASP.net MVC obtiene un token de acceso de solo aplicación para la API de Graph mediante la Biblioteca de autenticación de Active Directory para .NET.

Los datos de la solicitud de token de concesión de credenciales de cliente:

| Elemento | Valor
|----|----
| grant\_type | **client\_credentials**
| client\_id | Id. de cliente de la aplicación
| resource | Identificador del recurso para el que se solicita el token de acceso de código URL. En este caso, el identificador de la API de Azure AD Graph: **https://graph.windows.net/** | client\_secret o client\_assertion\_type + client\_assertion | Si la aplicación utiliza la credencial de contraseña, utilice client\_secret. Si la aplicación usa credencial de certificado, utilice client\_assertion.

Una solicitud de ejemplo de token de concesión de credenciales de cliente:

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Una respuesta de ejemplo de token de concesión de credenciales de cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### Obtención de Id. de objeto de entidad de servicio de aplicación en Azure AD de usuario

Ahora, use el token de acceso de solo aplicación para consultar la API de [entidades de servicio de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ServicePrincipalEntity) para determinar el identificador de objeto de la entidad de servicio de la aplicación en el directorio.

El método [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) de la aplicación de ejemplo ASP.net MVC implementa esta llamada.

En el ejemplo siguiente se muestra cómo solicitar una entidad de servicio de aplicación. a0448380-c346-4f9f-b897-c18733de9394 es el identificador de cliente de la aplicación.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

En el ejemplo siguiente se muestra una respuesta a la solicitud de una entidad de servicio de aplicación

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Obtención del identificador del rol RBAC de Azure

Debe asignar el rol RBAC apropiado a la entidad de servicio de su aplicación en la suscripción seleccionada. Para ello debe determinar el identificador del rol RBAC de Azure.

El rol RBAC correcta para su aplicación:

- Si la aplicación solo supervisa la suscripción, sin realizar ningún cambio, requiere solo permisos de lector en la suscripción. Asigne el rol **Lector**.
- Si la aplicación administra Azure la suscripción, la creación, modificación o eliminación de entidades, requerirá uno de los permisos de colaborador.
  - Para administrar un tipo concreto de recurso, asigne los roles de colaborador específicos del recurso (colaborador de máquina virtual, colaborador de red virtual, colaborador de cuenta de almacenamiento, etc.).
  - Para administrar cualquier tipo de recurso, asigne el rol **Colaborador**.

La asignación de roles para la aplicación será visible para los usuarios, así que seleccione el privilegio mínimo requerido.

Llame a la [API de definición de roles de Resource Manager](https://msdn.microsoft.com/library/azure/dn906879.aspx) para enumerar todos los roles RBAC de Azure y luego itere en el resultado para buscar la definición de rol deseada por nombre.

El método [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) de la aplicación de ejemplo ASP.net MVC implementa esta llamada.

El siguiente ejemplo de solicitud muestra cómo obtener el identificador del rol RBAC de Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb es el identificador de la suscripción.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La respuesta está en el formato siguiente:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Tenga en cuenta que no es preciso llamar a esta API de forma continua. Una vez que haya determinado el GUID conocido de la definición de rol, puede construir el identificador de la definición de rol como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Estos son los GUID conocidos de los roles integrados que se usan habitualmente:

| Rol | Guid |
| ----- | ------ |
| Lector | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Colaborador | b24988ac-6180-42a0-ab88-20f7382dd24c
| Colaborador de la máquina virtual | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Colaborador de la red virtual | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Colaborador de la cuenta de almacenamiento | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Colaborador de sitio web | de139f84-1756-47ae-9be6-808fbbe84772
| Colaborador de plan web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Colaborador de SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Colaborador de la base de datos de SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### Asignación de un rol RBAC a una aplicación

Ya tiene todo lo que necesita para asignar el rol RBAC apropiado a la entidad de servicio de su aplicación en la suscripción seleccionada mediante la API de [asignación de roles de creación de Resource Manager](https://msdn.microsoft.com/library/azure/dn906887.aspx).

El método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) de la aplicación de ejemplo ASP.net MVC implementa esta llamada.

Un ejemplo de solicitud para asignar el rol RBAC a una aplicación:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

En la solicitud, se utilizan los siguientes valores:

| Guid | Descripción |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | el identificador de la suscripción
| c3097b31-7309-4c59-b4e3-770f8406bad2 | el identificador de objeto de la entidad de servicio de la aplicación
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | el guid conocido del rol RBAC de lector
| 4f87261d-2816-465d-8311-70a27558df4c | un guid nuevo creado para la nueva asignación de rol

La respuesta está en el formato siguiente:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Obtención de token de acceso de solo aplicación para Azure Resource Manager

El siguiente paso es validar que la aplicación tiene el acceso deseado en la suscripción. Para ello, debe realizar una tarea de prueba en la suscripción con un token de solo aplicación para Azure Resource Manager. La tarea de prueba debe validar que la aplicación realmente tenga el acceso deseado en la suscripción, para realizar la supervisión y administración sin conexión.

Para obtener un token de acceso de solo aplicación para Azure Resource Manager, siga las instrucciones de la sección [Obtención de token de acceso de solo aplicación para la API de Azure AD Graph](#app-azure-ad-graph), con un valor diferente para el parámetro del recurso:

    https://management.core.windows.net/

Las líneas 210-214 del método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) de la aplicación de ejemplo ASP.net MVC obtiene un token de acceso de solo aplicación para Azure Resource Manager mediante la Biblioteca de autenticación de Active Directory para .NET.

#### Obtención de permisos la aplicación en la suscripción

Para comprobar que la aplicación tiene el acceso deseado en una suscripción de Azure, también puede llamar a la API de [permisos de Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx), de forma similar a cómo se determina si el usuario tiene derechos de administración de acceso en la suscripción. Sin embargo, esta vez llame a la API de permisos con el token de acceso de solo aplicación que recibió en el paso anterior.

El método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) de la aplicación de ejemplo ASP.NET MVC implementa esta llamada.

## Administración de suscripciones conectadas

Cuando el rol RBAC apropiado se asigna a la entidad de servicio de la aplicación en la suscripción, la aplicación puede seguir supervisándola o administrándola mediante tokens de acceso de solo aplicación para Azure Resource Manager.

Si el propietario de la suscripción quita la asignación de rol de la aplicación mediante el Portal de administración de Azure o las herramientas de la línea de comandos, la aplicación no podrá acceder a dicha suscripción. En ese caso, debe notificar al usuario que se ha roto la conexión con la suscripción desde fuera de la aplicación y darle la opción de "reparar" la conexión. La "reparación" simplemente volvería a crear la asignación de roles que se eliminó sin conexión.


## Desconexión de las suscripciones

Igual que habilitó al usuario para que conectara sus suscripciones a la aplicación, también debe permitir al usuario desconectar suscripciones. Desde el punto de vista de la administración de acceso, desconectar significa quitar la asignación de roles que la entidad de servicio de la aplicación tiene en la suscripción. Opcionalmente, también se pueden quitar todos los estados de la aplicación para la suscripción. Solo los usuarios con permiso de administración de acceso en la suscripción podrán desconectar la suscripción.

El [método RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303) de la aplicación de ejemplo ASP.net MVC implementa esta llamada.

Eso es todo: los usuarios ya pueden conectar y administrar fácilmente sus suscripciones de Azure con su aplicación.

<!---HONumber=AcomDC_0420_2016-->