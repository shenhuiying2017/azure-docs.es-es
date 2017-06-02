---
title: "Azure Active Directory B2C: personalización de la interfaz de usuario con las directivas personalizadas | Microsoft Docs"
description: "Un tema sobre la personalización de la interfaz de usuario mediante el uso de directivas personalizadas en Azure AD B2C"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: 9de6a57671cf65c4aa5d8695d21e0932175b1183
ms.contentlocale: es-es
ms.lasthandoff: 05/22/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: personalización de la interfaz de usuario en una directiva personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Después de completar este artículo, tendrá una directiva personalizada de registro e inicio de sesión con su marca y apariencia.  Azure AD B2C permite controlar casi completamente el código HTML y CSS que se presenta al usuario final.  Cuando se usa una directiva personalizada, la personalización de la interfaz del usuario se configura en XML en lugar de usando controles de Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Antes de continuar, debe completar el artículo de [introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).  Debe tener una directiva personalizada activa para registrar e iniciar sesión de cuentas locales.

## <a name="the-page-ui-customization-feature"></a>La característica de personalización de la interfaz de usuario de la página

Con la característica de interfaz de usuario de página, puede personalizar la apariencia de cualquier directiva personalizada.  Mantenga la coherencia visual y de la marca entre la aplicación y Azure AD B2C.

