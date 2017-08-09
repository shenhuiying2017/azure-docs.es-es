
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Llamar a la API de Microsoft Graph desde una aplicación de página única de JavaScript (SPA)

En esta guía se muestra cómo una aplicación de página única de JavaScript (SPA) puede iniciar sesión en cuentas personales y profesionales, obtener un token de acceso y llamar a la API de Microsoft Graph u otras API que requieran tokens de acceso desde un punto de conexión de Azure Active Directory v2.

### <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de la aplicación de ejemplo generada por esta guía](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Más información

La aplicación de ejemplo que se crea con esta guía permite que una aplicación de página única de JavaScript consulte a la API de Microsoft Graph o a una API web que acepte tokens de un punto de conexión de Azure Active Directory v2. En este escenario, después de que un usuario inicia sesión, se agrega un token a las solicitudes HTTP a través del encabezado de autorización. La adquisición y la renovación de los tokens se realiza por medio de la biblioteca de autenticación de Microsoft (MSAL).
<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Esta guía utiliza la siguiente biblioteca:

|Biblioteca|Descripción|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticación de Microsoft para la vista preliminar de JavaScript|
<!--end-collapse-->
