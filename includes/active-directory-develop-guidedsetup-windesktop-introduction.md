# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Llamada a la API Graph de Microsoft desde una aplicación de escritorio de Windows

Esta guía muestra cómo una aplicación nativa .NET de escritorio de Windows (XAML) puede obtener un token de acceso y llamar a Microsoft Graph API u otras API que requieren tokens de acceso desde un punto de conexión de Azure Active Directory v2.

Cuando haya completado la guía, la aplicación podrá llamar a una API protegida que usa cuentas personales (lo que incluye outlook.com, live.com y otras). La aplicación también usa cuentas profesionales y educativas de cualquier empresa y organización que utilice Azure Active Directory.  

> [!NOTE] 
> Esta guía requiere Visual Studio 2015 Update 3 o Visual Studio 2017.  ¿No tiene ninguna de estas versiones? [Descargue Visual Studio 2017 de manera gratuita](https://www.visualstudio.com/downloads/).

## <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de esta guía](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

La aplicación de ejemplo que se crea con esta guía permite que una aplicación de escritorio de Windows consulte Microsoft Graph API o que una API web acepte tokens de un punto de conexión de Azure Active Directory v2. En este escenario, agregará un token a las solicitudes HTTP mediante el encabezado de autorización. La Biblioteca de autenticación de Microsoft (MSAL) administra la adquisición y renovación de los tokens.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Tratamiento de la adquisición de tokens para tener acceso a API web protegidas

Una vez que el usuario se autentica, la aplicación de ejemplo recibe un token que se puede usar para consultar una Microsoft Graph API o a una API web protegida mediante Azure Active Directory v2.

Las API, como Microsoft Graph, requieren un token para permitir el acceso a recursos específicos. Por ejemplo, un token es necesario para leer un perfil del usuario, acceder al calendario de un usuario o enviar correo electrónico. La aplicación puede solicitar un token de acceso mediante MSAL para acceder a estos recursos mediante la especificación de ámbitos de API. Este token de acceso se agrega luego al encabezado de autorización de HTTP para todas las llamadas efectuadas al recurso protegido. 

MSAL administra el almacenamiento en caché y la actualización de los tokens de acceso automáticamente, así que no tiene que preocuparse por ello.

## <a name="nuget-packages"></a>Paquetes NuGet

Esta guía utiliza los siguientes paquetes NuGet:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de autenticación de Microsoft (MSAL)|

