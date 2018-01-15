---
title: "Incorporación de autenticación en iOS con Azure Mobile Apps"
description: "Obtenga información acerca de cómo usar las Azure Mobile Apps para autenticar a los usuarios de su aplicación iOS en una variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: e0eeee05ebad2e8148752f988bbbc2f6a0d7c296
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-ios-app"></a>Incorporación de la autenticación a la aplicación iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tutorial podrá agregar la autenticación al proyecto de [inicio rápido de iOS] mediante un proveedor de identidades compatible. Este tutorial está basado en el tutorial de [inicio rápido de iOS] , que debe completar primero.

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adición de la aplicación a las direcciones URL de redirección externa permitidas

La autenticación segura requiere que se defina un nuevo esquema de dirección URL para la aplicación.  Esto permite que el sistema de autenticación se redirija a la aplicación una vez completado el proceso de autenticación.  En este tutorial, se usará el esquema de dirección URL _appname_.  Sin embargo, puede utilizar cualquier otro esquema de dirección URL que elija.  Debe ser único para la aplicación móvil.  Para habilitar la redirección en el lado de servidor:

1. En [Azure Portal], seleccione el servicio App Service.

2. Haga clic en la opción de menú **Autenticación/autorización**.

3. Haga clic en **Azure Active Directory** en la sección **Proveedores de autenticación**.

4. Establezca el **modo de administración** en **Avanzado**.

5. En **URL de redirección externas permitidas**, introduzca `appname://easyauth.callback`.  El valor de _appname_ de esta cadena es el esquema de dirección URL para la aplicación móvil.  Debe seguir la especificación normal de las direcciones URL para un protocolo (usar únicamente letras y números, y comenzar por una letra).  Debe tomar nota de la cadena que elija ya que necesitará ajustar el código de la aplicación móvil con el esquema de direcciones URL en varios sitios.

6. Haga clic en **OK**.

7. Haga clic en **Save**(Guardar).

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

En Xcode, presione **Ejecutar** para iniciar la aplicación. Se genera una excepción porque la aplicación intenta acceder al back-end como usuario sin autenticar, pero la tabla *TodoItem* ahora requiere autenticación.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación
**Objective-C**:

1. En el Mac, abra *QSTodoListViewController.m* en Xcode y agregue el siguiente método:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Cambie *google* a *microsoftaccount*, *twitter*, *facebook* o *windowsazureactivedirectory* si no usa Google como su proveedor de identidades. Si utiliza Facebook, debe [incluir los dominios de Facebook en la lista blanca][1] en su aplicación.

    Reemplace **urlScheme** por un nombre único para la aplicación.  El valor de urlScheme debe ser el mismo que el protocolo de esquema de dirección URL que especificó en el campo **URL de redirección externas permitidas** de Azure Portal. urlScheme se utiliza en la devolución de llamada de autenticación para volver a la aplicación una vez completada la solicitud de autenticación.

2. Reemplace `[self refresh]` en `viewDidLoad` en *QSTodoListViewController.m* por el código siguiente:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra el archivo `QSAppDelegate.h` y agregue el código siguiente:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra el archivo `QSAppDelegate.m` y agregue el código siguiente:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Agregue este código directamente antes de la línea `#pragma mark - Core Data stack`.  Reemplace el valor de _appname_ por valor de urlScheme que utilizó en el paso 1.

5. Abra el archivo `AppName-Info.plist` (reemplazando AppName por el nombre de la aplicación) y agregue el código siguiente:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Este código debe colocarse dentro del elemento `<dict>`.  Reemplace la cadena _appname_ (dentro de la matriz de **CFBundleURLSchemes**) por el nombre de la aplicación que eligió en el paso 1.  También puede realizar estos cambios en el editor plist; haga clic en el archivo `AppName-Info.plist` en XCode para abrir el editor de plist.

    Reemplace la cadena `com.microsoft.azure.zumo` de **CFBundleURLName** por el identificador del grupo Apple.

6. Presione *Ejecutar* para iniciar la aplicación y, después, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

**SWIFT**:

1. En el Mac, abra *ToDoTableViewController.swift* en Xcode y agregue el siguiente método:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Cambie *google* a *microsoftaccount*, *twitter*, *facebook* o *windowsazureactivedirectory* si no usa Google como su proveedor de identidades. Si utiliza Facebook, debe [incluir los dominios de Facebook en la lista blanca][1] en su aplicación.

    Reemplace **urlScheme** por un nombre único para la aplicación.  El valor de urlScheme debe ser el mismo que el protocolo de esquema de dirección URL que especificó en el campo **URL de redirección externas permitidas** de Azure Portal. urlScheme se utiliza en la devolución de llamada de autenticación para volver a la aplicación una vez completada la solicitud de autenticación.

2. Quite las líneas `self.refreshControl?.beginRefreshing()` y `self.onRefresh(self.refreshControl)` al final de `viewDidLoad()` en *ToDoTableViewController.swift*. Agregue una llamada a `loginAndGetData()` en su lugar:

    ```swift
    loginAndGetData()
    ```

3. Abra el archivo `AppDelegate.swift` y agregue la siguiente línea a la clase `AppDelegate`:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Reemplace el valor de _appname_ por valor de urlScheme que utilizó en el paso 1.

4. Abra el archivo `AppName-Info.plist` (reemplazando AppName por el nombre de la aplicación) y agregue el código siguiente:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Este código debe colocarse dentro del elemento `<dict>`.  Reemplace la cadena _appname_ (dentro de la matriz de **CFBundleURLSchemes**) por el nombre de la aplicación que eligió en el paso 1.  También puede realizar estos cambios en el editor plist; haga clic en el archivo `AppName-Info.plist` en XCode para abrir el editor de plist.

    Reemplace la cadena `com.microsoft.azure.zumo` de **CFBundleURLName** por el identificador del grupo Apple.

5. Presione *Ejecutar* para iniciar la aplicación y, después, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

La autenticación de App Service utiliza Apple Inter-App Communication.  Para más detalles sobre este tema, consulte la [documentación de Apple][2].
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[inicio rápido de iOS]: app-service-mobile-ios-get-started.md