Funciona de la siguiente manera: Azure AD B2C ejecuta código en el explorador del consumidor y usa un enfoque moderno denominado [Uso compartido de recursos entre orígenes (CORS)](http://www.w3.org/TR/cors/).  En primer lugar, especifique una dirección URL en la directiva personalizada con contenido HTML personalizado.  Azure AD B2C combina elementos de interfaz de usuario con el contenido HTML cargado desde la dirección URL y muestra la página al consumidor.

## <a name="creating-your-html5-content"></a>Creación de contenido HTML5

Vamos a crear contenido HTML con el nombre de la marca del producto en el título.

1. Haga clic en **Copiar** en este fragmento de código HTML.  Se trata de código HTML5 con formato correcto con un elemento vacío llamado `<div id="api"></div>` ubicado en alguna parte de `<body>`. Este elemento marca dónde se inserta el contenido de Azure AD B2C.

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

1. Péguelo en un editor de texto y guarde el archivo como `customize-ui.html`

## <a name="create-a-blob-storage-account"></a>Creación de una cuenta de almacenamiento de blobs

>[!NOTE]
> En esta guía, se usa Azure Blob Storage para hospedar el contenido.  También podría hospedar el contenido en un servidor web, pero se requiere que [habilite Uso compartido de recursos entre orígenes (CORS) en el servidor web](https://enable-cors.org/server.html).

Hospedemos este código HTML en Azure Blob Storage.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
1. En el menú central, seleccione **Nuevo** -> **Almacenamiento** -> **Cuenta de almacenamiento**.
1. Escriba un **nombre** único para la cuenta de almacenamiento.
1. **Modelo de implementación** puede quedar como **Resource Manager**.
1. Cambie **Tipo de cuenta** a **Almacenamiento de blobs**.
1. **Rendimiento** puede quedar como **Estándar**.
1. **Replicación** puede quedar como **RA-GRS**.
1. **Nivel de acceso** puede quedar como **Acceso frecuente**.
1. **Cifrado del servicio de almacenamiento** puede quedar como **Deshabilitado**.
1. Seleccione una **suscripción** para la cuenta de almacenamiento.
1. Cree un **grupo de recursos** o seleccione uno existente.
1. Seleccione la **ubicación geográfica** de la cuenta de almacenamiento.
1. Haga clic en **Crear** para crear la cuenta de almacenamiento.
1. Espere que termine la implementación y la hoja de la cuenta de almacenamiento se abrirá automáticamente.

## <a name="create-a-container"></a>Creación de un contenedor

Vamos a crear un contenedor público en Azure Blob Storage.

1. Cambie a la pestaña de la izquierda, llamada **Información general**.
1. Haga clic en **+ Contenedor**.
1. En **Nombre**, escriba `$root`
1. Establezca **Tipo de acceso** en **Blob**.
1. Haga clic en "$root" para abrir el contenedor nuevo.
1. Haga clic en **Cargar**.
1. Haga clic en el icono de carpeta junto a `Select a file`.
1. Vaya a `customize-ui.html` que creamos en [la sección anterior](#the-page-ui-customization-feature).
1. Haga clic en **Cargar**.
1. Seleccione el blob `customize-ui.html` que cargamos.
1. Haga clic en el botón de copiar junto a la **dirección URL**.
1. Abra un explorador y vaya a esta dirección URL.  Si no puede acceder, asegúrese de que el tipo de acceso de contenedor está establecido en blob.

## <a name="configure-cors"></a>Configuración de CORS

A continuación, vamos a configurar Azure Blob Storage para Uso compartido de recursos entre orígenes (CORS).

>[!NOTE]
>¿Desea probar la característica de personalización de la interfaz de usuario con nuestro contenido HTML y CSS de ejemplo?  Proporcionamos [una herramienta auxiliar sencilla](active-directory-b2c-reference-ui-customization-helper-tool.md) que carga y configura el contenido de ejemplo en la cuenta de Azure Blob Storage.  Si usa la herramienta, vaya directamente a [Modificación de la directiva de inicio de sesión o de registro](#modify-your-sign-up-or-sign-in-custom-policy)

1. En la configuración de la hoja de almacenamiento, abra **CORS**.
1. Haga clic en **+ Agregar**.
1. Establezca `Allowed origins` en `*`.
1. En la lista desplegable `Allowed verbs`, seleccione `GET` y `OPTIONS`.
1. Establezca `Allowed headers` en `*`.
1. Establezca `Exposed headers` en `*`.
1. Establezca `Maximum age (seconds)` en `200`.
1. Haga clic en **Agregar**.

## <a name="testing-cors"></a>Prueba de CORS

Vamos a validar nuestra preparación.

1. Vaya a http://test-cors.org/ y pegue la dirección URL en el campo `Remote URL`.
1. Haga clic en **Enviar solicitud**
1. Si recibe un error, asegúrese de que la [configuración de CORS](#configure-cors) es correcta.  Puede que también deba borrar la caché del explorador o intente una sesión de navegación privada `CTRL-SHIFT-P`.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificación de la directiva de inicio de sesión o de registro

1. En la etiqueta `<TrustFrameworkPolicy>` de nivel superior, debe buscar la etiqueta `<BuildingBlocks>`.  Dentro de la etiqueta `<BuildingBlocks>`, copie este ejemplo para agregar una etiqueta `ContentDefinitions`.  Reemplace `{your_storage_account}` por el nombre de la cuenta de almacenamiento.

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

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja Azure AD B2C.
1. Haga clic en **Todas las directivas**.
1. Haga clic en **Cargar directiva**
1. Cargue `SignUpOrSignin.xml` con la etiqueta `ContentDefinitions` que agregamos.

## <a name="test-the-custom-policy-using-run-now"></a>Prueba de la directiva personalizada con "Ejecutar ahora"

1. Abra la **hoja Azure AD B2C** y vaya a **Todas las directivas**.
1. Seleccione la directiva personalizada que cargó y, luego, haga clic en el botón **Ejecutar ahora**.
1. Debe poder registrarse con una dirección de correo electrónico.

## <a name="next-steps"></a>Pasos siguientes

Esta [guía de referencia para la personalización de la interfaz de usuario para las directivas integradas](active-directory-b2c-reference-ui-customization.md) contiene información adicional sobre los elementos de la interfaz de usuario que se pueden personalizar.  No hay diferencia entre la personalización de la interfaz de usuario entre las directivas integradas y las directivas personalizadas.

