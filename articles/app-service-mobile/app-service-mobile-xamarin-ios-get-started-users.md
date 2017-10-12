---
title: "Introducción a la autenticación de Aplicaciones móviles en Xamarin iOS"
description: "Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de su aplicación Xamarin iOS a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: glenga
ms.openlocfilehash: 454b2df5a9bf8cfba93befea54370957ab044d95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Adición de la autenticación a la aplicación Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido Xamarin.iOS mediante un proveedor de identidades compatible con Servicio de aplicaciones. Una vez que la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario y podrá tener acceso a datos de tabla restringida.

Primero debe completar el tutorial [Creación de una aplicación Xamarin.iOS]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Adición de la aplicación a las direcciones URL de redirección externa permitidas

La autenticación segura requiere que se defina un nuevo esquema de dirección URL para la aplicación. Esto permite que el sistema de autenticación se redirija a la aplicación una vez completado el proceso de autenticación. En este tutorial, se usará el esquema de dirección URL _appname_. Sin embargo, puede utilizar cualquier otro esquema de dirección URL que elija. Debe ser único para la aplicación móvil. Para habilitar la redirección en el lado de servidor:

1. En [Azure Portal], seleccione App Service.

2. Haga clic en la opción de menú **Autenticación/autorización**.

3. En **URL de redirección externas permitidas**, introduzca `url_scheme_of_your_app://easyauth.callback`.  El valor de **esquema_de_dirección_URL_de_la_aplicación** de esta cadena es el esquema de dirección URL para la aplicación móvil.  Debe seguir la especificación normal de las direcciones URL para un protocolo (usar únicamente letras y números, y comenzar por una letra).  Debe tomar nota de la cadena que elija ya que necesitará ajustar el código de la aplicación móvil con el esquema de direcciones URL en varios sitios.

4. Haga clic en **Aceptar**.

5. Haga clic en **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación. El error se registra en la consola del depurador. De esta forma, en Visual Studio, debería ver el error en la ventana de salida.

&nbsp;&nbsp;Este error no autorizado se produce porque la aplicación intenta acceder al back-end de la aplicación móvil como usuario no autenticado. La tabla *TodoItem* ahora requiere autenticación.

Luego, actualizará la aplicación cliente para solicitar recursos del back-end de la aplicación móvil con un usuario autenticado.

## <a name="add-authentication-to-the-app"></a>Incorporación de autenticación a la aplicación
En esta sección, modificará la aplicación para mostrar una pantalla de inicio de sesión antes de mostrar los datos. Cuando se inicie la aplicación, no se conectará a Servicios de aplicaciones y no mostrará datos. Después de que el usuario intente actualizar una vez, aparecerá la pantalla de inicio y, una vez que haya iniciado sesión, se mostrará la lista de tareas pendientes.

1. En el proyecto de cliente, abra el archivo **QSTodoService.cs** y agregue la siguiente instrucción using y `MobileServiceUser`, con descriptor de acceso a la clase QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Agregue un nuevo método denominado **Authenticate** a **QSTodoService** con la siguiente definición:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Si usa un proveedor de identidades que no sea una cuenta de Facebook, cambie el valor que usó con **LoginAsync** por uno de los siguientes: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modifique la definición del método de **ViewDidLoad**, para lo que debe quitar la llamada a **RefreshAsync()** cerca del final:
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();
   
            RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync();
            }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync();
        }
4. Modifique el método **RefreshAsync** para autenticar si el valor de la propiedad **User** es null. Agregue el siguiente código al principio de la definición del método:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Abra **AppDelegate.cs** y agregue el siguiente método:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Abra el archivo **Info.plist**, vaya a **Tipos de URL** en la sección **Opciones avanzadas**. Ahora, configure el **Identificador** y los **Esquemas de URL** de su tipo de dirección URL y haga clic en **Agregar tipo de dirección URL**. El valor de **Esquemas de URL** debe ser el mismo que el de {url_scheme_of_your_app}.
7. En Visual Studio o Xamarin Studio conectado a su host de compilación de Xamarin en el equipo Mac, ejecute el proyecto de cliente destinado a un dispositivo o un emulador. Compruebe que la aplicación no muestra ningún dato.
   
    Despliegue la lista de tareas para actualizar la pantalla, lo que dará lugar a que aparezca la pantalla de inicio de sesión. Una vez que haya proporcionado credenciales válidas, la aplicación mostrará la lista de tareas pendientes y podrá actualizar los datos.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Creación de una aplicación Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md