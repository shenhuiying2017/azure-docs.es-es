---
title: "Introducción a Xamarin de Azure AD | Microsoft Docs"
description: "Cree aplicaciones de Xamarin que se integren con Azure AD para el inicio de sesión y la llamada a las API protegidas por Azure AD mediante OAuth."
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 94a7d35115420d455fe94e1173abf76622172f6f
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-xamarin-getting-started"></a>Introducción a Xamarin de Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Con Xamarin, puede escribir aplicaciones móviles en C# que se pueden ejecutar en iOS, Android y Windows (dispositivos móviles y PC). Si va a compilar una aplicación con Xamarin, Azure Active Directory (Azure AD) simplifica la autenticación de los usuarios con sus cuentas de Azure AD. La aplicación también puede consumir de forma segura cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para aplicaciones de Xamarin que necesitan acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL). El único objetivo de ADAL es simplificar para las aplicaciones el acceso a los tokens. Para demostrar lo sencillo que es, en este artículo se muestra cómo compilar una aplicación de DirectorySearcher que:

* Se ejecute en iOS, Android, Escritorio de Windows, Windows Phone y Tienda Windows.
* Use una biblioteca de clases portable (PCL) única para autenticar a los usuarios y obtener tokens para Graph API de Azure AD.
* Busque un directorio para los usuarios con un UPN determinado.

