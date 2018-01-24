---
title: "Introducción a Azure AD AngularJS | Microsoft Docs"
description: "Creación de una aplicación de una sola página AngularJS que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d46f04a3e9fdb1beaf11c769ecdd7b807c5f817f
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-angularjs-getting-started"></a>Introducción a AngularJS de Azure AD

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Con Azure Active Directory (Azure AD), es sencillo y directo agregar llamadas de inicio y cierre de sesión, así como llamadas seguras de API de OAuth a las aplicaciones de una sola página.  Permite que la aplicación autentique a los usuarios con sus cuentas de Active Directory de Windows Server, además de consumir cualquier web API que Azure AD ayuda a proteger, como las API de Office 365 o la API de Azure.

Para las aplicaciones de JavaScript que se ejecutan en un explorador, Azure AD proporciona la biblioteca de autenticación de Active Directory (ADAL), o adal.js. La única finalidad de adal.js es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes (ToDo) de AngularJS que permita realizar las siguientes acciones:

* Iniciar sesión del usuario en la aplicación con Azure AD como proveedor de identidades.

* Mostrar información sobre el usuario
* Realizar una llamada con seguridad a la API de la lista de tareas pendientes de la aplicación mediante tokens de portador de Azure AD.
* Cerrar la sesión del usuario en la aplicación

Para crear la aplicación de trabajo completa, debe realizar estas acciones:

1. Registrar la aplicación con Azure AD.
2. Instalar ADAL y configurar la aplicación de una sola página.
3. Usar ADAL para ayudar a proteger las páginas en la aplicación de una sola página.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). También necesita a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Paso 1: Registro de la aplicación DirectorySearcher
Para habilitar la aplicación a fin de autenticar a los usuarios y obtener tokens, primero debe registrarla en el inquilino de Azure AD:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si inició sesión en varios directorios, debe asegurarse de que está viendo el directorio correcto. Para ello, en la barra superior, haga clic en su cuenta. En la lista **Directorio** lista, elija el inquilino de Azure AD donde desea registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y luego seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y luego seleccione **Agregar**.
5. Siga las indicaciones y cree una nueva aplicación web y/o API web:
  * **Nombre** describe la aplicación a los usuarios.
  * **URI de redirección** es la ubicación a la que Azure AD devolverá los tokens. La ubicación predeterminada para este ejemplo es `https://localhost:44326/`.
6. Cuando termine el registro, Azure AD asignará un identificador de aplicación único a la aplicación.  Necesitará este valor en las secciones siguientes, así que cópielo de la pestaña de la aplicación.
7. Adal.js utiliza el flujo implícito de OAuth para comunicarse con Azure AD. Debe habilitar el flujo implícito para la aplicación mediante estos pasos:
  1. Haga clic en la aplicación y seleccione **Manifiesto** para abrir el editor de manifiestos en línea.
  2. Busque la propiedad `oauth2AllowImplicitFlow`. Establezca su valor en `true`.
  3. Haga clic en **Guardar** para guardar el manifiesto.
8. Conceda permisos para la aplicación en todo el inquilino. Vaya a **Configuración** > **Propiedades** > **Permisos necesarios** y haga clic en el botón **Conceder permisos** de la barra superior. Haga clic en **Sí** para continuar.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Paso 2: Instalación de ADAL y configuración de aplicación de una sola página
Ahora que tiene una aplicación en Azure AD, puede instalar adal.js y escribir el código relacionado con la identidad.

### <a name="configure-the-javascript-client"></a>Configuración del cliente de JavaScript
Comience agregando adal.js al proyecto TodoSPA mediante la Consola del Administrador de paquetes:
  1. Descargue [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) y agréguelo al directorio del proyecto `App/Scripts/`.
  2. Descargue [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) y agréguelo al directorio del proyecto `App/Scripts/`.
  3. Cargue cada script antes del fin de `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Configuración del servidor back-end
Para que la API de tareas pendientes del back-end de la aplicación de una sola página acepte tokens del explorador, el back-end necesita información de configuración acerca del registro de la aplicación. En el proyecto TodoSPA, abra `web.config`. Reemplace los valores de los elementos de la sección `<appSettings>` para que reflejen los valores usados en Azure Portal. El código hará referencia a estos valores cada vez que use ADAL.
  * `ida:Tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `ida:Audience` es el identificador de cliente de la aplicación que copió del portal.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Paso 3: Uso de ADAL para ayudar a proteger las páginas en la aplicación de una sola página
Adal.js se integra con la ruta de AngularJS y los proveedores HTTP, por lo que puede ayudar a proteger vistas individuales en la aplicación de una sola página.

1. En `App/Scripts/app.js`, importe el módulo adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicialice `adalProvider` con los valores de configuración del registro de su aplicación, también en `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Ayude a proteger la vista `TodoList` en la aplicación utilizando solamente una línea de código: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Resumen
Ahora tiene una aplicación de una sola página segura con capacidad para iniciar la sesión de los usuarios y emitir solicitudes protegidas de token de portador a su API de back-end. Cuando un usuario hace clic en el vínculo **TodoList**, adal.js le redireccionará automáticamente a Azure AD para iniciar sesión si es necesario. Además, adal.js adjuntará automáticamente un token de acceso a cualquier solicitud de Ajax que se envíe al servidor back-end de la aplicación.  

Los pasos anteriores son los mínimos necesarios para compilar una aplicación de una sola página mediante adal.js. Pero algunas otras características son útiles en una aplicación de una sola página:

* Para emitir explícitamente solicitudes de inicio y cierre de sesión, puede definir funciones en sus controladores que invocan adal.js.  En `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Puede que desee presentar información de usuario en la interfaz de usuario de la aplicación. El servicio ADAL ya se ha agregado al controlador `userDataCtrl`, por lo que puede tener acceso al objeto `userInfo` en la vista asociada, `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Hay muchos casos en los que deseará saber si el usuario ha iniciado sesión o no. También puede utilizar el objeto `userInfo` para recopilar esta información.  Por ejemplo, en `index.html` puede mostrar el botón **Iniciar sesión** o **Cerrar sesión** en función del estado de autenticación:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

La aplicación de una sola página integrada con Azure AD puede autenticar a los usuarios, efectuar llamadas seguras a back-end mediante OAuth 2.0 y obtener información básica sobre el usuario. Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la aplicación de una sola página de la lista de tareas pendientes e inicie sesión con uno de esos usuarios. Agregue tareas a la lista de tareas pendientes de los usuarios, cierre la sesión y vuelva a iniciarla.

Adal.js facilita la incorporación de características comunes de identidad en la aplicación. Se encarga de todo el trabajo duro: administración de la memoria caché, compatibilidad con el protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de los tokens caducados, etc.

Como referencia, en [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) está disponible el ejemplo finalizado (sin sus valores de configuración).

## <a name="next-steps"></a>pasos siguientes
Ahora puede pasar a otros escenarios. Puede intentar [llamar a una API web CORS desde una aplicación de una sola página](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
