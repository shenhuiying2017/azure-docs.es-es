---
title: 'Azure Active Directory B2C: herramienta auxiliar de personalización de la interfaz de usuario de página | Microsoft Docs'
description: Herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario (IU) de página en Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2016
ms.author: swkrish

---
# Azure Active Directory B2C: una herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario de la página
Este artículo es un complemento del [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md) de Azure Active Directory (Azure AD) B2C. Los pasos siguientes describen cómo ejecutar la característica de personalización de la interfaz de usuario de la página mediante el contenido HTML y CSS de ejemplo que hemos proporcionado.

## Obtención de un inquilino de Azure AD B2C
Antes de personalizar nada, deberá [obtener un inquilino de Azure AD B2C](active-directory-b2c-get-started.md) si todavía no tiene uno.

## Creación de una directiva de registro o de inicio de sesión
El contenido de ejemplo que hemos proporcionado se puede usar para personalizar dos páginas en una [directiva de registro o de inicio de sesión](active-directory-b2c-reference-policies.md): la [página de inicio de sesión unificado](active-directory-b2c-reference-ui-customization.md) y la [página de atributos autoafirmados](active-directory-b2c-reference-ui-customization.md). Al [crear una directiva de registro o de inicio de sesión](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), agregue Cuenta local (dirección de correo electrónico), Facebook, Google y Microsoft como **proveedores de identidades**. Estos son los únicos proveedores de identidades que aceptará nuestro contenido HTML de ejemplo. También puede agregar un subconjunto de estos IDP si lo desea.

## Registro de una aplicación
Deberá [registrar la aplicación](active-directory-b2c-app-registration.md) en el inquilino B2C que puede usarse para ejecutar la directiva. Después de registrar la aplicación, tiene algunas opciones que puede usar para ejecutar la directiva de registro:

