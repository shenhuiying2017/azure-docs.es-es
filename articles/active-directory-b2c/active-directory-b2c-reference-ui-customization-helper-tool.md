---
title: "Azure Active Directory B2C: herramienta auxiliar de personalización de la interfaz de usuario de página | Microsoft Docs"
description: "Herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario (IU) de página en Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: bryanla
ms.assetid: ae935d52-3520-4a94-b66e-b35bb40e7514
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: swkrish
ms.openlocfilehash: a9ccdea64213d564b271699afe28f5ae6db0a71a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: una herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario de la página
Este artículo es un complemento del [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md) de Azure Active Directory (Azure AD) B2C. Los pasos siguientes describen cómo ejecutar la característica de personalización de la interfaz de usuario de la página mediante el contenido HTML y CSS de ejemplo que hemos proporcionado.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtención de un inquilino de Azure AD B2C
Antes de personalizar nada, deberá [obtener un inquilino de Azure AD B2C](active-directory-b2c-get-started.md) si todavía no tiene uno.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión
El contenido de ejemplo que hemos proporcionado se puede usar para personalizar dos páginas en una [directiva de registro o de inicio de sesión](active-directory-b2c-reference-policies.md): [la página de inicio de sesión unificado](active-directory-b2c-reference-ui-customization.md) y la [página de atributos autoafirmados](active-directory-b2c-reference-ui-customization.md). Al [crear una directiva de registro o de inicio de sesión](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), agregue Cuenta local (dirección de correo electrónico), Facebook, Google y Microsoft como **proveedores de identidades**. Estos son los únicos proveedores de identidades que aceptará nuestro contenido HTML de ejemplo.  También puede agregar un subconjunto de estos IDP si lo desea.

## <a name="register-an-application"></a>Registro de una aplicación
Deberá [registrar la aplicación](active-directory-b2c-app-registration.md) en el inquilino B2C que puede usarse para ejecutar la directiva. Después de registrar la aplicación, tiene algunas opciones que puede usar para ejecutar la directiva de registro:

