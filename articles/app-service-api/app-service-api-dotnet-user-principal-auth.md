---
title: "Autenticación de usuario para API Apps en Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo proteger una aplicación de API del Servicio de aplicaciones de Azure al permitir el acceso únicamente a usuarios autenticados."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: 3896760d-46ff-4b67-b98a-edd233f24758
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 16a2c6f8e704528c38054bba394e3d11098077e0
ms.openlocfilehash: c39e7bca6f339a1797a9344778e6a01ffa8adecc


---
# <a name="user-authentication-for-api-apps-in-azure-app-service"></a>Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure
## <a name="overview"></a>Información general
Este artículo muestra cómo proteger una aplicación de API de Azure para que solo puedan llamarla los usuarios autenticados. Se da por supuesto que ha leído [Autenticación y autorización en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).

Aprenderá a realizar los siguientes procedimientos:

* Cómo configurar un proveedor de autenticación, con detalles para Azure Active Directory (Azure AD).
* Cómo consumir una aplicación de API protegida mediante la [Biblioteca de autenticación de Active Directory (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

El artículo contiene dos secciones:

* La sección [Configuración de la autenticación de usuarios en el Servicio de aplicaciones de Azure](#authconfig) explica de forma general cómo configurar la autenticación de usuarios de cualquier aplicación de API y se aplica por igual a todos los marcos compatibles con el Servicio de aplicaciones, incluyendo .NET, Node.js y Java.
* A partir de la sección [Continuación de los tutoriales de aplicaciones de API de .NET](#tutorialstart) , el artículo lo guía en la configuración de una aplicación de ejemplo con un back-end de .NET y un front-end de AngularJS para que use Azure Active Directory para la autenticación de los usuarios. 

## <a name="a-idauthconfiga-how-to-configure-user-authentication-in-azure-app-service"></a><a id="authconfig"></a> Configuración de la autenticación de usuarios en el Servicio de aplicaciones de Azure
Esta sección proporciona instrucciones generales que se aplican a cualquier aplicación de API. Para obtener los pasos específicos para la aplicación de ejemplo de .NET To Do List, vaya a [Continuación de los tutoriales de aplicaciones de API de .NET](#tutorialstart).

1. En [Azure Portal](https://portal.azure.com/), vaya a la hoja **Configuración** de la aplicación de API que quiere proteger, busque la sección **Características** y haga clic en **Autenticación/autorización**.
   
    ![Autenticación/autorización del Portal de Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)
2. En la hoja **Autenticación/autorización**, haga clic en **Activado**.
3. Seleccione una de las opciones de la lista desplegable **Acción necesaria cuando la solicitud no está autenticada** .
   
   * Si desea que solo puedan acceder a la aplicación de API las llamadas que estén autenticadas, elija una de las opciones de **Iniciar sesión con** . Esta opción permite proteger la aplicación de API sin escribir código que se ejecute en ella.
   * Si desea que todas las llamadas accedan a la aplicación de API, elija **Permitir solicitud (sin acción)**. Esta opción se puede utilizar para mostrar a los autores de llamadas no autenticados los proveedores de autenticación entre los que pueden elegir. Con esta opción es preciso escribir código para controlar la autorización.
     
     Para más información, consulte [Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-authentication.md#multiple-protection-options).
4. Seleccione uno o varios **proveedores de autenticación**.
   
    La imagen muestra las opciones que requieren que Azure AD autentique a todos los autores de llamadas.
   
    ![Hoja Autenticación/autorización del Portal de Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
   
    Al elegir un proveedor de autenticación, el portal muestra la hoja de configuración de dicho proveedor. 
   
    Para más información sobre cómo configurar las hojas de configuración de los proveedores de autenticación, consulte [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (El vínculo abre un artículo sobre Azure AD, pero el propio artículo contiene pestañas con vínculos a artículos para los restantes proveedores de autenticación).
5. Cuando termine con la hoja de configuración del proveedor de autenticación, haga clic en **Aceptar**.
6. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.

Al hacerlo, el Servicio de aplicaciones autentica todas las llamadas API antes de que lleguen a la aplicación de API. Estos servicios de autenticación funcionan igual en todos los lenguajes que admite el Servicio de aplicaciones, como .NET, Node.js y Java. 

Para realizar llamadas API autenticadas, el autor de la llamada incluye el token de portador de OAuth 2.0 del proveedor de autenticación en el encabezado de autorización de las solicitudes HTTP. El token se puede adquirir mediante el SDK del proveedor de autenticación.

## <a name="a-idtutorialstarta-continuing-the-net-api-apps-tutorials"></a><a id="tutorialstart"></a> Continuación de los tutoriales de aplicaciones de API de .NET
Si está siguiendo la serie de tutoriales de Node.js o Java para aplicaciones de API, vaya al siguiente artículo, [Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-service-principal-auth.md). 

Si está siguiendo la serie de tutoriales de .NET para aplicaciones de API y ya ha implementado la aplicación de ejemplo como se indica en el [primer](app-service-api-dotnet-get-started.md) y el [segundo](app-service-api-cors-consume-javascript.md) tutorial, vaya a la sección [Configuración de la autenticación en App Service y Azure AD](#azureauth).

Si desea seguir este tutorial sin completar el primero ni el segundo, lleve a cabo los pasos siguientes que explican cómo empezar a trabajar usando un proceso automatizado para implementar la aplicación de ejemplo.

> [!NOTE]
> Los siguientes pasos lo llevan al mismo punto de partida, como si hubiera hecho los dos primeros tutoriales, con una excepción: Visual Studio ya no sabrá en qué aplicación web o aplicación de API se implementa cada proyecto. Esto significa que este tutorial no ofrecerá instrucciones exactas sobre cómo implementar en los objetivos correctos. Si no se siente cómodo averiguando los pasos de implementación por su cuenta, se recomienda que siga la serie de tutoriales desde el [primero](app-service-api-dotnet-get-started.md) en lugar de comenzar con este proceso de implementación automatizado.
> 
> 

1. Asegúrese de que cumple todos los requisitos previos descritos en el [primer tutorial](app-service-api-dotnet-get-started.md). Además de los requisitos previos descritos, en estos tutoriales de autenticación se da por hecho que ha trabajado con aplicaciones web del Servicio de aplicaciones y aplicaciones de API en Visual Studio y el Portal de Azure.
2. Haga clic en el botón **Deploy to Azure** (Implementar en Azure) en el [archivo Léame del repositorio del ejemplo To Do List](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) para implementar las aplicaciones de API y la aplicación web. Tome nota del grupo de recursos de Azure que se crea, ya que puede usar más adelante esta información para buscar nombres de aplicación web y de aplicación de API.
3. Descargue o clone el [repositorio del ejemplo To Do List](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) para obtener el código que funcionará localmente en Visual Studio.

## <a name="a-idazureautha-set-up-authentication-in-app-service-and-azure-ad"></a><a id="azureauth"></a> Configuración de la autenticación en el Servicio de aplicaciones y Azure AD
En este momento. la aplicación se ejecuta en el Servicio de aplicaciones de Azure sin necesidad de que los usuarios se autentiquen. En esta sección se agregará la autenticación, para lo que es preciso realizar las siguientes tareas:

* Configurar el Servicio de aplicaciones para que requiera la autenticación de Azure Active Directory (Azure AD) para llamar a la aplicación de API de nivel intermedio.
* Crear una aplicación de Azure.
* Configurar la aplicación de Azure AD para enviar el token de portador después de iniciar sesión en el front-end de AngularJS. 

Si experimenta problemas al seguir las instrucciones del tutorial, consulte la sección [Solución de problemas](#troubleshooting) al final. 

### <a name="configure-authentication-for-the-middle-tier-api-app"></a>Configuración de la autenticación para la aplicación de API de nivel intermedio
1. En [Azure Portal](https://portal.azure.com/), vaya a la hoja **Configuración** de la aplicación de API que creó para el proyecto ToDoListAPI, busque la sección **Características** y haga clic en **Autenticación/autorización**.
   
    ![Autenticación/autorización del Portal de Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)
2. En la hoja **Autenticación/autorización**, haga clic en **Activado**.
3. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.
   
    Esta opción garantiza que ninguna solicitud no autenticada llegará a la aplicación de API. 
4. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.
   
    ![Hoja Autenticación/autorización del Portal de Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
5. En la hoja** Configuración de Azure Active Directory**, haga clic en **Rápido**.
   
    ![Opción Express de Autenticación/autorización del Portal de Azure](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)
   
    Con la opción **Rápido**, App Service puede crear automáticamente una aplicación de Azure AD en su [inquilino](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)de Azure AD. 
   
    No es preciso que cree un inquilino, porque todas las cuenta de Azure tienen uno automáticamente.
6. En **Modo de administración**, haga clic en **Crear nueva aplicación de AD** si no está seleccionada y observe el valor que se encuentra en el cuadro de texto **Crear aplicación**; más adelante buscará esta aplicación de AAD en el Portal de Azure clásico.
   
    ![Configuración de Azure AD del Portal de Azure](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)
   
    Azure creará automáticamente una aplicación de Azure AD con el nombre indicado en su inquilino de Azure AD. De forma predeterminada, la aplicación de Azure AD recibe el mismo nombre que la aplicación de API. Si lo prefiere, escriba un nombre diferente.
7. Haga clic en **Aceptar**.
8. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.
   
    ![Haga clic en Guardar](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

Ahora los usuarios de su inquilino de Azure AD son los únicos que pueden llamar a la aplicación de API.

### <a name="optional-test-the-api-app"></a>Opcional: prueba de la aplicación de API
1. En un explorador, vaya a la dirección URL de la aplicación de API: en la hoja **Aplicación de API** de Azure Portal, haga clic en el vínculo bajo **URL**.  
   
    Se le redirigirá a una pantalla de inicio de sesión porque no se permite que las solicitudes no autenticadas tengan acceso a la aplicación de API.
   
    Si el explorador va a la página que indica que "se creó correctamente", es posible que el explorador ya haya iniciado sesión (en ese caso, abra una ventana de InPrivate o Incognito y vaya a la dirección URL de la aplicación de API).
2. Inicie sesión con las credenciales de un usuario en su inquilino de Azure AD.
   
    Una vez iniciada la sesión, en el explorador aparece una página que indica que "se creó correctamente".
3. Cierre el explorador.

### <a name="configure-the-azure-ad-application"></a>Configuración de la aplicación de Azure AD
Cuando se configuró la autenticación de Azure AD, el Servicio de aplicaciones creó automáticamente una aplicación de Azure AD. De forma predeterminada, la nueva aplicación de Azure AD se configuró para proporcionar el token de portador a la dirección URL de la aplicación de API. En esta sección, configurará la aplicación de Azure AD para que proporcione el token de portador al front-end de AngularJS, en lugar de directamente a la aplicación de API de nivel intermedio. El front-end de AngularJS enviará el token a la aplicación de API al llamarla.

> [!NOTE]
> Para mantener el proceso lo más sencillo posible, en este tutorial se utiliza una aplicación única de Azure AD tanto para el front-end como para la aplicación de API de nivel intermedio. Otra opción es usar dos aplicaciones de Azure AD. En ese caso, tendría que conceder permiso a la aplicación de Azure AD del front-end para llamar a la aplicación de Azure AD de nivel intermedio. Este enfoque de varias aplicaciones le proporcionaría un control más específico sobre los permisos para cada nivel.
> 
> 

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.
   
   Tiene que usar al portal clásico porque varias de las opciones de Azure Active Directory a las que necesita acceder aún no están disponibles en el Portal de Azure actual.
2. En la pestaña **Directorio** , haga clic en su inquilino de AAD.
   
   ![Azure AD en el Portal clásico](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)
3. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y, después, en la marca de verificación.
   
   También es posible que tenga que actualizar la página para ver la nueva aplicación.
4. En la lista de aplicaciones, haga clic en el nombre de aquella que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API.
   
   ![Pestaña Aplicaciones de Azure AD](./media/app-service-api-dotnet-user-principal-auth/appstab.png)
5. Haga clic en **Configurar**.
   
   ![Pestaña Configurar de Azure AD](./media/app-service-api-dotnet-user-principal-auth/configure.png)
6. En **URL de inicio de sesión** , escriba la dirección URL de la aplicación web de AngularJS, sin barra diagonal final.
   
   Por ejemplo, https://todolistangular.azurewebsites.net.
   
   ![URL de inicio de sesión](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)
7. En **URL de respuesta** , escriba la dirección URL de su aplicación web, sin barra diagonal final.
   
   Por ejemplo: https://todolistsangular.azurewebsites.net.
8. Haga clic en **Save**.
   
   ![URL de respuesta](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)
9. En la parte inferior de la página, haga clic en **Administrar manifiesto > Descargar manifiesto**.
   
   ![Descargar el manifiesto](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)
10. Descargue el archivo en una ubicación en que pueda editar.
11. En el archivo de manifiesto descargado, busque la propiedad `oauth2AllowImplicitFlow`. Cambie el valor de esta propiedad de `false` a `true` y después guarde el archivo.
    
    Esta configuración es necesaria para el acceso desde una aplicación de una página de JavaScript. Permite que el token de portador de Oauth 2.0 se devuelva al fragmento de dirección URL.
12. Haga clic en **Administrar manifiesto > Cargar manifiesto** y cargue el archivo actualizado en el paso anterior.
    
    ![Cargar el manifiesto](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)
13. Copie el valor de **Id. de cliente** y guárdelo en un lugar donde pueda recuperarlo más adelante.

## <a name="configure-the-todolistangular-project-to-use-authentication"></a>Configuración del proyecto ToDoListAngular para que use la autenticación
En esta sección, cambiará el front-end de AngularJS para que use la Biblioteca de autenticación de Active Directory (ADAL) para JS para adquirir en Azure AD un token de portador de usuario que ha iniciado sesión. El código incluirá el token en las solicitudes HTTP enviadas al nivel intermedio, tal como se muestra en el diagrama siguiente. 

![Diagrama de autenticación de usuario](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Realice los cambios siguientes en los archivos del proyecto ToDoListAngular.

1. Abra el archivo *index.cshtml*.
2. Quite la marca de comentario de las líneas que hacen referencia a la Biblioteca de autenticación de Active Directory (ADAL) para scripts JS.
   
        <script src="app/scripts/adal.js"></script>
        <script src="app/scripts/adal-angular.js"></script>
3. Abra el archivo *app/scripts/app.js* .
4. Convierta en comentario el bloque de código marcado para "sin autenticación" y quite la marca de comentario del bloque de código marcado para "con autenticación".
   
    Este cambio hace referencia al proveedor de autenticación de ADAL JS y le proporciona los valores de configuración. En los pasos siguientes, establezca los valores de configuración tanto de su aplicación de API como de la aplicación de Azure AD.
5. En el código que establece la variable `endpoints` , establezca la dirección URL de la API en la dirección URL de la aplicación de API que creó para el proyecto ToDoListAPI y establezca el identificador de la aplicación de Azure AD en el identificador de cliente que copió del Portal de Azure clásico.
   
    El código será similar al del ejemplo siguiente:
   
        var endpoints = {
            "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };
6. En la llamada a `adalProvider.init`, establezca `tenant` en el nombre del inquilino y `clientId` en el mismo valor que usó en el paso anterior.
   
    El código será similar al del ejemplo siguiente:
   
        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );
   
    Estos cambios en `app.js` especifican que el código de llamada y la API a la que se ha llamado están en la misma aplicación de Azure AD.
7. Abra el archivo *app/scripts/homeCtrl.js* .
8. Convierta en comentario el bloque de código marcado para "sin autenticación" y quite la marca de comentario del bloque de código marcado para "con autenticación".
9. Abra el archivo *app/scripts/indexCtrl.js* .
10. Convierta en comentario el bloque de código marcado para "sin autenticación" y quite la marca de comentario del bloque de código marcado para "con autenticación".

### <a name="deploy-the-todolistangular-project-to-azure"></a>Implementación del proyecto ToDoListAngular en Azure
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAngular y, después, en **Publicar**.
2. Haga clic en **Publicar**.
   
    Visual Studio implementa el proyecto y abre un explorador en la dirección URL base de la aplicación web. Esto mostrará una página de error 403, lo que es normal cuando se intenta ir a una dirección URL base de Web API desde un explorador.
   
    Para poder probar la aplicación, será preciso que previamente realice un cambio en la aplicación de API de nivel intermedio.
3. Cierre el explorador.

## <a name="configure-the-todolistapi-project-to-use-authentication"></a>Configuración del proyecto ToDoListAPI para que use la autenticación
Actualmente, el proyecto ToDoListAPI envía "*" como valor de `owner` a ToDoListDataAPI. En esta sección cambiará el código para enviar el identificador del usuario que ha iniciado sesión.

Realice los cambios siguientes en el proyecto ToDoListAPI.

1. Abra el archivo *Controllers/ToDoListController.cs* y quite la marca de comentario de la línea en cada método de acción que establece `owner` en el valor de la notificación `NameIdentifier` de Azure AD. Por ejemplo:
   
        owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;
   
    **Importante**: No quite la marca de comentario del código en el método `ToDoListDataAPI`; lo hará más adelante en el tutorial de autenticación de la entidad de servicio.

### <a name="deploy-the-todolistapi-project-to-azure"></a>Implementación del proyecto ToDoListAPI en Azure
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAPI y haga clic en **Publicar**.
2. Haga clic en **Publicar**.
   
    Visual Studio implementa el proyecto y abre un explorador en la URL base de la aplicación de API.
3. Cierre el explorador.

### <a name="test-the-application"></a>Prueba de la aplicación
1. Vaya a la dirección URL de la aplicación web, **mediante HTTPS, no HTTP**.
2. Haga clic en la pestaña **Lista de tareas pendientes** .
   
    Se le pedirá que inicie sesión.
3. Inicie sesión con las credenciales de usuario en su inquilino de AAD.
4. Aparece la página **Lista de tareas pendientes** .
   
   ![Página de Lista de tareas pendientes](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)
   
   No se muestran tareas pendientes porque hasta todas han sido para el propietario "*". Ahora el nivel intermedio solicita tareas para el usuario que ha iniciado sesión y aún no se ha creado ninguna.
5. Agregue nuevas tareas pendientes para comprobar que la aplicación funciona.
6. En otra ventana del explorador, vaya a la dirección URL de la interfaz de usuario de Swagger para la aplicación de API de ToDoListDataAPI y haga clic en **ToDoList > Get** (ToDoList > Obtener). Escriba un asterisco en el parámetro `owner` y haga clic en **Try it out**(Probarlo).
   
   La respuesta muestra que las nuevas tareas pendientes tienen el identificador de usuario real de Azure AD en la propiedad Owner.
   
   ![Id. de propietario en respuesta JSON](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)

## <a name="building-the-projects-from-scratch"></a>Creación de los proyectos desde cero
Los dos proyectos de Web API se crearon con la plantilla de proyecto **Aplicación de API de Azure** y reemplazando el controlador de los valores predeterminados por un controlador de ToDoList. 

Para más información sobre cómo crear una aplicación de una página de AngularJS con un back-end de API web 2, consulte [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)(Laboratorio práctico: creación de una aplicación de una sola página con ASP.NET Web API y Angular.js). Para obtener información acerca de cómo agregar código de autenticación de Azure AD, consulte los siguientes recursos:

* [Seguridad de las aplicaciones de una sola página AngularJS con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introducing ADAL JS v1 (Presentación de ADAL JS v1)](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## <a name="troubleshooting"></a>Solución de problemas
[!INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Asegúrese de no confundir ToDoListAPI (nivel intermedio) con ToDoListDataAPI (capa de datos). Por ejemplo, compruebe que ha agregado autenticación a la aplicación de API de nivel intermedio, no a la capa de datos. 
* Asegúrese de que el código fuente de AngularJS hace referencia a la URL de la aplicación de API de nivel intermedio (ToDoListAPI, no ToDoListDataAPI) y al identificador de cliente de Azure AD correcto. 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial ha aprendido a utilizar la autenticación del Servicio de aplicaciones para una aplicación de API y a llamar a la aplicación de API mediante la biblioteca de ADAL JS. En el siguiente tutorial aprenderá a [acceder de forma segura a la aplicación de API en escenarios de servicio a servicio](app-service-api-dotnet-service-principal-auth.md).




<!--HONumber=Jan17_HO4-->


