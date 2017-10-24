---
title: "Azure Active Directory B2C: personalización de la interfaz de usuario (UI) de Azure AD B2C dinámicamente con directivas personalizadas"
description: "Cómo admitir varias experiencias de personalización de marca con contenido HTML/CSS que cambia dinámicamente en tiempo de ejecución"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C: Configuración de la interfaz de usuario con contenido dinámico con directivas personalizadas
Las directivas personalizadas de Azure AD B2C le permiten enviar un parámetro en una cadena de consulta. Dicho parámetro se pasa al punto de conexión HTML y puede cambiar dinámicamente el contenido de la página. Por ejemplo, puede cambiar la imagen de fondo del inicio de sesión o del registro en B2C en función de un parámetro que se pasa desde la aplicación web o dispositivo móvil. 

## <a name="prerequisites"></a>Requisitos previos
Este artículo se centra en cómo personalizar la interfaz de usuario de Azure AD B2C con **contenido dinámico** mediante directivas personalizadas. Para empezar a trabajar con la personalización de la interfaz de usuario mediante directiva personalizada, consulte el artículo [Personalización de la interfaz de usuario mediante una directiva personalizada](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>
>  El artículo [Configuración de la personalización de la interfaz de usuario mediante una directiva personalizada](active-directory-b2c-ui-customization-custom.md) explica los conceptos básicos de personalización de la interfaz de usuario de Azure AD B2C con una directiva personalizada:
> * La característica de personalización de la interfaz de usuario de la página.
> * Una herramienta que le ayude a probar la característica de personalización de la interfaz de usuario de la página con nuestro contenido de ejemplo.
> * Los elementos de interfaz de usuario principales en cada tipo de página.
> * Prácticas recomendadas al usar esta característica.

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>Adición de un vínculo a las plantillas HTML5/CSS al recorrido del usuario

En una directiva personalizada, una definición de contenido define el identificador URI de la página HTML5 que se usa para un paso determinado de la interfaz de usuario. Por ejemplo, las páginas de inicio de sesión o de registro. La directiva base define la apariencia predeterminada apuntando a un identificador URI de archivos HTML5 (hace referencia a su CSS). En la directiva de extensión, puede modificar la apariencia y comportamiento reemplazando LoadUri para ese archivo HTML5. Por lo tanto, las definiciones de contenido contienen las direcciones URL del contenido externo que se define creando archivos HTML5/CSS según corresponda. 

La sección `ContentDefinitions` contiene una serie de elementos XML `ContentDefinition`. El atributo ID del elemento `ContentDefinition` especifica el tipo de páginas relacionadas con la definición de contenido. Por lo tanto el contexto en el que se va a usar una plantilla personalizada de HTML5/CSS. En la tabla siguiente se describe el conjunto de identificadores de definición de contenido reconocidos por el motor IEF y el tipo de páginas relacionadas con ellos.

| Id. de definición de contenido | Plantilla HTML5 predeterminada| Descripción | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de error**. Esta página se muestra cuando se produce una excepción o un error. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de selección del proveedor de identidades**. Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión. Estas opciones normalmente son proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selección del proveedor de identidades para el registro**. Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión. Estas opciones son proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de contraseña olvidada**. Esta página contiene un formulario que el usuario tiene que rellenar para iniciar el restablecimiento de contraseña.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inicio de sesión en una cuenta local**. Esta página contiene un formulario de registro con una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener un cuadro de entrada de texto y un cuadro de entrada de contraseña. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de registro en una cuenta local**. Esta página contiene un formulario de registro para una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener varios controles de entrada, como un cuadro de entrada de texto, un cuadro de entrada de contraseña, un botón de radio, cuadros desplegables de selección única y casillas de selección múltiple. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticación multifactor**. Esta página permite a los usuarios verificar sus números de teléfono (mediante texto o voz) durante el registro o el inicio de sesión. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de registro en una cuenta social**. Esta página contiene un formulario de registro que los usuarios tienen que rellenar al registrarse con una cuenta existente de un proveedor de identidades social. Esta página es similar a la página anterior de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de actualización de perfil**. Esta página contiene un formulario que los usuarios pueden usar para actualizar su perfil. Esta página es similar a la página de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inicio de sesión o registro unificada**. Esta página controla tanto el registro como el inicio de sesión de los usuarios. Los usuarios pueden utilizar proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales.  |

## <a name="serving-dynamic-content"></a>Entrega de contenido dinámico
En el artículo anterior [Configuración de la personalización de la interfaz de usuario mediante una directiva personalizada](active-directory-b2c-ui-customization-custom.md) se cargaron los archivos HTML5 a Azure Blob Storage. Esos archivos HTML5 son estáticos y representan el mismo contenido HTML para cada solicitud. En este artículo, usamos una aplicación web de ASP.Net que puede aceptar parámetros en la cadena de consulta y responder según corresponda. En este tutorial realizará lo siguiente:
* Creación de una aplicación web de ASP.NET Core que hospeda la plantilla HTML5 
* Adición de la plantilla HTML5 personalizada _unified.cshtml_ 
* Publicación de la aplicación web en Azure App Service 
* Configuración de CORS para la aplicación web
* Invalidación de los elementos `LoadUri` para que apunten al archivo HTML5

## <a name="step-1-create-an-aspnet-web-app"></a>Paso 1: Creación de una aplicación web de ASP.NET

1.  Cree un proyecto nuevo en Visual Studio seleccionando **Archivo > Nuevo > Proyecto**.
2.  En el cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C# > Web > Aplicación web ASP.NET Core (.NET Core)**.
3.  Llame a la aplicación, por ejemplo, Contoso.AADB2C.UI y, a continuación, seleccione **Aceptar**.

    ![Creación de un nuevo proyecto de Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  Seleccione la plantilla **Aplicación Web**
5.  Asegúrese de establecer la autenticación en **Sin autenticación**.

    ![Selección de la plantilla API Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  Haga clic en **Aceptar** para crear el proyecto.

## <a name="step-2-create-mvc-view"></a>Paso 2: Creación de la vista de MVC
### <a name="step-21-download-b2c-built-in-html5-template"></a>Paso 2.1: Descarga de la plantilla HTML5 integrada de B2C
La plantilla HTML5 personalizada se basa en la plantilla HTML5 integrada de B2C. Puede descargar el archivo [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) o descargarlo desde el [paquete de iniciación](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Use este archivo HTML5 para una página de inicio de sesión o de registro unificada.

### <a name="step-22-add-mvc-view"></a>Paso 2.2: Adición de la vista de MVC
1. Haga clic con el botón derecho en la carpeta Vistas/Inicio y, a continuación, en **Agregar > Nuevo elemento**.
 ![Agregar nuevo elemento MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. En el cuadro de diálogo **Agregar nuevo elemento - Contoso.AADB2C.UI**, seleccione **Web > ASP > NET**
3. Pulse **Página de vista de MVC**
4. En el cuadro de texto **Nombre**, cambie el nombre a _unified.cshtml_.
5. Haga clic en **Agregar**
 ![Agregar vista de MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)
6.  Si este archivo no está abierto, haga doble clic en el archivo para abrirlo. Borre el contenido del archivo _unified.cshtml_.
7. Con fines de demostración, eliminamos la referencia a la página de diseño. Agregue el siguiente fragmento de código al archivo _unified.cshtml_

    ```C#
    @{
        Layout = null;
    }
    ```

8. Abra el archivo _unified.cshtml_ que descargó de la plantilla HTML5 integrada de AAD B2C.
9. Busque los símbolos `@` y reemplácelos por `@@`
10. Copie el contenido del archivo y péguelo debajo de la definición de Layout. El código debe ser similar a: ![archivo unified.cshtml después de agregar el código HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>Paso 2.3: Cambio de la imagen de fondo
10. Busque el elemento `<img>` con ID `background_background_image` y reemplace el valor de `src` por _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ u otra imagen de fondo de su elección.
![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>Paso 2.4: Adición de la vista al controlador MVC
Abra **Controllers\HomeController.cs** y agregue el siguiente método. 
```C
public IActionResult unified()
{
    return View();
}
```
Este código especifica que el método debe utilizar un archivo de plantilla de vista para presentar una respuesta al explorador. Dado que no se especifica explícitamente el nombre del archivo de la plantilla de vista, MVC usa de forma predeterminada el archivo de vista _unified.cshtml_ en la carpeta /Views/Home.

Después de agregar el método _unified_, el código debe ser similar a: ![Cambio al controlador para representar la vista](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

Depure la aplicación web y asegúrese de que la página _unified_ es accesible. Por ejemplo: `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>Paso 2.5: Publicación en Azure
1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.UI** y seleccione **Publicar**.

    ![Publicación en Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  Asegúrese de que **Microsoft Azure App Service** está seleccionado y seleccione **Publicar**.

    ![Creación de nueva aplicación en Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    El cuadro de diálogo **Crear App Service** le ayuda a crear todos los recursos de Azure necesarios para ejecutar la aplicación web de ASP.NET en Azure.

> [!NOTE]
>
>Para más información sobre la publicación, consulte [Creación de una aplicación web de ASP.NET en Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3.  En **Nombre de la aplicación web**, escriba un nombre único de aplicación (los caracteres válidos son `a-z`, `0-9` y `-`). La dirección URL de la aplicación web es `http://<app_name>.azurewebsites.NET`, donde `<app_name>` es el nombre de la aplicación web. Puede aceptar el nombre generado automáticamente, que es único.

    Seleccione **Crear** para comenzar a crear los recursos de Azure.

    ![Entrada de las propiedades de App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  Una vez finalizado el asistente, publica la aplicación web ASP.NET en Azure y, a continuación, inicia la aplicación en el explorador predeterminado; a continuación, copie la dirección URL de la página _unified_. Por ejemplo, _https://<app_name>.azurewebsites.net/home/unified_

## <a name="step-3-configure-cors-in-azure-app-service"></a>Paso 3: Configuración de CORS en Azure App Service
1. En el explorador, vaya a [Azure Portal](https://portal.azure.com/)

2. Haga clic en **Servicios de aplicaciones**y en el nombre de la aplicación de API.

    ![Seleccione la aplicación de API en el portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. En la sección **Configuración**, desplácese hacia abajo y busque la sección **API** y, a continuación, haga clic en **CORS**.

    ![Selección de la configuración de CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. En el cuadro de texto, escriba las direcciones URL desde las que desea permitir que procedan las llamadas de JavaScript.
También puede escribir un asterisco (*) para especificar que se aceptan todos los dominios de origen.

5. Haga clic en **Guardar**.

    ![Haga clic en Guardar](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

Tras hacer clic en Guardar, la aplicación de API aceptará llamadas de JavaScript desde las direcciones URL especificadas. 

## <a name="step-4-html5-template-validation"></a>Paso 4: Validación de la plantilla HTML5
La plantilla HTML5 está lista para usarse.  Sin embargo, no está disponible en ninguno de los elementos `ContentDefinition`. Antes de agregar el elemento `ContentDefinition` a la directiva personalizada, debe:
* Asegurarse de que el contenido sea accesible y compatible con HTML5.
* Asegurarse de que el servidor de contenido esté habilitado para CORS.

>[!NOTE]
>
>Para comprobar que el sitio en el que hospeda su contenido tiene habilitado CORS y probar las solicitudes CORS, puede usar el sitio http://test-cors.org/. 

* El contenido servido es seguro sobre **HTTPS**.
* Usar **direcciones URL absolutas** como https://yourdomain/content para todos los vínculos, el contenido de CSS y las imágenes.

## <a name="step-5-configure-your-content-definition"></a>Paso 5: Configuración de la definición de contenido
Para configurar `ContentDefinition`
1.  Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2.  Busque el elemento `<ContentDefinitions>` y copie el contenido entero del nodo `<ContentDefinitions>`.
3.  Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<BuildingBlocks>`. Si el elemento no existe, agréguelo.
4.  Pegue el contenido entero del nodo `<ContentDefinitions>` que copió como secundario del elemento `<BuildingBlocks>`. 
5.  Busque el nodo `<ContentDefinition>` que incluye `Id="api.signuporsignin"` en el código XML que ha copiado.
6.  Cambie el valor de `LoadUri` de _~/tenant/default/unified_ a _https://app_name.azurewebsites.net/home/unified_ La directiva personalizada debe ser similar a: ![definición de contenido](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Paso 6: Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Abra **Todas las directivas**.
4.  Seleccione **Cargar directiva**.
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Paso 7: Prueba de la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

    >[!NOTE]
    >
    >    **Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. 
    >    Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.
    >

2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debe poder ver el HTML5 personalizado con el fondo que creó anteriormente ![Directiva de registro o de inicio de sesión](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>Paso 8: Adición de contenido dinámico
En esta sección, cambiamos el fondo en función del parámetro de la cadena de consulta _campaignId_. La aplicación de usuario de confianza (aplicaciones web y móviles) envía el parámetro a AAD B2C. La directiva lee el parámetro y envía su valor a la plantilla HTML5 


### <a name="step-81-adding-content-definition-parameter"></a>Paso 8.1: Adición del parámetro de definición de contenido

Para agregar el elemento `ContentDefinitionParameters`:
1.  Abra el archivo SignUpOrSignin de la directiva (por ejemplo, SignUpOrSignin.xml).
2.  Busque el nodo `<UserJourneyBehaviors>` 
4.  Agregue el siguiente fragmento de código XML en el nodo `<UserJourneyBehaviors>` 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>Paso 8.2: Cambio del código para aceptar parámetros de la cadena de consulta y reemplazo de la imagen de fondo en consecuencia
En esta sección, se modifica el método _unified_ de HomeController para aceptar el parámetro campaignId. A continuación, el método comprueba su valor y establece la variable `ViewData["background"]` según corresponda.

1. Abra **Controllers\HomeController.cs** y cambie el método `unified` con el siguiente fragmento de código:

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Busque el elemento `<img>` con ID `background_background_image` y reemplace el valor de `src` por `@ViewData["background"]`.

    ![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3 Carga de los cambios y publicación de la directiva
1. Publique el proyecto de Visual studio en Azure App Service
2. Cargue la directiva SignUporSignIn.xml en AAD B2C
3.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**. Debería ver la misma imagen de fondo que antes.
4. Copie la dirección URL de la barra de direcciones del explorador
5. Agregue el parámetro de la cadena de consulta _campaignId_ al identificador URI. Por ejemplo, agregue `&campaignId=hawaii`, tal y como se muestra en la siguiente imagen ![Cambio del fondo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. Presione **ENTRAR** y la página presentará el fondo de Hawaii ![Cambio del fondo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. Ahora, cambie el valor a *Tokyo* y presione **ENTRAR**. La página presenta el fondo de Tokyo ![Cambio del fondo de página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Paso 9: Cambio del resto del recorrido del usuario
Si navega a la página de inicio de sesión y hace clic en el vínculo **Regístrese ahora**, verá el fondo predeterminado, no el que ha definido. Este comportamiento es debido a que solo cambió la página de registro o inicio de sesión, sin embargo, debe cambiar también el resto de las definiciones de contenido de autoaserción. Para ello, siga los pasos:
* En el paso 2:
    * Descargue el archivo **selfasserted**.
    * Copie el contenido del archivo.
    * Cree una nueva vista **selfasserted**.
    * Agregue **selfasserted** al controlador **Home**.
* En el paso 4 
    * En la directiva de extensión, busque el nodo `<ContentDefinition>` que incluye `Id="api.selfasserted"`, `Id="api.localaccountsignup"` y `Id="api.localaccountpasswordreset"`
    *  Establezca el atributo `LoadUri` en el URI de autoaserción.
* En el paso 8.2, cambie el código para aceptar parámetros en la cadena de consulta 
* Cargue la directiva TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.
* Ejecute la prueba de la directiva y haga clic en **Regístrese ahora** para ver el resultado

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Descarga de los archivos y código de la directiva completos
* Se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* Puede descargar aquí el código completo [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)




