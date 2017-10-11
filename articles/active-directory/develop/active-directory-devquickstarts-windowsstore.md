---
title: "Introducción a Tienda Windows de Azure AD | Microsoft Docs"
description: "Cree aplicaciones de la Tienda Windows que se integren con Azure AD para el inicio de sesión y la llamada a las API protegidas por Azure AD mediante OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b5189dc06d7f8b0ed4426944948b904feba847e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>Integración de Azure AD con aplicación de la Tienda Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Los proyectos de Windows Store 8.1 y de la versión anterior no son compatibles con Visual Studio 2017.  Para más información, consulte [Compatibilidad y destinatarios de la plataforma Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Si va a desarrollar aplicaciones para la Tienda Windows, Azure Active Directory (Azure AD) simplifica la autenticación de los usuarios con sus cuentas de Active Directory. Mediante la integración con Azure AD, una aplicación puede consumir de forma segura cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para aplicaciones de escritorio de la Tienda Windows que necesitan acceder a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory o ADAL. El único propósito de ADAL es facilitar que la aplicación obtenga acceso a los tokens. Para demostrar lo sencillo que es, en este artículo se muestra cómo compilar una aplicación de la Tienda Windows de DirectorySearcher que:

* Obtiene tokens de acceso para llamar a la API Graph de Azure AD mediante el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Busca un directorio para usuarios con un nombre principal de usuario (UPN).
* Cerrar la sesión de los usuarios

## <a name="before-you-get-started"></a>Antes de comenzar
* Descargue el [esqueleto del proyecto](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) o el [ejemplo completado](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Cada descarga es una solución de Visual Studio 2015.
* También necesita a un inquilino de Azure AD en el que crear usuarios y registrar la aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

Cuando esté listo, siga los procedimientos que se describen en las tres secciones siguientes.

## <a name="step-1-register-the-directorysearcher-app"></a>Paso 1: Registro de la aplicación DirectorySearcher
Para permitir que la aplicación obtenga tokens, primero debe registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API de Azure AD Graph: Este es el procedimiento:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta. A continuación, en la lista **Directorio**, seleccione el inquilino de Active Directory donde quiere registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y seleccione **Agregar**.
5. Siga las indicaciones y cree una **aplicación de cliente nativa**.
  * **Nombre**: describe la aplicación a los usuarios.
  * El **URI de redirección** es una combinación de esquema y cadena que usará Azure AD para devolver respuestas de tokens. Por ahora, escriba un valor de marcador de posición (por ejemplo, **http://DirectorySearcher**). Este valor lo remplazará más adelante.
6. Después de haber completado el registro, Azure AD asigna la aplicación a un id. de aplicación único. Copie el valor de la pestaña **Aplicación**, ya que lo necesitará más adelante.
7. En la página **Configuración**, seleccione **Permisos necesarios** y **Agregar**.
8. Para la aplicación **Azure Active Directory**, seleccione **Microsoft Graph** como la API.
9. En **Permisos delegados**, agregue el permiso **Acceder al directorio como usuario con sesión iniciada**. Así se permite que la aplicación realice consultas en la API Graph sobre usuarios.

## <a name="step-2-install-and-configure-adal"></a>Paso 2: Instalación y configuración de ADAL
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para permitir que AAL se comunique con Azure AD, proporciónele alguna información sobre el registro de aplicación.

1. Agregue ADAL al proyecto DirectorySearcher mediante la Consola del Administrador de paquetes.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. En el proyecto de DirectorySearcher, abra MainPage.xaml.cs.
3. Reemplace los valores de la región **Valores configurados** por los valores que escribió en Azure Portal. El código hace referencia a estos valores cada vez que usa AAL.
  * El *inquilino* es el dominio de su inquilino de Azure AD (por ejemplo, contoso.onmicrosoft.com).
  * *clientId* es e id. de cliente de la aplicación, que se copia del portal.
4. Ahora debe detectar el URI de devolución de llamada para la aplicación de la Tienda Windows. Establezca un punto de interrupción en esta línea en el método `MainPage` :
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Compile la solución y asegúrese de que se haya restaurado las referencias de paquete. Si faltan paquetes, abra el Administrador de paquetes de Nuget y restáurelos.
6. Ejecute la aplicación y copie el valor de `redirectUri` cuando se alcance el punto de interrupción. El valor debe tener un aspecto similar al siguiente:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. De nuevo en la pestaña **Configuración** de la aplicación en Azure Portal, agregue un valor de **RedirectUri** como el anterior.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Paso 3: Uso de ADAL para obtener tokens de Azure AD
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a `authContext.AcquireToken(…)` y ADAL se encarga del resto.  

1. Inicialice la clase `AuthenticationContext`, que es la clase principal de AAL. Esta acción pasa a ADAL las coordenadas necesarias para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Busque el método `Search(...)`, que se invoca cuando los usuarios hacen clic en el botón **Buscar** en la interfaz de usuario de la aplicación. Este método realiza una solicitud GET a la API de Azure AD Graph para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado. Para consultar la API Graph, incluya un token de acceso en el encabezado de **autorización** de la solicitud. Aquí es donde entra en juego AAL.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Cuando la aplicación llama a `AcquireTokenAsync(...)` para solicitar un token, ADAL intenta devolver uno sin pedir al usuario credenciales. Si ADAL determina que el usuario necesita iniciar sesión para obtener un token, muestra un cuadro de diálogo de inicio de sesión, recopila las credenciales del usuario y devuelve un token después de que la autenticación se realiza correctamente. Si por algún motivo ADAL no puede devolver un token, el estado *AuthenticationResult* es un error.
3. Ahora es el momento de usar el token de acceso que acaba de adquirir. También en el método `Search(...)`, adjunte el token a la solicitud GET de API Graph en el encabezado de **autorización**:

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Puede usar el objeto `AuthenticationResult` para mostrar información sobre el usuario de la aplicación, como su id.:

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. También puede usar ADAL para cerrar la sesión de los usuarios en la aplicación. Cuando el usuario haga clic en el botón **Cerrar sesión**, asegúrese de que la siguiente llamada a `AcquireTokenAsync(...)` muestre una vista de inicio de sesión. Con ADAL, esta acción es tan fácil como borrar la caché de tokens:

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>Pasos siguientes
Ahora tiene una aplicación de la Tienda Windows en funcionamiento que puede autenticar a los usuarios, realizar llamadas seguras a las API web que usan OAuth 2.0 y obtener información básica sobre el usuario.

Si aún no ha rellenado al inquilino con usuarios, ahora es el momento de hacerlo.
1. Ejecute la aplicación DirectorySearcher y luego inicie sesión con uno de los usuarios.
2. Busque otros usuarios según su UPN.
3. Cierre la aplicación y vuelva a ejecutarla. Observe cómo la sesión del usuario permanece intacta.
4. Cierre la sesión; para ello, haga clic con el botón derecho para mostrar la barra inferior y vuelva a iniciar sesión con otro usuario.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación. Hace el trabajo sucio por usted: administración en caché, compatibilidad con el protocolo OAuth, presentación al usuario de una interfaz de usuario de inicio de sesión y actualización de tokens expirados. Solo debe conocer una única llamada de API, `authContext.AcquireToken*(…)`.

Como referencia, descargue el [ejemplo completado](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (sin sus valores de configuración).

Ahora puede trasladarse a escenarios de identidad adicionales. Por ejemplo, pruebe [Protección de una API web de .NET con Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