* Compilar una de las aplicaciones de inicio rápido de Azure AD B2C que se enumeran en la sección "Introducción" de [Versión preliminar de Azure Active Directory B2C: registro e inicio de sesión de consumidores en las aplicaciones](active-directory-b2c-overview.md#getting-started).
* Usar la aplicación de [área de juegos de Azure AD B2C](https://aadb2cplayground.azurewebsites.net) previamente compilada. Si elige usar el área de juegos, debe registrar una aplicación en el inquilino de B2C mediante el **URI de redirección** `https://aadb2cplayground.azurewebsites.net/`.
* Use el botón **Ejecutar ahora** de la directiva en el [Portal de Azure](https://portal.azure.com/).

## Personalización de la directiva
Para personalizar la apariencia de la directiva, debe crear primero los archivos HTML y CSS usando las convenciones específicas de Azure AD B2C. Luego, puede cargar su contenido estático en una ubicación disponible públicamente para que Azure AD B2C pueda tener acceso a él. Esta ubicación podría ser su propio servidor web dedicado, el Almacenamiento de blobs de Azure, la Red de entrega de contenido de Azure o cualquier otro proveedor de hospedaje de recursos estático. Los únicos requisitos son que el contenido esté disponible a través de HTTPS y que se pueda tener acceso a él mediante CORS. Una vez que se ha expuesto el contenido estático en la página web, puede editar la directiva para que señale a esta ubicación y presentar ese contenido a los consumidores. El [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md) describe de forma detallada cómo funciona la característica de personalización de Azure AD B2C.

Para los fines de este tutorial, ya hemos creado algún contenido de ejemplo y lo hemos hospedado en el almacenamiento de blobs de Azure. El contenido de ejemplo es una personalización muy básica del tema de nuestra empresa ficticia: "Wingtip Toys". Para probarlo en su propia directiva, siga estos pasos:

1. Inicie sesión en el inquilino en el [Portal de Azure](https://portal.azure.com/) y navegue hasta la hoja de características de B2C.
2. Haga clic en **Sign-up or sign-in policies** (Directivas de registro o de inicio de sesión) y luego en su directiva (por ejemplo, "b2c\_1\_sign\_up").
3. Haga clic en **Page UI customization** (Personalización de interfaz de usuario de página) y, luego, en **Unified sign-up or sign-in page** (Página de registro o de inicio de sesión unificada).
4. Coloque el conmutador **Usar página personalizada** en la posición **Sí**. En el campo **URI de página personalizada**, escriba `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Haga clic en **OK**.
5. Haga clic en **Página de suscripción de cuenta local**. Alterne el conmutador **Usar plantilla personalizada** a **Sí**. En el campo **URI de página personalizada**, escriba `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Repita el mismo paso para la **página de registro de cuenta de redes sociales**. Haga clic en **Aceptar** dos veces para cerrar las hojas de personalización de la interfaz de usuario.
7. Haga clic en **Guardar**.

Ahora puede probar la directiva personalizada. Puede usar su propia aplicación o el área de juegos de Azure AD B2C si lo desea, pero también puede hacer simplemente clic en el comando **Ejecutar** de la hoja de directivas. Seleccione la aplicación en la lista desplegable y elija el URI de redirección correspondiente. Haga clic en el botón **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá pasar por la experiencia de usuario de registro en su aplicación con el nuevo contenido implementado.

## Carga del contenido de ejemplo en el almacenamiento de blobs de Azure
Si desea usar el almacenamiento de blobs de Azure para hospedar el contenido de su página, puede crear su propia cuenta de almacenamiento y usar nuestra herramienta auxiliar de B2C para cargar los archivos.

### Crear una cuenta de almacenamiento
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ Nuevo** -> **Datos y almacenamiento** -> **Cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de Almacenamiento de blobs de Azure. Puede registrarse para obtener una evaluación gratuita en el [sitio web de Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Especifique un **nombre** para la cuenta de almacenamiento (por ejemplo, "contoso") y elija las opciones adecuadas en **Plan de tarifa**, **Grupo de recursos** y **Suscripción**. Asegúrese de tener la opción **Anclar a Panel de inicio** activada. Haga clic en **Crear**.
4. Regrese al panel de inicio y haga clic en la cuenta de almacenamiento que acaba de crear.
5. En la sección **Resumen**, haga clic en **Contenedores** y luego en **+ Agregar**.
6. Asigne un **Nombre** al contenedor (por ejemplo, "b2c") y seleccione **Blob** como el **Tipo de acceso**. Haga clic en **OK**.
7. El contenedor que creó aparecerá en la lista de la hoja **Blobs**. Anote la dirección URL del contenedor; por ejemplo, debería ser similar a `https://contoso.blob.core.windows.net/b2c`. Cierre la hoja **Blobs**.
8. En la hoja de la cuenta de almacenamiento, haga clic en **Claves** y anote los valores de los campos **Nombre de la cuenta de almacenamiento** y **Clave de acceso primaria**.
9. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
10. Haga clic en **+ Nuevo** -> **Datos y almacenamiento** -> **Cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de Almacenamiento de blobs de Azure. Puede registrarse para obtener una evaluación gratuita en el [sitio web de Azure](https://azure.microsoft.com/pricing/free-trial/).
11. Seleccione **Almacenamiento de blobs** en **Tipo de cuenta** y deje las restantes opciones con su valor predeterminado. Puede editar los valores de Grupo de recursos y Ubicación si lo desea. Haga clic en **Crear**.
12. Regrese al panel de inicio y haga clic en la cuenta de almacenamiento que acaba de crear.
13. En la sección **Resumen**, haga clic en **+Contenedor**.
14. Asigne un **Nombre** al contenedor (por ejemplo, "b2c") y seleccione **Blob** como el **Tipo de acceso**. Haga clic en **OK**.
15. Abra las **propiedades** del contenedor y anote la dirección URL del contenedor; por ejemplo, debería ser similar a `https://contoso.blob.core.windows.net/b2c`. Cierre la hoja del contenedor.
16. En la hoja de la cuenta de almacenamiento, haga clic en **icono de la llave** y anote los valores de los campos **Nombre de la cuenta de almacenamiento** y **Clave de acceso primaria**.

> [!NOTE]
> La **Clave de acceso principal** es una credencial de seguridad importante.
> 
> 

### Descargue la herramienta auxiliar y los archivos de ejemplo.
Puede descargar la [herramienta auxiliar de Almacenamiento de blobs de Azure y archivos de ejemplo en formato .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) o clonarla de GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositorio contiene un directorio `sample_templates\wingtip`, que contiene HTML, CSS e imágenes de ejemplo. Para que estas plantillas hagan referencia a su propia cuenta de Almacenamiento de blobs de Azure, debe editar los archivos HTML. Abra `unified.html` y `selfasserted.html` y reemplace todas las instancias de `https://localhost` por la dirección URL de su propio contenedor que anotó en los pasos anteriores. Debe usar la ruta de acceso absoluta de los archivos HTML porque, en este caso, Azure AD servirá el código HTML bajo el dominio `https://login.microsoftonline.com`.

### Carga de los archivos de ejemplo
En el mismo repositorio, descomprima `B2CAzureStorageClient.zip` y ejecute el archivo `B2CAzureStorageClient.exe` que hay dentro. Este programa simplemente carga todos los archivos del directorio especificado en la cuenta de almacenamiento y permite que CORS acceda a esos archivos. Si siguió los pasos anteriores, los archivos HTML y CSS apuntarán ahora a la cuenta de almacenamiento. Tenga en cuenta que el nombre de la cuenta de almacenamiento es la parte que precede a `blob.core.windows.net`; por ejemplo, `contoso`. Para comprobar que el contenido se ha cargado correctamente, intente acceder a `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` en un explorador. Use también [http://test-cors.org/](http://test-cors.org/) para asegurarse de que el contenido está ahora habilitado para CORS. (busque "estado XHR: 200" en el resultado).

### Personalización de la directiva, de nuevo
Ahora que ha cargado el contenido de ejemplo en su propia cuenta de almacenamiento, debe modificar la directiva de registro para que haga referencia a él. Repita los pasos de la sección anterior ["Personalización de la directiva"](#customize-your-policy), esta vez usando las direcciones URL de su propia cuenta de almacenamiento. Por ejemplo, la ubicación de su archivo `unified.html` sería `<url-of-your-container>/wingtip/unified.html`.

Ahora puede usar el botón **Ejecutar ahora** o su propia aplicación para volver a ejecutar la directiva. El resultado debería ser casi exactamente el mismo, ya que usó el mismo HTML y CSS de ejemplo en ambos casos. Sin embargo, las directivas ahora hacen referencia a su propia instancia de Almacenamiento de blobs de Azure, y tiene la posibilidad de editar y volver a cargar los archivos a su gusto. Para más información acerca de la personalización de HTML y CSS, consulte el [artículo principal sobre la personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=AcomDC_0727_2016-->