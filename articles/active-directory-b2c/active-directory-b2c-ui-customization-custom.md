---
title: "Personalización de la interfaz de usuario con directivas personalizadas - Azure AD B2C | Microsoft Docs"
description: "Información acerca de cómo personalizar una interfaz de usuario (IU) con directivas personalizadas en Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: c430b488016f038ed1d7a67a8d52c057df1ea40e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: configuración de la interfaz de usuario personalizada en una directiva personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Después de completar este artículo, tendrá una directiva personalizada de registro e inicio de sesión con su marca y apariencia. Con Azure Active Directory B2C (Azure AD B2C), controlará prácticamente todo el contenido HTML y CSS que se presenta a los usuarios. Cuando se usa una directiva personalizada, la personalización de la interfaz del usuario se configura en XML en lugar de con controles de Azure Portal. 

## <a name="prerequisites"></a>Requisitos previos

Antes de continuar, lea el artículo de [introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md). Debe tener una directiva personalizada activa para registrar e iniciar sesión de cuentas locales.

## <a name="page-ui-customization"></a>Personalización de la interfaz de usuario de la página

La característica de personalización de la interfaz de usuario de la página sirve para personalizar la apariencia y la experiencia de cualquier directiva personalizada. También puede mantener la coherencia visual y de la marca entre la aplicación y Azure AD B2C.

