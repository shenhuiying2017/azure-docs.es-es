---
title: "Referencia de Azure Active Directory B2C: personalización de la IU de un recorrido de usuario con directivas personalizadas | Microsoft Docs"
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 68f40aa638a687398512278a0b77d1ba392859cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalización de la IU de un recorrido de usuario con directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Este artículo es una descripción avanzada del funcionamiento de la personalización de la interfaz de usuario y cómo habilitarla con directivas B2C personalizadas, mediante el marco de experiencia de identidad.


En una solución de negocio a consumidor, es esencial una experiencia de usuario sin problemas. Por experiencia de usuario sin problemas, entendemos una experiencia, ya sea en un dispositivo o en un explorador, donde el recorrido de un usuario por nuestro servicio no se pueda distinguir del que tiene lugar en el servicio al cliente que usa.

## <a name="understand-the-cors-way-for-ui-customization"></a>Descripción del método CORS para la personalización de la interfaz de usuario

Azure AD B2C le permite personalizar el aspecto de la experiencia de usuario (UX) en las diversas páginas que se atienden y muestran mediante las directivas personalizadas.

Con esa finalidad, Azure AD B2C ejecuta código en el explorador del consumidor y usa el enfoque moderno y estándar [Uso compartido de recursos entre orígenes (CORS)](http://www.w3.org/TR/cors/) para cargar contenido personalizado desde una dirección URL específica que se define en una directiva personalizada para que apunte a las plantillas HTML5/CSS. CORS es un mecanismo que permite que se soliciten recursos restringidos, como fuentes, en una página web desde otro dominio fuera del dominio del que se origina el recurso.

En comparación con la antigua forma tradicional, donde las páginas de plantilla eran propiedad de la solución, donde se proporcionaba texto e imágenes limitados o donde se ofrecía un control limitado del diseño y funcionamiento que llevaba a más dificultades para lograr una experiencia sin problemas, el método CORS admite HTML5 y CSS y le permite:

- Hospedar el contenido y la solución e insertar sus controles mediante el script del lado cliente.
- Tener control total sobre cada píxel del diseño y el funcionamiento.

Puede proporcionar tantas páginas de contenido como quiera mediante la creación de archivos HTML5/CSS según sea adecuado.

> [!NOTE]
> Por motivos de seguridad, el uso de JavaScript está bloqueado para la personalización. Para desbloquear JavaScript, es necesario el uso de un nombre de dominio personalizado para su inquilino de Azure AD B2C.

En cada una de las plantillas HTML5/CSS, se proporciona un elemento *anchor*, que corresponde al elemento `<div id=”api”>` necesario en el código HTML o en la página de contenido como se ilustra a continuación. Azure AD B2C requiere que todas las páginas de contenido tengan este div específico.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

El contenido relacionado con Azure AD B2C de la página se insertará en este div, mientras que el resto de la página está bajo su control. El código JavaScript de Azure AD B2C extrae el contenido e inserta nuestro HTML en el elemento div específico. Azure AD B2C inserta los siguientes controles según sea adecuado: control selector de cuenta, controles de inicio de sesión, controles multifactor (actualmente basados en teléfono) y controles de colección de atributos. Azure AD B2C garantiza que todos los controles son accesibles y compatibles con HTML5, que se les puede cambiar completamente el estilo y que una versión de control no los deteriorará.

El contenido combinado se muestra finalmente como el documento dinámico para el consumidor.

Para garantizar que lo indicado anteriormente funcione según lo esperado, debe:

- Asegurarse de que el contenido sea accesible y compatible con HTML5.
- Asegurarse de que el servidor de contenido esté habilitado para CORS.
- Servir el contenido a través de HTTPS.
- Usar direcciones URL absolutas como https://yourdomain/content para todos los vínculos y el contenido de CSS.

> [!TIP]
> Para comprobar que el sitio en el que hospeda su contenido tiene habilitado CORS y probar las solicitudes CORS, puede usar el sitio http://test-cors.org/. Gracias a este sitio, puede enviar simplemente la solicitud CORS a un servidor remoto (para probar si se admite CORS) o enviarla a un servidor de prueba (para explorar determinadas características de CORS).

> [!TIP]
> El sitio http://enable-cors.org/ también constituye un recurso de gran utilidad sobre CORS.

Gracias a este enfoque basado en CORS, los usuarios finales tendrán experiencias coherentes entre su aplicación y las páginas que atiende Azure AD B2C.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Como requisito previo, debe crear una cuenta de almacenamiento. Necesitará una suscripción de Azure para crear una cuenta de Almacenamiento de blobs de Azure. Puede registrarse para obtener una evaluación gratuita en el [sitio web de Azure](https://azure.microsoft.com/en-us/pricing/free-trial/).

1. Abra una sesión de exploración y desplácese hasta el [portal de Azure](https://portal.azure.com).
2. Inicie sesión con sus credenciales administrativas.
3. Haga clic en **Nuevo** > **Datos y almacenamiento** > **Cuenta de almacenamiento**.  Se abre la hoja **Crear cuenta de almacenamiento**.
4. En **Nombre**, proporcione un nombre para la cuenta de almacenamiento, por ejemplo, *contoso369b2c*. Más adelante se hará referencia a este valor como *storageAccountName*.
5. Elija las selecciones adecuadas para el plan de tarifa, el grupo de recursos y la suscripción. Asegúrese de tener la opción **Anclar a Panel de inicio** activada. Haga clic en **Crear**.
6. Regrese al panel de inicio y haga clic en la cuenta de almacenamiento que acaba de crear.
7. En la sección **Servicios**, haga clic en **Blobs**. Se abre una hoja **Blob service**.
8. Haga clic en **+ Contenedor**.
9. En **Nombre**, proporcione un nombre para el contenedor, por ejemplo, *b2c*. Más adelante se hará referencia a este valor como *containerName*.
9. Seleccione **Blob** para **Tipo de acceso**. Haga clic en **Crear**.
10. El contenedor que creó aparecerá en la lista de la hoja **Blob service**.
11. Cierre la hoja **Blobs** .
12. En la hoja **Cuenta de almacenamiento**, haga clic en el icono de **Clave**. Se abre una hoja **Claves de acceso**.  
13. Anote el valor de **key1**. Más adelante se hará referencia a este valor como *key1*.

## <a name="downloading-the-helper-tool"></a>Descarga de la herramienta auxiliar

1.  Descargue la herramienta auxiliar desde [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Guarde el archivo *B2C-AzureBlobStorage-Client-master.zip* en la máquina local.
3.  Extraiga el contenido del archivo B2C-AzureBlobStorage-Client-master.zip en su disco local, por ejemplo, en la carpeta **UI-Customization-Pack**. Se creará debajo una carpeta *B2C-AzureBlobStorage-Client-master*.
4.  Abra la carpeta y extraiga en ella el contenido del archivo *B2CAzureStorageClient.zip*.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Carga de los archivos de ejemplo UI-Customization-Pack

1.  Mediante el Explorador de Windows, vaya a la carpeta *B2C-AzureBlobStorage-Client-master* ubicada en la carpeta *UI-Customization-Pack* creada en la sección anterior.
2.  Ejecute el archivo *B2CAzureStorageClient.exe*. Este programa simplemente carga todos los archivos del directorio especificado en la cuenta de almacenamiento y permite que CORS acceda a esos archivos.
3.  Cuando se le solicite, especifique: a.  El nombre de la cuenta de almacenamiento *storageAccountName*, por ejemplo *contoso369b2c*.
    b.  La clave de acceso principal de Azure Blob Storage, *key1*, por ejemplo, *contoso369b2c*.
    c.  El nombre de su contenedor de Blob Storage, *containerName*, por ejemplo, *b2c*.
    d.  La ruta de acceso de los archivos de ejemplo del *paquete de inicio*, por ejemplo, *\B2CTemplates\wingtiptoys*.

Si siguió los pasos anteriores, los archivos HTML5 y CSS del *UI-Customization-Pack* de la empresa ficticia **wingtiptoys** apuntará ahora a la cuenta de almacenamiento.  Puede comprobar que el contenido se ha cargado correctamente; para ello, abra la hoja del contenedor relacionada en el portal de Azure. Como alternativa, puede comprobar que el contenido se ha cargado correctamente accediendo a la página desde un explorador. Para más información, consulte [Azure Active Directory B2C: una herramienta auxiliar que se usa para demostrar la característica de personalización de la interfaz de usuario de las páginas](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Comprobación de que la cuenta de almacenamiento está habilitada para CORS

CORS (Uso compartido de recursos entre orígenes) debe estar habilitado en su punto de conexión para que Azure AD B2C cargue el contenido. El motivo es que el contenido está hospedado en un dominio diferente al dominio desde el que Azure AD B2C Premium atiende la página.

Para comprobar que el almacenamiento en el que hospeda su contenido está habilitado para CORS, siga estos pasos:

1. Abra una sesión de exploración y vaya a la página *unified.html* mediante la dirección URL completa de su ubicación en su cuenta de almacenamiento, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Por ejemplo, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Vaya a http://test-cors.org. Este sitio le permite comprobar que la página que usa está habilitada para CORS.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. En **Remote URL** (Dirección URL remota), escriba la dirección URL completa de su contenido unified.html y haga clic en **Send Request** (Enviar solicitud).
4. Compruebe que la salida de la sección **Results**(Resultados) contenga el *estado XHR: 200*. Esto indica que CORS está habilitado.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
La cuenta de almacenamiento debe incluir ahora un contenedor de blobs llamado *b2c* en nuestra ilustración que contiene las siguientes plantillas de wingtiptoys del *paquete de inicio*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

En la tabla siguiente se describe la finalidad de las páginas HTML5 anteriores.

| Plantilla HTML5 | Descripción |
|----------------|-------------|
| *phonefactor.html* | Esta página se puede usar como plantilla para una página de autenticación multifactor. |
| *resetpassword.html* | Esta página se puede usar como plantilla para una página de contraseña olvidada. |
| *selfasserted.html* | Esta página se puede usar como plantilla para una página de registro en una cuenta social, una página de registro en una cuenta local o una página de inicio de sesión en una cuenta local. |
| *unified.html* | Esta página se puede usar como plantilla para una página de inicio de sesión o registro unificada. |
| *updateprofile.html* | Esta página se puede usar como plantilla para una página de actualización de perfil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Adición de un vínculo a las plantillas HTML5/CSS al recorrido de usuario

Puede agregar un vínculo a las plantillas HTML5/CSS al recorrido de usuario mediante la edición de una directiva personalizada de forma directa.

Las plantillas HTML5/CSS personalizadas que se usarán en el recorrido de usuario se deben especificar en una lista de definiciones de contenido que se pueden emplear en esos recorridos de usuario. Para este fin, se debe declarar un elemento XML *<ContentDefinitions>* opcional en la sección *<BuildingBlocks>* de su archivo XML de directivas personalizadas.

En la tabla siguiente se describe el conjunto de identificadores de definición de contenido reconocidos por el motor de experiencia de identidad de Azure AD B2C y el tipo de páginas relacionadas con ellos.

| Id. de definición de contenido | Descripción |
|-----------------------|-------------|
| *api.error* | **Página de error**. Esta página se muestra cuando se produce una excepción o un error. |
| *api.idpselections* | **Página de selección del proveedor de identidades**. Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión. Estos son los proveedores de identidades de empresa, los proveedores de identidades sociales como Facebook y Google+ o las cuentas locales (basadas en una dirección de correo electrónico o un nombre de usuario). |
| *api.idpselections.signup* | **Selección del proveedor de identidades para el registro**. Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión. Estos son los proveedores de identidades de empresa, los proveedores de identidades sociales como Facebook y Google+ o las cuentas locales (basadas en una dirección de correo electrónico o un nombre de usuario). |
| *api.localaccountpasswordreset* | **Página de contraseña olvidada**. Esta página contiene un formulario que el usuario tiene que rellenar para iniciar el restablecimiento de contraseña.  |
| *api.localaccountsignin* | **Página de inicio de sesión en una cuenta local**. Esta página contiene un formulario de registro que el usuario tiene que rellenar al iniciar sesión con una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener un cuadro de entrada de texto y un cuadro de entrada de contraseña. |
| *api.localaccountsignup* | **Página de registro en una cuenta local**. Esta página contiene un formulario de registro que el usuario tiene que rellenar al registrarse con una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener diferentes controles de entrada, como un cuadro de entrada de texto, un cuadro de entrada de contraseña, un botón de radio, cuadros desplegables de selección única y casillas de verificación de selección múltiple. |
| *api.phonefactor* | **Página de autenticación multifactor**. Esta página permite a los usuarios verificar sus números de teléfono (mediante texto o voz) durante el registro o el inicio de sesión. |
| *api.selfasserted* | **Página de registro en una cuenta social**. Esta página contiene un formulario de registro que el usuario tiene que rellenar al registrarse con una cuenta existente de un proveedor de identidades social, como Facebook o Google+. Esta página es similar a la página anterior de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.selfasserted.profileupdate* | **Página de actualización de perfil**. Esta página contiene un formulario que el usuario puede usar para actualizar su perfil. Esta página es similar a la página anterior de registro en una cuenta social, excepto por los campos de entrada de contraseña. |
| *api.signuporsignin* | **Página de inicio de sesión o registro unificada**.  Esta página controla tanto la suscripción como el inicio de sesión de los usuarios, que pueden usar proveedores de identidades de empresa, proveedores de identidades sociales, como Facebook o Google +, o cuentas locales.

## <a name="next-steps"></a>Pasos siguientes
[Referencia: Understand how custom policies work with the Identity Experience Framework in B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md) (Funcionamiento de las directivas personalizadas con el marco de experiencia de identidad en B2C)