## <a name="before-you-get-started"></a>Antes de comenzar
* Descargue el [esqueleto del proyecto](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o el [ejemplo completado](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada descarga es una solución de Visual Studio 2013.
* También necesita a un inquilino de Azure AD en el que crear usuarios y registrar la aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

Cuando esté listo, siga los procedimientos descritos en las cuatro secciones siguientes.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Paso 1: Configuración del entorno de desarrollo de Xamarin
Como este tutorial incluye proyectos de iOS, Android y Windows, necesitará tanto Visual Studio como Xamarin. Para crear el entorno es necesario, realice el proceso que se describe en [Establecimiento e instalación de Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) en MSDN. Estas instrucciones incluyen material que puede examinar para aprender más sobre Xamarin mientras espera a que finalicen los instaladores.

Después de que finalice la instalación, abra la solución en Visual Studio. Encontrará seis proyectos: cinco proyectos específicos de la plataforma y una PLC, DirectorySearcher.cs, que se compartirá entre todas las plataformas.

## <a name="step-2-register-the-directorysearcher-app"></a>Paso 2: Registro de la aplicación DirectorySearcher
Para permitir que la aplicación obtenga tokens, primero debe registrarla en su inquilino de Azure AD y concederle permiso de acceso a Graph API de Azure AD: Este es el procedimiento:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta. A continuación, en la lista **Directorio**, seleccione el inquilino de Active Directory donde quiere registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y luego seleccione **Agregar**.
5. Para crear una nueva **aplicación cliente nativa**, siga las indicaciones.
  * **Nombre**: describe la aplicación a los usuarios.
  * El **URI de redirección** es una combinación de esquema y cadena que usará Azure AD para devolver respuestas de tokens. Escriba un valor (por ejemplo, http://DirectorySearcher).
6. Una vez completado el registro, Azure AD asigna a la aplicación un identificador de aplicación único. Copie el valor de la pestaña **Aplicación**, ya que lo necesitará más adelante.
7. En la página **Configuración**, seleccione **Permisos necesarios** y **Agregar**.
8. Seleccione **Microsoft Graph** como la API. En **Permisos delegados**, agregue el permiso **Leer datos de directorio**.  
Esta acción permite que la aplicación consulte Graph API para los usuarios.

## <a name="step-3-install-and-configure-adal"></a>Paso 3: Instalación y configuración de ADAL
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para permitir que AAL se comunique con Azure AD, proporciónele alguna información sobre el registro de aplicación.

1. Agregue ADAL al proyecto DirectorySearcher mediante la Consola del Administrador de paquetes.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Observe que se agregan dos referencias de biblioteca a cada proyecto: la parte PCL de ADAL y una parte específica de la plataforma.
2. En el proyecto DirectorySearcherLib, abra DirectorySearcher.cs.
3. Reemplace los valores de miembro de clase por los valores que escribió en Azure Portal. El código hace referencia a estos valores cada vez que usa AAL.

  * El *inquilino* es el dominio de su inquilino de Azure AD (por ejemplo, contoso.onmicrosoft.com).
  * *clientId* es e id. de cliente de la aplicación, que se copia del portal.
  * *returnUri* es el URI que de redirección que especificó en el portal (por ejemplo, http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Paso 4: Uso de ADAL para obtener tokens de Azure AD
Casi toda la lógica de autenticación de la aplicación se basa en `DirectorySearcher.SearchByAlias(...)`. En los proyectos específicos de la plataforma, lo único que hace falta es pasar un parámetro contextual a la PCL `DirectorySearcher`.

1. Abra DirectorySearcher.cs y agregue un nuevo parámetro al método `SearchByAlias(...)`. `IPlatformParameters` es el parámetro contextual que encapsula los objetos específicos de la plataforma que ADAL necesita para realizar la autenticación.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicialice `AuthenticationContext`, que es la clase principal de ADAL.  
Esta acción pasa a ADAL las coordenadas que necesita para comunicarse con Azure AD.
3. Llame a `AcquireTokenAsync(...)`, que acepta el objeto `IPlatformParameters` e inicia el flujo de autenticación necesario para devolver un token a la aplicación.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` primero intenta devolver un token para el recurso solicitado (la API Graph API en este caso) sin solicitar a los usuarios que escriban sus credenciales (mediante el almacenamiento en caché o la actualización de tokens antiguos). Solo si es necesario, muestra a los usuarios la página de inicio de sesión de Azure AD antes de adquirir el token solicitado.
4. Adjunte el token de acceso a la solicitud de Graph API en la cabecera de **autorización**:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Eso es todo para la PCL `DirectorySearcher` y el código relacionado con la identidad de la aplicación. Ya solo queda llamar al método `SearchByAlias(...)` en las vistas de cada plataforma y, si es necesario, agregar código para administrar correctamente el ciclo de vida de la interfaz de usuario.

### <a name="android"></a>Android
1. En MainActivity.cs, agregue una llamada a `SearchByAlias(...)` en el controlador de clic de botón:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Reemplace el método de ciclo de vida `OnActivityResult` para reenviar cualquier redirección de autenticación de vuelta al método de autenticación. ADAL proporciona un método auxiliar para esto en Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Escritorio de Windows
En MainWindow.xaml.cs, realice una llamada a `SearchByAlias(...)` pasando `WindowInteropHelper` al objeto `PlatformParameters` del escritorio:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
En DirSearchClient_iOSViewController.cs, el objeto `PlatformParameters` de iOS toma una referencia al controlador de vista:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
En Windows Universal, abra MainPage.xaml.cs y luego implemente el método `Search`. Este método usa un método auxiliar en un proyecto compartido para actualizar la interfaz de usuario según sea necesario.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Pasos siguientes
Ahora dispone de una aplicación de Xamarin que funciona que puede autenticar a los usuarios y llamar a API web de forma segura mediante OAuth 2.0 en cinco plataformas diferentes.

Si aún no ha rellenado al inquilino con usuarios, ahora es el momento de hacerlo.

1. Ejecute la aplicación DirectorySearcher y luego inicie sesión con uno de los usuarios.
2. Busque otros usuarios según su UPN.

ADAL facilita la incorporación de características de identidad comunes en la aplicación. Hace el trabajo sucio por usted: administración en caché, compatibilidad con el protocolo OAuth, presentación al usuario de una interfaz de usuario de inicio de sesión y actualización de tokens expirados. Solo debe conocer una única llamada de API, `authContext.AcquireToken*(…)`.

Como referencia, descargue el [ejemplo completado](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (sin sus valores de configuración).

Ahora puede trasladarse a escenarios de identidad adicionales. Por ejemplo, pruebe [Protección de una API web de .NET con Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