Funciona de la siguiente manera: Azure AD B2C ejecuta código en el explorador del consumidor y usa un enfoque moderno denominado [Uso compartido de recursos entre orígenes (CORS)](http://www.w3.org/TR/cors/). En primer lugar, especifique una dirección URL en la directiva personalizada con contenido HTML personalizado. Azure AD B2C combina elementos de la interfaz de usuario con el contenido HTML cargado desde la dirección URL y muestra la página al consumidor.

## <a name="create-your-html5-content"></a>Creación de contenido HTML5

Puede crear contenido HTML con el nombre de la marca del producto en el título.

1. Copie el siguiente fragmento de código HTML. Es HTML5 bien formado con un elemento vacío denominado *\<div id="api"\>\</div\>* situado en las etiquetas *\<body\>*. Este elemento indica dónde se va a insertar el contenido de Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

   >[!NOTE]
   >Por motivos de seguridad, el uso de JavaScript está bloqueado para la personalización.

2. Pegue el fragmento de código copiado en un editor de texto y guarde el archivo como *customize-ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Creación de una cuenta de Almacenamiento de blobs de Azure

>[!NOTE]
> En este artículo se usa Azure Blob Storage para hospedar el contenido. Puede elegir hospedar el contenido en un servidor web, pero deberá [habilitar CORS en el servidor web](https://enable-cors.org/server.html).

Para hospedar este contenido HTML en Blob Storage, haga lo siguiente:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú **central**, seleccione **Nuevo** > **Almacenamiento** > **Cuenta de almacenamiento**.
3. Escriba un **Nombre** único para la cuenta de almacenamiento.
4. El **Modelo de implementación** puede permanecer como **Resource Manager**.
5. Cambie **Tipo de cuenta** a **Almacenamiento de blobs**.
6. El **Rendimiento** puede permanecer como **Estándar**.
7. La **Replicación** puede permanecer como **RA-GRS**.
8. El **Nivel de acceso** puede permanecer **Activo**.
9. El **Cifrado del servicio de almacenamiento** puede permanecer **Deshabilitado**.
10. Seleccione una **Suscripción** para la cuenta de almacenamiento.
11. Cree un **Grupo de recursos** o seleccione uno existente.
12. Seleccione la **Ubicación geográfica** de la cuenta de almacenamiento.
13. Haga clic en **Crear** para crear la cuenta de almacenamiento.  
    Espere que termine la implementación y la hoja de la **Cuenta de almacenamiento** se abrirá automáticamente.

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor público en Blob Storage, haga lo siguiente:

1. Haga clic en la pestaña **Introducción**.
2. Haga clic en **Contenedor**.
3. En **Nombre**, escriba **$root**.
4. Establezca **Tipo de acceso** en **Blob**.
5. Haga clic en **$root** para abrir el contenedor nuevo.
6. Haga clic en **Cargar**.
7. Haga clic en el icono de carpeta junto a **Seleccione un archivo**.
8. Vaya al blob **customize-ui.html** que creó en la sección [Personalización de la interfaz de usuario de la página](#the-page-ui-customization-feature).
9. Haga clic en **Cargar**.
10. Seleccione el blob customize-ui.html que cargara.
11. Junto a **URL**, haga clic en **Copiar**.
12. En un explorador, pegue la dirección URL copiada y vaya al sitio. Si no puede acceder, asegúrese de que el tipo de acceso de contenedor está establecido en **blob**.

## <a name="configure-cors"></a>Configuración de CORS

Para configurar Blob Storage para Uso compartido de recursos entre orígenes (CORS), haga lo siguiente:

>[!NOTE]
>¿Desea probar la característica de personalización de la interfaz de usuario con nuestro contenido HTML y CSS de ejemplo? Proporcionamos [una herramienta auxiliar sencilla](active-directory-b2c-reference-ui-customization-helper-tool.md) que carga y configura el contenido de ejemplo en la cuenta de Blob Storage. Si usa la herramienta, vaya directamente a [Modificación de la directiva de inicio de sesión o de registro](#modify-your-sign-up-or-sign-in-custom-policy).

1. Abra **CORS** en **Configuración** (hoja **Almacenamiento**).
2. Haga clic en **Agregar**.
3. Para los **Orígenes permitidos**, escriba un asterisco (\*).
4. En la lista desplegable de **Verbos permitidos**, seleccione **GET** y **OPTIONS**.
5. Para los **Encabezados permitidos**, escriba un asterisco (\*).
6. Para los **Encabezados expuestos**, escriba un asterisco (\*).
7. Para **Antigüedad máxima (segundos)**, escriba **200**.
8. Haga clic en **Agregar**.

## <a name="test-cors"></a>Prueba de CORS

Para comprobar que está listo, haga lo siguiente:

1. Vaya al sitio web [test-cors.org](http://test-cors.org/) y pegue la dirección URL en el cuadro **Remote URL** (Dirección URL remota).
2. Haga clic en **Send Request** (Enviar solicitud).  
    Si recibe un error, asegúrese de que la [configuración de CORS](#configure-cors) sea correcta. Puede que también deba borrar la caché del explorador o abrir una sesión de navegación privada, para ello, presione Ctrl + Mayús + P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificación de la directiva de inicio de sesión o de registro

En la etiqueta *\<TrustFrameworkPolicy\>* superior, debería encontrar la etiqueta *\<BuildingBlocks\>*. Dentro de las etiquetas *\<BuildingBlocks\>*, agregue una etiqueta *\<ContentDefinitions\>*; para ello, copie el ejemplo siguiente. Reemplace *your_storage_account* por el nombre de la cuenta de almacenamiento.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Carga de la directiva personalizada actualizada

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2. Haga clic en **Todas las directivas**.
3. Haga clic en **Cargar directiva**.
4. Cargue `SignUpOrSignin.xml` con la etiqueta *\<ContentDefinitions\>* que agregó anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Prueba de la directiva personalizada con **Ejecutar ahora**

1. En la hoja **Azure AD B2C**, vaya a **Todas las directivas**.
2. Seleccione la directiva personalizada que cargó y, luego, haga clic en el botón **Ejecutar ahora**.
3. Debería poder registrarse con una dirección de correo electrónico.

## <a name="reference"></a>Referencia

Aquí encontrará plantillas de ejemplo de personalización de interfaz de usuario:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

La carpeta sample_templates/wingtip contiene los siguientes archivos HTML:

| Plantilla HTML5 | Descripción |
|----------------|-------------|
| *phonefactor.html* | Use este archivo como plantilla para una página de autenticación multifactor. |
| *resetpassword.html* | Use este archivo como plantilla para una página de recuperación de contraseña. |
| *selfasserted.html* | Use este archivo como plantilla para una página de registro en una cuenta social, una página de registro en una cuenta local o una página de inicio de sesión en una cuenta local. |
| *unified.html* | Use este archivo como plantilla para una página de inicio de sesión o registro unificada. |
| *updateprofile.html* | Use este archivo como plantilla para una página de actualización de perfil. |

En la sección [Modificación de la directiva de inicio de sesión o de registro](#modify-your-sign-up-or-sign-in-custom-policy) configuró la definición del contenido de `api.idpselections`. Todos los identificadores de definición de contenido que se reconocen en el marco de la experiencia de identidad de Azure AD B2C y sus descripciones aparecen en la tabla siguiente:

| Id. de definición de contenido | Descripción | 
|-----------------------|-------------|
| *api.error* | **Página de error**. Esta página se muestra cuando se produce una excepción o un error. |
| *api.idpselections* | **Página de selección del proveedor de identidades**. Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión. Estas opciones son proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales. |
| *api.idpselections.signup* | **Selección del proveedor de identidades para el registro**. Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión. Estas opciones son proveedores de identidades de empresa, proveedores de identidades sociales como Facebook y Google+ o cuentas locales. |
| *api.localaccountpasswordreset* | **Página de contraseña olvidada**. Esta página contiene un formulario que el usuario tiene que rellenar para iniciar el restablecimiento de contraseña.  |
| *api.localaccountsignin* | **Página de inicio de sesión en una cuenta local**. Esta página contiene un formulario de registro con una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener un cuadro de entrada de texto y un cuadro de entrada de contraseña. |
| *api.localaccountsignup* | **Página de registro en una cuenta local**. Esta página contiene un formulario de registro para una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener varios controles de entrada, como un cuadro de entrada de texto, un cuadro de entrada de contraseña, un botón de radio, cuadros desplegables de selección única y casillas de selección múltiple. |
| *api.phonefactor* | **Página de autenticación multifactor**. Esta página permite a los usuarios verificar sus números de teléfono (mediante texto o voz) durante el registro o el inicio de sesión. |
| *api.selfasserted* | **Página de registro en una cuenta social**. Esta página contiene un formulario de registro que los usuarios tienen que rellenar al registrarse con una cuenta existente de un proveedor de identidades social, como Facebook o Google+. Esta página es similar a la página anterior de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.selfasserted.profileupdate* | **Página de actualización de perfil**. Esta página contiene un formulario que los usuarios pueden usar para actualizar su perfil. Esta página es similar a la página de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.signuporsignin* | **Página de inicio de sesión o registro unificada**. Esta página controla tanto la suscripción como el inicio de sesión de los usuarios, que pueden usar proveedores de identidades de empresa, proveedores de identidades sociales, como Facebook o Google +, o cuentas locales.  |

## <a name="next-steps"></a>Pasos siguientes

Para información adicional sobre los elementos de la interfaz de usuario que se pueden personalizar, consulte la [guía de referencia para la personalización de la interfaz de usuario para las directivas integradas](active-directory-b2c-reference-ui-customization.md).
