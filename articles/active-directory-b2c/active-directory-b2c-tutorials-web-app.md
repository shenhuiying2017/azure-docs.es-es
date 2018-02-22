---
title: "Tutorial: Uso de Azure Active Directory B2C para la autenticación de usuarios en una aplicación web para ASP.NET"
description: "Tutorial sobre cómo usar Azure Active Directory B2C para registrar e iniciar sesión con usuarios en una aplicación web para ASP.NET."
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: ee006476f9e40e9d1a6e7213cb1881ca46ea75c2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-authenticate-users-with-azure-active-directory-b2c-in-an-aspnet-web-app"></a>Tutorial: Autenticación de usuarios con Azure Active Directory B2C en una aplicación web para ASP.NET

En este tutorial se muestra cómo usar Azure Active Directory (Azure AD) B2C para registrar e iniciar sesión con usuarios en una aplicación web para ASP.NET. Azure AD B2C permite que las aplicaciones puedan autenticarse en cuentas de las redes sociales, cuentas de empresa y cuentas de Azure Active Directory mediante protocolos estándar abiertos.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Registrar una aplicación web para ASP.NET de ejemplo en el inquilino de Azure AD B2C.
> * Crear directivas para el registro, el inicio de sesión, la edición de un perfil y el restablecimiento de contraseña.
> * Configurar la aplicación web de ejemplo para usar el inquilino de Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

* Crear su propio [inquilino de Azure AD B2C](active-directory-b2c-get-started.md).
* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de **ASP.NET y desarrollo web**.

## <a name="register-web-app"></a>Registro de una aplicación web

