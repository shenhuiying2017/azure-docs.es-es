---
title: "Versión de prueba de una aplicación web habilitada para Azure AD B2C"
description: "Guía de inicio rápido para probar una aplicación web de ASP.NET de ejemplo que usa Azure Active Directory B2C para proporcionar inicio de sesión de usuario."
services: active-directory-b2c
author: PatAltimore
manager: mtillman
ms.reviewer: saraford
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 2/13/2018
ms.author: patricka
ms.openlocfilehash: 87c180445038b1205e2f6aab1ce721765ecb35c9
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-test-drive-an-azure-ad-b2c-enabled-web-app"></a>Inicio rápido: Versión de prueba de una aplicación web habilitada para Azure AD B2C

Azure Active Directory (Azure AD) B2C proporciona administración de identidades en la nube para mantener la protección de su aplicación, empresa y clientes. Azure AD B2C permite que las aplicaciones puedan autenticarse con cuentas de redes sociales y cuentas de empresa mediante protocolos estándar abiertos.

En esta guía de inicio rápido, se utiliza una aplicación de ASP.NET de ejemplo habilitada para Azure AD B2C para iniciar sesión mediante un proveedor de identidades de redes sociales y llamar a una API web protegida por Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de **ASP.NET y desarrollo web**. 
* Una cuenta de redes sociales de Facebook, Google, Microsoft o Twitter.

## <a name="download-the-sample"></a>Descarga del ejemplo

[Descargue un archivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="run-the-app-in-visual-studio"></a>Ejecución de una aplicación en Visual Studio

En la carpeta de proyecto de la aplicación de ejemplo, abra la solución `B2C-WebAPI-DotNet.sln` en Visual Studio.

Hay dos proyectos en la solución de ejemplo:

**Aplicación de ejemplo de aplicación web (TaskWebApp)**: aplicación web para crear y editar una lista de tareas. La aplicación web utiliza la directiva de **registro o de inicio de sesión** que los usuarios se registren o inicien sesión.

**Aplicación de ejemplo de API web (TaskService)**: API web que admite la funcionalidad de creación, lectura, actualización y eliminación de la lista de tareas. Azure AD B2C protege la API web y la aplicación web la llama.

Para esta guía de inicio rápido, debe ejecutar los proyectos `TaskWebApp` y `TaskService` al mismo tiempo. 

1. Seleccione la solución `B2C-WebAPI-DotNet` en el Explorador de soluciones.
2. En el menú de Visual Studio, seleccione **Proyecto > Establecer proyectos de inicio...** . 
3. Seleccione el botón de radio **Proyectos de inicio múltiples**.
4. Cambie la **Acción** de ambos proyectos a **Iniciar**. Haga clic en **OK**.

Presione **F5** para depurar las dos aplicaciones. Cada aplicación se abre en su propia pestaña del explorador:

`https://localhost:44316/`: esta página es la aplicación web ASP.NET. Interactúe directamente con esta aplicación en la guía de inicio rápido.
`https://localhost:44332/`: esta página es la API web a la que llama la aplicación web ASP.NET.

## <a name="create-an-account"></a>Crear una cuenta

Haga clic en el vínculo **Sign up / Sign in** (Registro/inicio de sesión) en la aplicación web de ASP.NET para iniciar el flujo de trabajo **Sign Up or Sign In** (Registrarse o iniciar sesión) en función de una directiva de Azure AD B2C.

![Aplicación web ASP.NET de ejemplo](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

El ejemplo admite varias opciones de registro: usar un proveedor de identidades de redes sociales o crear una cuenta local con una dirección de correo electrónico. Para este tutorial rápido, use una cuenta de proveedor de identidades sociales de Facebook, Google, Microsoft o Twitter. 

### <a name="sign-up-using-a-social-identity-provider"></a>Registro con un proveedor de identidades de redes sociales

Azure AD B2C presenta una página de inicio de sesión personalizada para una marca ficticia llamada Wingtip Toys para la aplicación web de ejemplo. 

1. Para registrarse con un proveedor de identidades de redes sociales, haga clic en el botón del proveedor de identidades que desee usar.

    ![Proveedor de inicio de sesión o registro](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    Debe autenticarse (iniciar sesión) con las credenciales de su cuenta de redes sociales y autorizar a la aplicación para que lea la información de su cuenta de redes sociales. Al conceder acceso, la aplicación puede recuperar la información del perfil de la cuenta de redes sociales como el nombre y la ciudad. 

2. Finalice el proceso de inicio de sesión para el proveedor de identidades. Por ejemplo, si elige Twitter, escriba sus credenciales de Twitter y haga clic en **Iniciar sesión**.

    ![Autenticación y autorización con una cuenta de redes sociales](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

    Los detalles del perfil de su nueva cuenta de Azure AD B2C se rellenan automáticamente con la información de su cuenta de redes sociales.

3. Actualice los campos de nombre para mostrar, puesto que ocupa y ciudad y haga clic en **Continuar**.  Los valores que especifique se usan para el perfil de cuenta de usuario de Azure AD B2C.

    ![Detalles del perfil de registro de nueva cuenta](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

    Ha usado correctamente la aplicación web de ejemplo que usa una directiva de Azure AD B2C para autenticar con un proveedor de identidades y crear una cuenta de usuario de Azure AD B2C. 

## <a name="edit-your-profile"></a>Edición del perfil

Azure Active Directory B2C proporciona funcionalidad para permitir a los usuarios actualizar sus perfiles. La aplicación web de ejemplo usa una directiva de perfil de edición de Azure AD B2C para el flujo de trabajo. 

1. En la barra de menú de la aplicación web, haga clic en el nombre de perfil y seleccione **Editar perfil** para editar el perfil que ha creado.

    ![Edición de perfil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Cambie el **nombre para mostrar** y la **ciudad**.  
3. Haga clic en **Continuar** para actualizar su perfil. El nuevo nombre para mostrar se muestra en la parte superior derecha de la página principal de la aplicación web.

## <a name="access-a-protected-web-api-resource"></a>Acceso a un recurso de API web protegido

1. Haga clic en la **lista de tareas pendientes** para escribir y modificar los elementos de la lista de tareas pendientes. 

2. Especifique el texto en el cuadro de texto **Nuevo elemento**. Haga clic en **Agregar** para llamar a la API web protegida de Azure AD B2C que agrega un elemento de la lista de tareas pendientes.

    ![Adición de un elemento de la lista de tareas pendientes](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    La aplicación web ASP.NET incluye un token de acceso de Azure AD en la solicitud al recurso de API web protegido para realizar operaciones en los elementos de la lista de tareas pendientes del usuario.

Ha utilizado correctamente su cuenta de usuario de Azure AD B2C para realizar una llamada autorizada a una API web de Azure AD B2C protegida.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el inquilino de Azure AD B2C si tiene previsto leer otros tutoriales o guías de inicio rápido de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el inquilino de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, ha utilizado una aplicación de ASP.NET de ejemplo habilitada para Azure AD B2C para iniciar sesión con una página de inicio de sesión personalizada, iniciar sesión con un proveedor de identidades de redes sociales, crear una cuenta de Azure AD B2C y llamar a una API web protegida por Azure AD B2C. 

Continúe el tutorial para aprender a configurar la aplicación de ASP.NET de ejemplo para usar su propio inquilino de Azure AD B2C.

> [!div class="nextstepaction"]
> [Tutorial: Autenticación de usuarios con Azure Active Directory B2C en una aplicación web de ASP.NET](active-directory-b2c-tutorials-web-app.md)