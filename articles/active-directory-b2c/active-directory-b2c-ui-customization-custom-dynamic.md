---
title: "Azure Active Directory B2C: personalización de la interfaz de usuario de Azure AD B2C dinámicamente utilizando directivas personalizadas"
description: "Admisión de varias experiencias de personalización de marca con contenido HTML5/CSS que cambia dinámicamente en tiempo de ejecución."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: 3a2310ae6266709df6677c55f11b15239c0425a2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: configuración de la interfaz de usuario con contenido dinámico utilizando directivas personalizadas
Mediante el uso de las directivas personalizadas de Azure Active Directory B2C (Azure AD B2C), puede enviar un parámetro en una cadena de consulta. Al pasar dicho parámetro al punto de conexión HTML, puede cambiar de forma dinámica el contenido de la página. Por ejemplo, puede cambiar la imagen de fondo en la página de inicio de sesión o de registro de Azure AD B2C en función de un parámetro que se pasa desde la aplicación web o dispositivo móvil. 

## <a name="prerequisites"></a>requisitos previos
Este artículo se centra en cómo personalizar la interfaz de usuario de Azure AD B2C con *contenido dinámico* utilizando directivas personalizadas. Para empezar, consulte el artículo sobre [personalización de la interfaz de usuario en una directiva personalizada](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>El artículo de Azure AD B2C [Configuración de la interfaz de usuario personalizada en una directiva personalizada](active-directory-b2c-ui-customization-custom.md), explica los fundamentos siguientes:
> * La característica de personalización de la interfaz de usuario de la página.
> * Las herramientas esenciales para probar la característica de personalización de la interfaz de usuario de la página con nuestro contenido de ejemplo.
> * Los principales elementos de interfaz de usuario de cada tipo de página.
> * Procedimientos recomendados para aplicar esta característica.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Incorporación de un vínculo a las plantillas HTML5/CSS al recorrido de usuario

En una directiva personalizada, una definición de contenido define el identificador URI de la página HTML5 que se usa para un paso determinado de la interfaz de usuario (por ejemplo, las páginas de inicio de sesión o de registro). La directiva base define la apariencia y funcionamiento predeterminados apuntando a un identificador URI de archivos HTML5 (en las CSS). En la directiva de extensión, puede modificar la apariencia y funcionamiento reemplazando LoadUri para el archivo HTML5. Las definiciones de contenido incluyen las direcciones URL del contenido externo que se define creando archivos HTML5/CSS según corresponda. 

La sección `ContentDefinitions` contiene una serie de elementos XML `ContentDefinition`. El atributo ID del elemento `ContentDefinition` especifica el tipo de página que se relaciona con la definición de contenido. Es decir, el elemento define el contexto que una plantilla personalizada de HTML5/CSS va a aplicar. En la tabla siguiente se describe el conjunto de identificadores de definición de contenido reconocidos por el motor IEF y el tipo de páginas relacionadas con ellos.

| Id. de definición de contenido | Plantilla HTML5 predeterminada| DESCRIPCIÓN | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de error**. Esta página se muestra cuando se produce una excepción o un error. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de selección del proveedor de identidades**. Esta página contiene una lista de proveedores de identidades entre los que el usuario puede elegir durante el inicio de sesión. Las opciones normalmente son proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selección del proveedor de identidades para el registro**. Esta página contiene una lista de proveedores de identidades entre los que el usuario puede elegir durante el registro. Las opciones son proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de contraseña olvidada**. Esta página contiene un formulario que los usuarios tienen que rellenar para iniciar el restablecimiento de contraseña.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inicio de sesión en una cuenta local**. Esta página contiene un formulario para el inicio de sesión con una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener un cuadro de entrada de texto y un cuadro de entrada de contraseña. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de registro en una cuenta local**. Esta página contiene un formulario para el registro para una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener varios controles de entrada, como un cuadro de entrada de texto, un cuadro de entrada de contraseña, un botón de radio, cuadros desplegables de selección única y casillas de selección múltiple. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticación multifactor**. Esta página permite a los usuarios verificar sus números de teléfono (mediante texto o voz) durante el registro o el inicio de sesión. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de registro en una cuenta social**. Esta página contiene un formulario que los usuarios tienen que rellenar al registrarse con una cuenta existente de un proveedor de identidad social. Esta página es similar a la página anterior de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de actualización de perfil**. Esta página contiene un formulario que los usuarios pueden usar para actualizar su perfil. Esta página es similar a la página de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inicio de sesión o registro unificada**. Esta página controla los procesos de registro y de inicio de sesión del usuario. Los usuarios pueden utilizar proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales.  |

## <a name="serving-dynamic-content"></a>Entrega de contenido dinámico
En el artículo [Configuración de la interfaz de usuario personalizada en una directiva personalizada](active-directory-b2c-ui-customization-custom.md) se cargaron los archivos HTML5 en Azure Blob Storage. Esos archivos HTML5 son estáticos y representan el mismo contenido HTML para cada solicitud. 

En este artículo, se usa una aplicación web de ASP.NET que puede aceptar parámetros en la cadena de consulta y responder según corresponda. 

En este tutorial realizará lo siguiente:
* Creación de una aplicación web de ASP.NET Core que hospeda las plantillas HTML5. 
* Incorporación de una plantilla HTML5 personalizada _unified.cshtml_. 
* Publicación de la aplicación web en Azure App Service. 
* Establecimiento de uso compartido de recursos entre orígenes (CORS) para la aplicación web.
* Invalidación de los elementos `LoadUri` para que apunten al archivo HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Paso 1: Creación de una aplicación web de ASP.NET

1. Cree un proyecto en Visual Studio, para lo que debe seleccionar **Archivo** >  **Nuevo** > **Proyecto**.

2. En la ventana **Nuevo proyecto**, seleccione **Visual C#** > **Web** > **Aplicación web ASP.NET Core (.NET Core)**.

3. Asigne un nombre a la aplicación, por ejemplo, *Contoso.AADB2C.UI* y, a continuación, seleccione **Aceptar**.

    ![Creación de un nuevo proyecto de Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Seleccione la plantilla **Aplicación web**.

5. Establezca la autenticación en **Sin autenticación**.

    ![Seleccione la plantilla Aplicación Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Haga clic en **Aceptar** para crear el proyecto.

## <a name="step-2-create-mvc-view"></a>Paso 2: Creación de la vista de MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Paso 2.1: Descarga de la plantilla HTML5 integrada de B2C
La plantilla HTML5 personalizada se basa en la plantilla HTML5 integrada de Azure AD B2C. Puede descargar el archivo [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) o descargar la plantilla desde el [paquete de iniciación](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Use este archivo HTML5 para crear una página de inicio de sesión o de registro unificada.

### <a name="step-22-add-the-mvc-view"></a>Paso 2.2: Incorporación de la vista de MVC
1. Haga clic con el botón derecho en la carpeta Vistas/Inicio y, a continuación, en **Agregar** > **Nuevo elemento**.

    ![Agregar nuevo elemento MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. En la ventana **Agregar nuevo elemento - Contoso.AADB2C.UI**, seleccione **Web > ASP.NET**.

3. Seleccione **Página de vistas de MVC**.

4. En el cuadro de texto **Nombre**, cambie el nombre a **unified.cshtml**.

5. Seleccione **Agregar**.

    ![Agregar la vista de MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Si el archivo *unified.cshtml* no está ya abierto, haga doble clic en el archivo para abrirlo y, a continuación, borre el contenido del archivo.

7. En este tutorial, vamos a eliminar la referencia a la página de diseño. Agregue el siguiente fragmento de código al archivo _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Abra el archivo _unified.cshtml_ que descargó de la plantilla HTML5 integrada de Azure AD B2C.

9. Reemplace el signo de arroba simple (@) con un doble signo de arroba (@@).

10. Copie el contenido del archivo y péguelo debajo de la definición de Layout. El código debe ser similar a:

    ![Archivo unified.cshtml después de agregar el HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Paso 2.3: Cambio de la imagen de fondo

Ubique el elemento `<img>` que contiene el valor de `ID` *background_background_image*y reemplace el valor `src` con **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** o cualquier otra imagen de fondo que desee utilizar.

![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Paso 2.4: Incorporación de la vista al controlador MVC

1. Abra **Controllers\HomeController.cs** y agregue el siguiente método: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Este código especifica que el método debe utilizar un archivo de plantilla *View* para presentar una respuesta al explorador. Dado que no se especifica explícitamente el nombre del archivo de la plantilla *View*, MVC usa de forma predeterminada el archivo de vista _unified.cshtml_ en la carpeta */Views/Home*.
    
    Después de agregar el método _unificado_, el código debe ser similar a:
    
    ![Cambiar el controlador para representar la vista](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Depure la aplicación web y asegúrese de que la página _unified_ es accesible (por ejemplo, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Paso 2.5: Publicación en Azure
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.UI** y seleccione **Publicar**.

    ![Publicación en Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Seleccione el elemento **Microsoft Azure App Service** y luego **Publicar**.

    ![Creación de nueva aplicación en Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Se abre la ventana **Crear servicio de aplicaciones**. En ella puede empezar a crear todos los recursos de Azure necesarios para ejecutar la aplicación web ASP.NET en Azure.

    > [!NOTE]
    > Para más información sobre la publicación, consulte [Creación de una aplicación web ASP.NET en Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. En el cuadro **Nombre de la aplicación web**, escriba un nombre único de aplicación, los caracteres válidos son a-z, A-Z, 0-9 y el guión(-). La dirección URL de la aplicación web es `http://<app_name>.azurewebsites.NET`, donde `<app_name>` es el nombre de la aplicación web. Puede aceptar el nombre generado automáticamente, que es único.

4. Seleccione **Crear** para comenzar a crear los recursos de Azure.

    ![Entrada de las propiedades de App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Una vez completado el proceso de creación, el asistente publica la aplicación web ASP.NET en Azure y, a continuación, inicia la aplicación en el explorador predeterminado.

5. Copie la dirección URL de la página _unificada_, por ejemplo, _https://<app_name>.azurewebsites.net/home/unified_.

## <a name="step-3-configure-cors-in-azure-app-service"></a>Paso 3: Configuración de CORS en Azure App Service
1. En [Azure Portal](https://portal.azure.com/), seleccione **App Services** y, después, el nombre de la aplicación de API.

    ![Seleccionar la aplicación de API en Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. En la sección **Configuración**, en **API**, seleccione **CORS**.

    ![Selección de la configuración de CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. En la ventana **CORS** en el cuadro **Orígenes permitidos**, realice una de las siguientes acciones:

    * Escriba las direcciones URL desde las que desea permitir que procedan las llamadas de JavaScript.
    * También puede escribir un asterisco (*) para especificar que se aceptan todos los dominios de origen.

4. Seleccione **Guardar**.

    ![La ventana CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Tras seleccionar **Guardar**, la aplicación de API aceptará llamadas de JavaScript desde las direcciones URL especificadas. 

## <a name="step-4-html5-template-validation"></a>Paso 4: Validación de la plantilla HTML5
La plantilla HTML5 está lista para usarse. Sin embargo, no está disponible en el código `ContentDefinition`. Para poder agregar `ContentDefinition` a la directiva personalizada, asegúrese de que:
* El contenido sea accesible y compatible con HTML5.
* El servidor de contenido esté habilitado para CORS.

    >[!NOTE]
    >Para comprobar que el sitio donde va a albergar el contenido ha habilitado CORS y puede probar las solicitudes CORS, vaya al sitio web [test-cors.org](http://test-cors.org/). 

* El contenido servido es seguro sobre **HTTPS**.
* Está usando *direcciones URL absolutas* como *https://yourdomain/content* para todos los vínculos, el contenido de CSS y las imágenes.

## <a name="step-5-configure-your-content-definition"></a>Paso 5: Configuración de la definición de contenido
Para configurar `ContentDefinition` haga lo siguiente:
1. Abra el archivo base de la directiva (por ejemplo, *TrustFrameworkBase.xml*).

2. Busque el elemento `<ContentDefinitions>` y, a continuación, copie todo el contenido del nodo `<ContentDefinitions>`.

3. Abra el archivo de extensión (por ejemplo, *TrustFrameworkExtensions.xml*) y busque el elemento `<BuildingBlocks>`. Si el elemento no existe, agréguelo.

4. Pegue el contenido completo del nodo `<ContentDefinitions>` que copió como elemento secundario del elemento `<BuildingBlocks>`. 

5. Busque el nodo `<ContentDefinition>` que contenga `Id="api.signuporsignin"` en el XML que ha copiado.

6. Cambie el valor de `LoadUri` de _~/tenant/default/unified_ a _https://<app_name>.azurewebsites.net/home/unified_.  
    La directiva personalizada debería ser similar a la siguiente:
    
    ![La definición de contenido](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Paso 6: Carga de la directiva en el inquilino
1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y seleccione **Azure AD B2C**.

2. Seleccione **Marco de experiencia de identidad**.

3. Seleccione **Todas las directivas**.

4. Seleccione **Cargar directiva**.

5. Active la casilla **Sobrescribir la directiva si existe**.

6. Cargue el archivo *TrustFrameworkExtensions.xml* y asegúrese de que pasa la validación.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Paso 7: Prueba de la directiva personalizada con Ejecutar ahora
1. Seleccione **Configuración de Azure AD B2C** y, después, **Marco de experiencia de identidad**.

    >[!NOTE]
    >La función Ejecutar ahora requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.  
    Debe poder ver el HTML5 personalizado con el fondo que creó anteriormente.

    ![Directiva de registro o de inicio de sesión](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Paso 8: Incorporación de contenido dinámico
Cambie el fondo en función del parámetro de la cadena de consulta _campaignId_. La aplicación de usuario de confianza (aplicaciones web y móviles) envía el parámetro a Azure AD B2C. La directiva lee el parámetro y envía su valor a la plantilla HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Paso 8.1: Incorporación del parámetro de definición de contenido

Agregue el elemento `ContentDefinitionParameters` haciendo lo siguiente:
1. Abra el archivo *SignUpOrSignin* de la directiva (por ejemplo, *SignUpOrSignin.xml*).

2. Busque el nodo `<DefaultUserJourney>`. 

3. En el nodo `<DefaultUserJourney>`, agregue el siguiente fragmento de código XML:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Paso 8.2: Cambio del código para aceptar parámetros de la cadena de consulta y reemplazo de la imagen de fondo
Modifique el método `unified` de HomeController para aceptar el parámetro campaignId. A continuación, el método comprueba el valor del parámetro y establece la variable `ViewData["background"]` según corresponda.

1. Abra el archivo *Controllers\HomeController.cs* y cambie el método `unified` agregando el siguiente fragmento de código:

    ```csharp
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

2. Busque el elemento `<img>` con identificador `background_background_image` y reemplace el valor de `src` por `@ViewData["background"]`.

    ![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: Carga de los cambios y publicación de la directiva
1. Publique el proyecto de Visual Studio en Azure App Service.

2. Cargue la directiva *SignUpOrSignin.xml* en Azure AD B2C.

3. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.  
    Debería ver la misma imagen de fondo que se mostró anteriormente.

4. Copie la dirección URL de la barra de direcciones del explorador.

5. Agregue el parámetro de la cadena de consulta _campaignId_ al identificador URI. Por ejemplo, agregue `&campaignId=hawaii` tal y como se muestra en la siguiente imagen:

    ![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Seleccione **ENTRAR** para mostrar la imagen de fondo de Hawai.

    ![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Cambie el valor a *Tokyo* y seleccione **Entrar**.  
    El explorador muestra el fondo de Tokio.

    ![Cambio del fondo de la página](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Paso 9: Cambio del resto del recorrido del usuario
Si selecciona el vínculo **Regístrese ahora** en la página de inicio de sesión, el explorador muestra la imagen de fondo predeterminada, no la imagen que ha definido. Este comportamiento se produce porque ha cambiado la página de registro o de inicio de sesión. Para cambiar el resto de las definiciones de contenido de autoaserción:
1. Vuelva al "Paso 2" y haga lo siguiente:

    a. Descargue el archivo *selfasserted*.

    b. Copie el contenido del archivo.

    c. Cree una nueva vista *selfasserted*.

    d. Agregue *selfasserted* al controlador **Home**.

2. Vuelva al "Paso 4" y haga lo siguiente: 

    a. En la directiva de extensión, busque el nodo `<ContentDefinition>` que incluye `Id="api.selfasserted"`, `Id="api.localaccountsignup"` y `Id="api.localaccountpasswordreset"`.

    b. Establezca el atributo `LoadUri` en el URI de *selfasserted*.

3. Vuelva a "Paso 8.2" y cambie el código para aceptar parámetros de cadena de consulta, pero esta vez para la función *selfasserted*. 

4. Cargue la directiva *TrustFrameworkExtensions.xml* y asegúrese de que pasa la validación.

5. Ejecute la prueba de la directiva y haga seleccione **Registrarse ahora** para ver el resultado.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descarga de los archivos y código de la directiva completos
* Una vez completado el tutorial [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md), le recomendamos que compile su escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization) para que los tenga como referencia.
* El código completo se puede descargar en [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




