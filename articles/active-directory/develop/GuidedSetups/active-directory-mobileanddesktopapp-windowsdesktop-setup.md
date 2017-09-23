---
title: "Introducción al escritorio de Windows en Azure AD v2: configuración | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones .NET de escritorio de Windows (XAML) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.contentlocale: es-es

---

## <a name="set-up-your-project"></a>Configurar su proyecto

En esta sección se proporcionan instrucciones paso a paso sobre cómo crear un nuevo proyecto a fin de mostrar cómo integrar una aplicación .NET de escritorio de Windows (XAML) con *Iniciar sesión en Microsoft* de manera que pueda consultar a las API web que requieran un token.

En la aplicación creada por esta guía se presenta un botón para Graph, los resultados se muestran en pantalla, y aparece también un botón de cierre de sesión.

> ¿Prefiere descargar este proyecto de Visual Studio de ejemplo en su lugar? [Descargue un proyecto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) y vaya al [paso de configuración](#create-an-application-express) para configurar el código de ejemplo antes de ejecutarlo.


### <a name="create-your-application"></a>Creación de la aplicación
1. En Visual Studio: `File` > `New` > `Project`<br/>
2. En *Plantillas*, seleccione `Visual C#`.
3. Seleccione `WPF App` (o *Aplicación WPF* según la versión de su Visual Studio).

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Adición de la biblioteca de autenticación de Microsoft (MASL) a su proyecto
1. En Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Copie y pegue lo siguiente en la ventana de la Consola del Administrador de paquetes:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> El paquete anterior instala la biblioteca de autenticación de Microsoft (MSAL). MSAL controla la adquisición, el almacenamiento en caché y la actualización de los tokens de usuario que se utilizan para tener acceso a las API protegidas por Azure Active Directory v2.

## <a name="add-the-code-to-initialize-msal"></a>Adición del código para inicializar MSAL
Este paso le ayudará a crear una clase para controlar la interacción con la biblioteca de MSAL, como el control de tokens.

1. Abra el archivo `App.xaml.cs` y agregue la referencia de la biblioteca MSAL a la clase:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Actualice la clase App a lo siguiente:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>Creación de la IU de la aplicación
La sección siguiente muestra cómo puede una aplicación consultar a un servidor de back-end protegido como Microsoft Graph. Debe crearse un archivo MainWindow.xaml automáticamente como parte de la plantilla de proyecto. Abra este archivo y, a continuación, siga las instrucciones siguientes:

Reemplace el valor `<Grid>` de la aplicación por lo siguiente:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