* Compilar una de las aplicaciones de inicio rápido de Azure AD B2C que se enumeran en la sección "Introducción" de [Versión preliminar de Azure Active Directory B2C: registro e inicio de sesión de consumidores en las aplicaciones](active-directory-b2c-overview.md#get-started).
* Usar la aplicación de [área de juegos de Azure AD B2C](https://aadb2cplayground.azurewebsites.net) previamente compilada. Si elige usar el área de juegos, debe registrar una aplicación en el inquilino de B2C mediante el **URI de redirección**`https://aadb2cplayground.azurewebsites.net/`.
* Use el botón **Ejecutar ahora** de la directiva en el [Portal de Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personalización de la directiva
Para personalizar la apariencia de la directiva, debe crear primero los archivos HTML y CSS usando las convenciones específicas de Azure AD B2C. Luego, puede cargar su contenido estático en una ubicación disponible públicamente para que Azure AD B2C pueda tener acceso a él. Esta ubicación podría ser su propio servidor web dedicado, Azure Blob Storage, Azure Content Delivery Network o cualquier otro proveedor de hospedaje de recursos estático. Los únicos requisitos son que el contenido esté disponible a través de HTTPS y que se pueda tener acceso a él mediante CORS. Una vez que se ha expuesto el contenido estático en la página web, puede editar la directiva para que señale a esta ubicación y presentar ese contenido a los consumidores. El [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md) describe de forma detallada cómo funciona la característica de personalización de Azure AD B2C.

Para los fines de este tutorial, ya hemos creado algún contenido de ejemplo y lo hemos hospedado en Azure Blob Storage. El contenido de ejemplo es una personalización muy básica del tema de nuestra empresa ficticia: "Wingtip Toys". Para probarlo en su propia directiva, siga estos pasos:

1. Inicie sesión en el inquilino en el [Portal de Azure](https://portal.azure.com/) y navegue hasta la hoja de características de B2C.
2. Haga clic en **Directivas de inicio de sesión o registro** y en su directiva (por ejemplo, "b2c\_1\_sign\_up\_sign\_in").
3. Haga clic en **Personalización de la interfaz de usuario de la página** y, luego, en **Página unificada de inicio de sesión o de registro**.
4. Cambie el conmutador **Usar página personalizada** a **Sí**. En el campo **URI de página personalizada**, escriba `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Haga clic en **OK**.
5. Haga clic en **Página de suscripción de cuenta local**. Cambie el conmutador **Usar plantilla personalizada** a **Sí**. En el campo **URI de página personalizada**, escriba `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Repita el mismo paso para la **página de registro de cuenta de redes sociales**.
   Haga clic en **Aceptar** dos veces para cerrar las hojas de personalización de la interfaz de usuario.
7. Haga clic en **Guardar**.

Ahora puede probar la directiva personalizada. Puede usar su propia aplicación o el área de juegos de Azure AD B2C si lo desea, pero también puede hacer simplemente clic en el comando **Ejecutar** de la hoja de directivas. Seleccione la aplicación en la lista desplegable y elija el URI de redirección correspondiente. Haga clic en el botón **Ejecutar ahora** . Se abrirá una nueva pestaña del explorador y podrá pasar por la experiencia de usuario de registro en su aplicación con el nuevo contenido implementado.

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Carga del contenido de ejemplo en Azure Blob Storage
Si desea usar Azure Blob Storage para hospedar el contenido de su página, puede crear su propia cuenta de almacenamiento y usar nuestra herramienta auxiliar de B2C para cargar los archivos.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ Nuevo** > **Datos y almacenamiento** > **Cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de Azure Blob Storage. Puede registrarse para obtener una evaluación gratuita en el [sitio web de Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Proporcione un **Nombre** para la cuenta de almacenamiento (por ejemplo, "contoso") y elija las opciones adecuadas para **Plan de tarifa**, **Grupo de recursos** y **Suscripción**. Asegúrese de tener la opción **Anclar a Panel de inicio** activada. Haga clic en **Crear**.
4. Regrese al panel de inicio y haga clic en la cuenta de almacenamiento que acaba de crear.
5. En la sección **Resumen**, haga clic en **Contenedores** y luego en **+ Agregar**.
6. Asigne un **Nombre** al contenedor (por ejemplo, "b2c") y seleccione **Blob** como **Tipo de acceso**. Haga clic en **Aceptar**.
7. El contenedor que creó aparecerá en la lista de la hoja **Blobs** . Anote la dirección URL del contenedor; por ejemplo, debería ser similar a `https://contoso.blob.core.windows.net/b2c`. Cierre la hoja **Blobs** .
8. En la hoja de la cuenta de almacenamiento, haga clic en **Claves** y anote los valores de los campos **Nombre de la cuenta de almacenamiento** y **Clave de acceso primaria**.

> [!NOTE]
> **Clave de acceso primaria** es una credencial de seguridad importante.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Descargue la herramienta auxiliar y los archivos de ejemplo.
Puede descargar la [herramienta auxiliar de Azure Blob Storage y archivos de ejemplo en formato .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) o clonarla de GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositorio contiene un directorio `sample_templates\wingtip` , que contiene HTML, CSS e imágenes de ejemplo. Para que estas plantillas hagan referencia a su propia cuenta de Azure Blob Storage, debe editar los archivos HTML. Abra `unified.html` y `selfasserted.html`, y reemplace todas las instancias de `https://localhost` por la dirección URL de su propio contenedor que anotó en los pasos anteriores. Debe usar la ruta de acceso absoluta de los archivos HTML porque, en este caso, Azure AD servirá el código HTML bajo el dominio `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Carga de los archivos de ejemplo
En el mismo repositorio, descomprima `B2CAzureStorageClient.zip` y ejecute el archivo `B2CAzureStorageClient.exe` que hay dentro. Este programa simplemente carga todos los archivos del directorio especificado en la cuenta de almacenamiento y permite que CORS acceda a esos archivos. Si siguió los pasos anteriores, los archivos HTML y CSS apuntarán ahora a la cuenta de almacenamiento. Tenga en cuenta que el nombre de la cuenta de almacenamiento es la parte que precede a `blob.core.windows.net`; por ejemplo, `contoso`. Para comprobar que el contenido se ha cargado correctamente, intente acceder a `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` en un explorador. Use también [http://test-cors.org/](http://test-cors.org/) para asegurarse de que el contenido está ahora habilitado para CORS. (busque "estado XHR: 200" en el resultado).

### <a name="customize-your-policy-again"></a>Personalización de la directiva, de nuevo
Ahora que ha cargado el contenido de ejemplo en su propia cuenta de almacenamiento, debe modificar la directiva de registro para que haga referencia a él. Repita los pasos de la sección anterior ["Personalización de la directiva"](#customize-your-policy) , esta vez usando las direcciones URL de su propia cuenta de almacenamiento. Por ejemplo, la ubicación de su archivo `unified.html` sería `<url-of-your-container>/wingtip/unified.html`.

Ahora puede usar el botón **Ejecutar ahora** o su propia aplicación para volver a ejecutar la directiva. El resultado debería ser casi exactamente el mismo, ya que usó el mismo HTML y CSS de ejemplo en ambos casos. Sin embargo, las directivas ahora hacen referencia a su propia instancia de Azure Blob Storage, y tiene la posibilidad de editar y volver a cargar los archivos a su gusto. Para más información acerca de la personalización de HTML y CSS, consulte el [artículo principal sobre la personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md).

