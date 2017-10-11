---
title: "Incorporación de autenticación a una aplicación de la Plataforma universal de Windows (UWP) | Microsoft Docs"
description: "Obtenga información acerca de cómo usar Aplicaciones móviles del Servicio de aplicaciones de Azure para autenticar a los usuarios de la aplicación de la Plataforma universal de Windows (UWP) en una variedad de proveedores de identidades, incluidos AAD,Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: windows
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 47da343d4ec956ec2e669757f56e853675f887a3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="add-authentication-to-your-windows-app"></a>Incorporación de la autenticación a la aplicación de Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo agregar la autenticación basada en la nube a su aplicación móvil. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido de la Plataforma universal de Windows (UWP) para Aplicaciones móviles mediante un proveedor de identidades compatible con el Servicio de aplicaciones de Azure. Una vez que el back-end de la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Introducción a las aplicaciones móviles](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adición de la aplicación a las direcciones URL de redirección externa permitidas

La autenticación segura requiere que se defina un nuevo esquema de dirección URL para la aplicación. Esto permite que el sistema de autenticación se redirija a la aplicación una vez completado el proceso de autenticación. En este tutorial, se usará el esquema de dirección URL _appname_. Sin embargo, puede utilizar cualquier otro esquema de dirección URL que elija. Debe ser único para la aplicación móvil. Para habilitar la redirección en el lado de servidor:

1. En [Azure Portal], seleccione App Service.

2. Haga clic en la opción de menú **Autenticación/autorización**.

3. En **URL de redirección externas permitidas**, introduzca `url_scheme_of_your_app://easyauth.callback`.  El valor de **esquema_de_dirección_URL_de_la_aplicación** de esta cadena es el esquema de dirección URL para la aplicación móvil.  Debe seguir la especificación normal de las direcciones URL para un protocolo (usar únicamente letras y números, y comenzar por una letra).  Debe tomar nota de la cadena que elija ya que necesitará ajustar el código de la aplicación móvil con el esquema de direcciones URL en varios sitios.

4. Haga clic en **Aceptar**.

5. Haga clic en **Save**.

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Ahora, puede comprobar que se deshabilitó el acceso anónimo a su back-end. Con el proyecto de aplicación para UWP configurado como proyecto de inicio, implemente y ejecute la aplicación; compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto se produce porque la aplicación intenta obtener acceso a su Código de aplicación móvil como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del Servicio de aplicaciones.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación
1. En el archivo de proyecto de aplicación para UWP MainPage.xaml.cs, agregue el siguiente fragmento de código:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Este código autentica al usuario con un inicio de sesión de Facebook. Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de **MobileServiceAuthenticationProvider** anterior por el valor de su proveedor.
2. Reemplace el método **OnNavigatedTo()** en MainPage.xaml.cs. A continuación, agregará un botón **Iniciar sesión** a la aplicación que desencadena la autenticación.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Agregue el siguiente fragmento de código a MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Abra el archivo de proyecto MainPage.xaml, busque el elemento que define el botón **Guardar** y reemplácelo por el código siguiente:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Agregue el siguiente fragmento de código a App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Abra el archivo Package.appxmanifest, vaya a **Declaraciones**, en la lista desplegable **Declaraciones disponibles** seleccione **Protocolo** y haga clic en el botón **Agregar**. Configure ahora las **Propiedades** de la declaración **Protocolo**. En **Nombre para mostrar**, agregue el nombre que quiere mostrar a los usuarios de la aplicación. En **Nombre**, agregue el {esquema_de_dirección_URL_de_la_aplicación}.
7. Presione la tecla F5 para ejecutar la aplicación, haga clic en el botón **Iniciar sesión** e inicie sesión en la aplicación con el proveedor de identidad que haya elegido. Una vez iniciada la sesión correctamente, la aplicación se ejecutará sin errores y podrá consultar al back-end y realizar actualizaciones de datos.

## <a name="tokens"></a>Almacenamiento del token de autorización en el cliente
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio de la aplicación cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por su servicio de aplicación e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

> [!NOTE]
> Puede almacenar en caché el token emitido por los servicios de aplicaciones con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

* [Incorporación de notificaciones push a su aplicación](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  : aprenda a agregar a la aplicación compatibilidad con notificaciones push y a configurar su back-end de aplicación móvil para usar Azure Notification Hubs para enviar notificaciones push.
* [Habilitación de la sincronización sin conexión para su aplicación](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  : aprenda a agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos) aun cuando no haya conexión de red.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
