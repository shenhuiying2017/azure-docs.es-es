---
title: "Versión de prueba de una aplicación de una página habilitada para Azure AD B2C"
description: "Guía de inicio rápido para probar una aplicación de una página de ejemplo que utiliza Azure Active Directory B2C para autenticar y registrar usuarios."
services: active-directory-b2c
documentationcenter: 
author: PatAltimore
manager: mtillman
ms.reviewer: saraford
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 2/13/2018
ms.author: patricka
ms.openlocfilehash: e659fd228c2294313a62b331c8e530b7d34073ac
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-test-drive-an-azure-ad-b2c-enabled-single-page-app"></a>Inicio rápido: Versión de prueba de una aplicación de una página habilitada para Azure AD B2C

Azure Active Directory (Azure AD) B2C proporciona administración de identidades en la nube para mantener la protección de su aplicación, empresa y clientes. Azure AD B2C permite que las aplicaciones puedan autenticarse con cuentas de redes sociales y cuentas de empresa mediante protocolos estándar abiertos.

En esta guía de inicio rápido, se utiliza una aplicación de una página de ejemplo habilitada para Azure AD B2C para iniciar sesión mediante un proveedor de identidades de redes sociales y llamar a una API web protegida por Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de **ASP.NET y desarrollo web**.
* Instalar [Node.js](https://nodejs.org/en/download/)
* Una cuenta de redes sociales de Facebook, Google, Microsoft o Twitter.

## <a name="download-the-sample"></a>Descarga del ejemplo

[Descargue un archivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="run-the-sample-application"></a>Ejecutar la aplicación de ejemplo

Para ejecutar este ejemplo desde el símbolo del sistema de Node.js, realice el siguiente procedimiento: 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

La aplicación Node.js da como resultado el número de puerto que está escuchando en localhost.

```
Listening on port 6420...
```

Vaya a la dirección URL de la aplicación `http://localhost:6420` en un explorador web.

![Aplicación de ejemplo en el explorador](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Crear una cuenta

Haga clic en el botón **Login** (Iniciar sesión) para iniciar el flujo de trabajo **Sign Up or Sign In** (Registrarse o iniciar sesión) de Azure AD B2C basado en una directiva de Azure AD B2C. 

El ejemplo admite varias opciones de registro: usar un proveedor de identidades de redes sociales o crear una cuenta local con una dirección de correo electrónico. Para este tutorial rápido, use una cuenta de proveedor de identidades sociales de Facebook, Google, Microsoft o Twitter. 

### <a name="sign-up-using-a-social-identity-provider"></a>Registro con un proveedor de identidades de redes sociales

Azure AD B2C presenta una página de inicio de sesión personalizada para una marca ficticia llamada Wingtip Toys para la aplicación web de ejemplo. 

1. Para registrarse con un proveedor de identidades de redes sociales, haga clic en el botón del proveedor de identidades que desee usar.

    ![Proveedor de inicio de sesión o registro](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Debe autenticarse (iniciar sesión) con las credenciales de su cuenta de redes sociales y autorizar a la aplicación para que lea la información de su cuenta de redes sociales. Al conceder acceso, la aplicación puede recuperar la información del perfil de la cuenta de redes sociales como el nombre y la ciudad. 

2. Finalice el proceso de inicio de sesión para el proveedor de identidades. Por ejemplo, si elige Twitter, escriba sus credenciales de Twitter y haga clic en **Iniciar sesión**.

    ![Autenticación y autorización con una cuenta de redes sociales](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

    Los detalles del perfil de la cuenta nueva se rellenan automáticamente con la información de su cuenta de redes sociales. 

3. Actualice los campos de nombre para mostrar, puesto que ocupa y ciudad y haga clic en **Continuar**.  Los valores que especifique se usan para el perfil de cuenta de usuario de Azure AD B2C.

    Ha creado correctamente una nueva cuenta de usuario de Azure AD B2C que usa un proveedor de identidades. 

## <a name="access-a-protected-web-api-resource"></a>Acceso a un recurso de API web protegido

Haga clic en el botón **Llamar a API web** para obtener nombre para mostrar de la llamada de API web como un objeto JSON. 

![Respuesta de la API web](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

La aplicación de una página de ejemplo incluye un token de acceso de Azure AD en la solicitud para el recurso de API web protegido para realizar la operación para devolver el objeto JSON.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el inquilino de Azure AD B2C si tiene previsto leer otros tutoriales o guías de inicio rápido de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el inquilino de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, ha utilizado una aplicación de ASP.NET de ejemplo habilitada para Azure AD B2C para iniciar sesión con una página de inicio de sesión personalizada, iniciar sesión con un proveedor de identidades de redes sociales, crear una cuenta de Azure AD B2C y llamar a una API web protegida por Azure AD B2C. 

El siguiente paso es crear su propio inquilino de Azure AD B2C y configurar el ejemplo para la ejecución utilizando el inquilino. 

> [!div class="nextstepaction"]
> [Creación de un inquilino de Azure Active Directory B2C en Azure Portal](active-directory-b2c-get-started.md)