Las aplicaciones se deben [registrar](../active-directory/develop/active-directory-dev-glossary.md#application-registration) en el inquilino antes de que puedan recibir [tokens de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token) de Azure Active Directory. El registro de una aplicación crea un [identificador de aplicación](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) para la aplicación en el inquilino. 

Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Seleccione **Azure AD B2C** en la lista de servicios de Azure Portal.

2. En la configuración de B2C, haga clic en **Aplicaciones** y luego en **Agregar**.

    Para registrar la aplicación web de ejemplo en el inquilino, utilice la siguiente configuración.

    ![Incorporación de una nueva aplicación](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Mi aplicación web de ejemplo | Escriba un **Nombre** que describa la aplicación a los consumidores. | 
    | **Incluir aplicación web o API web** | Sí | Seleccione **Sí** para una aplicación web. |
    | **Permitir flujo implícito** | Sí | Seleccione **Sí**, ya que la aplicación utiliza el [inicio de sesión con OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de respuesta** | `https://localhost:44316` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. En este tutorial, el ejemplo se ejecuta localmente (localhost) y escucha en el puerto 44316. |
    | **Cliente nativo** | Sin  | Como es una aplicación web y no un cliente nativo, seleccione No. |

3. Haga clic en **Crear** para registrar la aplicación.

Las aplicaciones registradas aparecen en la lista de aplicaciones del inquilino de Azure AD B2C. Seleccione la aplicación web en la lista. Se muestra el panel de propiedades de la aplicación web.

![Propiedades de la aplicación web](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Tome nota del **Id. del cliente de aplicación**. El identificador identifica la aplicación de forma exclusiva y será necesario al configurar la aplicación más adelante en el tutorial.

### <a name="create-a-client-password"></a>Creación de una contraseña de cliente

Azure AD B2C utiliza la autorización de OAuth2 para [aplicaciones cliente](../active-directory/develop/active-directory-dev-glossary.md#client-application). Las aplicaciones web son [clientes confidenciales](../active-directory/develop/active-directory-dev-glossary.md#web-client) y requieren un secreto de cliente (contraseña). Se utilizan el secreto de cliente y el identificador de cliente de aplicación cuando la aplicación web se autentica con Azure Active Directory. 

1. Seleccione la página de claves de la aplicación web registrada y haga clic en **Generar clave**.

2. Haga clic en **Guardar** para mostrar la clave.

    ![Página de claves generales de la aplicación](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

La clave se muestra una vez en el portal. Es importante copiar y guardar el valor de la clave. Se necesita este valor para la configuración de la aplicación. Proteja la clave. No comparta la clave de manera pública.

## <a name="create-policies"></a>Creación de directivas

Una directiva de Azure AD B2C define los flujos de trabajo de usuario. Por ejemplo, el registro, el inicio de sesión, el cambio de contraseñas y la edición de perfiles son flujos de trabajo comunes.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión

Para registrar a usuarios y que estos tengan acceso a la aplicación web, cree una **directiva de registro o de inicio de sesión**.

1. En la página del portal de Azure AD B2C, seleccione **Directivas de inicio de sesión o de registro** y haga clic en **Agregar**.

    Para configurar la directiva, utilice la siguiente configuración:

    ![Incorporación de directiva de registro o de inicio de sesión](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | Escriba un **Nombre** para la directiva. El nombre de directiva tiene como prefijo **b2c_1_**. En el código de ejemplo, se utiliza el nombre de directiva completo **b2c_1_SiUpIn**. | 
    | **Proveedor de identidades** | Registro por correo electrónico | El proveedor de identidades que se usa para identificar al usuario de forma exclusiva. |
    | **Atributos de registro** | Nombre para mostrar y Código postal | Seleccione los atributos que se van a recopilar del usuario durante el registro. |
    | **Notificaciones de la aplicación** | Nombre para mostrar, Código postal, El usuario es nuevo, Identificador de objeto del usuario | Seleccione las [notificaciones](../active-directory/develop/active-directory-dev-glossary.md#claim) que desea incluir en el [token de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token). |

2. Haga clic en **Crear** para crear la directiva. 

### <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil

Para permitir que los usuarios restablezcan la información de su perfil de usuario por su cuenta, cree una **directiva de edición de perfil**.

1. En la página del portal de Azure AD B2C, seleccione **Directivas de edición de perfil** y haga clic en **Agregar**.

    Para configurar la directiva, utilice la siguiente configuración:

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | Escriba un **Nombre** para la directiva. El nombre de directiva tiene como prefijo **b2c_1_**. En el código de ejemplo, se utiliza el nombre de directiva completo **b2c_1_SiPe**. | 
    | **Proveedor de identidades** | Inicio de sesión en una cuenta local | El proveedor de identidades que se usa para identificar al usuario de forma exclusiva. |
    | **Atributos de perfil** | Nombre para mostrar y Código postal | Seleccione los atributos que los usuarios pueden modificar durante la edición de un perfil. |
    | **Notificaciones de la aplicación** | Nombre para mostrar, Código postal, El usuario es nuevo, Identificador de objeto del usuario | Seleccione las [notificaciones](../active-directory/develop/active-directory-dev-glossary.md#claim) que desea incluir en el [token de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token) después de una edición de perfil correcta. |

2. Haga clic en **Crear** para crear la directiva. 

### <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña

Para habilitar en su aplicación el restablecimiento de contraseña, deberá crear una **directiva de restablecimiento de contraseña**. Esta directiva describe la experiencia del consumidor durante el restablecimiento de contraseña y el contenido de los tokens que recibe la aplicación al finalizar correctamente.

1. En la página del portal de Azure AD B2C, seleccione **Directivas de restablecimiento de contraseña** y haga clic en **Agregar**.

    Para configurar la directiva, utilice la siguiente configuración.

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | Escriba un **Nombre** para la directiva. El nombre de directiva tiene como prefijo **b2c_1_**. En el código de ejemplo, se utiliza el nombre de directiva completo **b2c_1_SSPR**. | 
    | **Proveedor de identidades** | Restablecer la contraseña mediante la dirección de correo electrónico | Es el proveedor de identidades que se usa para identificar al usuario de forma exclusiva. |
    | **Notificaciones de la aplicación** | Identificador de objeto del usuario | Seleccione las [notificaciones](../active-directory/develop/active-directory-dev-glossary.md#claim) que desea incluir en el [token de acceso](../active-directory/develop/active-directory-dev-glossary.md#access-token) después de un restablecimiento de contraseña correcto. |

2. Haga clic en **Crear** para crear la directiva. 

## <a name="update-web-app-code"></a>Actualización del código de aplicación web

Ahora que tiene una aplicación web registrada y las directivas creadas, debe configurar la aplicación para usar el inquilino de Azure AD B2C. En este tutorial, configurará una aplicación web de ejemplo. 

[Descargue un archivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

La aplicación web para ASP.NET de ejemplo es una aplicación de lista de tareas sencilla para crear y actualizar una lista de tareas pendientes. La aplicación utiliza [componentes de middleware de Microsoft OWIN](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/) para permitir que los usuarios se registren para usar la aplicación en su inquilino de Azure AD B2C. Al crear una directiva de Azure AD B2C, los usuarios pueden utilizar una cuenta de las redes sociales o crear una cuenta para utilizarla como su identidad al acceder a la aplicación. 

Hay dos proyectos en la solución de ejemplo:

**Aplicación de ejemplo de aplicación web (TaskWebApp)**: aplicación web para crear y editar una lista de tareas. La aplicación web utiliza la directiva de **registro o de inicio de sesión** para registrar a los usuarios o iniciar sesión con ellos mediante una dirección de correo electrónico.

**Aplicación de ejemplo de API web (TaskService)**: API web que admite la funcionalidad de creación, lectura, actualización y eliminación de la lista de tareas. Azure AD B2C protege a la API web y la aplicación web la llama.

Debe cambiar la aplicación para que use el registro de aplicación en el inquilino. También debe configurar las directivas que ha creado. La aplicación web ejemplo define los valores de configuración como configuración de la aplicación en el archivo Web.config. Para cambiar la configuración de la aplicación:

1. Abra la solución **B2C-WebAPI-DotNet** en Visual Studio.

2. En el proyecto de aplicación web **TaskWebApp**, abra el archivo **Web.config** y realice las siguientes actualizaciones:

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    
    <add key="ida:ClientId" value="The Application ID for your web app registered in your tenant" />
    
    <add key="ida:ClientSecret" value="Client password (client secret)" />
    ```
3. Actualice la configuración de directiva con el nombre generado al crear las directivas.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    <add key="ida:EditProfilePolicyId" value="b2c_1_SiPe" />
    <add key="ida:ResetPasswordPolicyId" value="b2c_1_SSPR" />
    ```

## <a name="run-the-sample-web-app"></a>Ejecución de la aplicación web de ejemplo

En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **TaskWebApp** y haga clic en **Establecer como proyecto de inicio**.

Presione **F5** para iniciar la aplicación web. Inicia el explorador predeterminado en la dirección del sitio web local `https://localhost:44316/`. 

La aplicación de ejemplo es compatible con el registro, el inicio de sesión, la edición de un perfil y el restablecimiento de contraseña. A continuación se muestra cómo se registra un usuario para usar la aplicación con una dirección de correo electrónico. Puede probar los demás escenarios por su cuenta.

### <a name="sign-up-using-an-email-address"></a>Registro con una dirección de correo electrónico

1. Haga clic en el vínculo de **registro/inicio de sesión** en el mensaje emergente superior para registrar un usuario de la aplicación web. Esto utiliza la directiva **b2c_1_SiUpIn** definida en un paso anterior.

2. Azure AD B2C presenta una página de inicio de sesión con un vínculo de registro. Como no tiene aún una cuenta, haga clic en el vínculo **Registrarse ahora**. 

3. El flujo de trabajo del registro presenta una página para recopilar y verificar la identidad del usuario con una dirección de correo electrónico. El flujo de trabajo de registro también recopila la contraseña del usuario y los atributos requeridos definidos en la directiva.

    Utilice una dirección de correo electrónico válida y valídela mediante el código de verificación. Establezca una contraseña. Especifique valores para los atributos solicitados. 

    ![Flujo de trabajo de registro](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Haga clic en **Crear** para crear una cuenta local en el inquilino de Azure AD B2C.

Ahora el usuario puede utilizar su dirección de correo electrónico para iniciar sesión y usar la aplicación web.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el inquilino de Azure AD B2C si tiene previsto leer otros tutoriales de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el inquilino de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido a crear a un inquilino de Azure AD B2C, crear directivas y actualizar la aplicación web de ejemplo para usar el inquilino de Azure AD B2C. Prosiga en el siguiente tutorial para aprender a registrar, configurar y llamar a ASP.NET Web API protegido por el inquilino de Azure AD B2C.

> [!div class="nextstepaction"]
> [Uso de Azure Active Directory B2C para proteger ASP.NET Web API](active-directory-b2c-tutorials-web-api.md)
