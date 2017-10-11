---
title: "Introducción a la autenticación de Aplicaciones móviles en Xamarin Android"
description: "Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de su aplicación Xamarin Android a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 8f9a1109018c708d52cdcb7b8bce43861cecd31c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adición de la autenticación a la aplicación Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios de una aplicación móvil desde la aplicación cliente. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Aplicaciones móviles de Azure. Una vez que la aplicación móvil finalice la autenticación y autorización correctamente, se mostrará el valor del identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Creación de una aplicación Xamarin.Android]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones
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

En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación. Esto sucede porque la aplicación intenta obtener acceso al back-end de la aplicación móvil como usuario sin autenticar. La tabla *TodoItem* ahora requiere autenticación.

Luego, actualizará la aplicación cliente para solicitar recursos del back-end de la aplicación móvil con un usuario autenticado.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación
La aplicación se actualiza para requerir a los usuarios que pulsen el botón **Iniciar sesión** y que se autentiquen para que se muestren los datos.

1. Agregue el siguiente código a la clase **TodoActivity** :
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Esto crea un método nuevo para autenticar un usuario y un controlador de método para un nuevo botón **Iniciar sesión** . El usuario del código de ejemplo anterior se autentica mediante el inicio de sesión en Facebook. Se usa un cuadro de diálogo para mostrar el identificador de usuario una vez autenticado.
   
   > [!NOTE]
   > Si usa un proveedor de identidades que no sea una cuenta de Facebook, cambie el valor que usó con **LoginAsync** por uno de los siguientes: *MicrosoftAccount*, *Twitter*, *Google* o *WindowsAzureActiveDirectory*.
   > 
   > 
2. En el método **OnCreate** , elimine o convierta en comentario la siguiente línea de código:
   
        OnRefreshItemsSelected ();
3. En el archivo Activity_To_Do.axml, agregue la siguiente definición del botón *LoginUser* antes del botón *AddItem* existente:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Agregue el siguiente elemento al archivo de recursos Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Abra el archivo AndroidManifest.xml, agregue el código siguiente dentro del elemento XML `<application>`:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador, e inicie sesión con el proveedor de identidades elegido. Una vez iniciada la sesión correctamente, la aplicación mostrará el identificador de inicio de sesión y la lista de tareas pendientes, y podrá realizar actualizaciones en los datos.

<!-- URLs. -->
[Creación de una aplicación Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md