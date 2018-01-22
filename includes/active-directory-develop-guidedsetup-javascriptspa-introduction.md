# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Llamar a Microsoft Graph API desde una aplicación de página única de JavaScript (SPA)

En esta guía se muestra cómo una aplicación de una sola página (SPA) de JavaScript puede iniciar sesión en cuentas personales, profesionales y educativas, obtener un token de acceso y llamar a Microsoft Graph API u otras API que requieran tokens de acceso desde el punto de conexión de Azure Active Directory v2.

### <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de la aplicación de ejemplo generada por esta guía](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Más información

La aplicación de ejemplo que se crea con esta guía permite que una aplicación de página única de JavaScript consulte a Microsoft Graph API o a una API web que acepte tokens de un punto de conexión de Azure Active Directory v2. En este escenario, después de que un usuario inicia sesión, se solicita un token de acceso a las solicitudes HTTP a través del encabezado de autorización. La adquisición y la renovación de los tokens se realizan por medio de la biblioteca de autenticación de Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Esta guía utiliza la siguiente biblioteca:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticación de Microsoft para la vista preliminar de JavaScript|

> [!NOTE]
> *msal.js* apunta al *punto de conexión de Azure Active Directory v2*, lo que permite que las cuentas personales, profesionales y educativas inicien sesión y adquieran tokens. El *punto de conexión de Azure Active Directory v2* tiene [algunas limitaciones](..\articles\active-directory\develop\active-directory-v2-limitations.md). Si solo le interesan las cuentas educativas y profesionales, use *adal.js* en el *punto de conexión V1*. Para comprender las diferencias entre los puntos de conexión v1 y v2, lea la [comparación entre v1 y v2](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
