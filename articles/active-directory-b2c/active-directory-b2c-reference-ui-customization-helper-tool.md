<properties
	pageTitle="Vista previa de Azure Active Directory B2C: herramienta auxiliar de personalización de la interfaz de usuario de página | Microsoft Azure"
	description="Herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario (IU) de página en Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: una herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario (IU) de página

Este artículo es un complemento del [principal](active-directory-b2c-reference-ui-customization) sobre la característica de personalización de la interfaz de usuario de página en Azure Active Directory (AD) B2C.

Seguir los pasos que se indican a continuación le permitirá ejecutar la característica de personalización de interfaz de usuario de página empleando nuestro contenido de ejemplo. Solo para fines de demostración, nuestro contenido de ejemplo es HTML5 **estático**.

1. Cargue nuestro contenido de ejemplo en el [Almacenamiento de blobs de Azure](http://azure.microsoft.com/documentation/services/storage/), hágalo accesible públicamente a través de HTTPS y active CORS (Uso compartido de recursos entre orígenes) en esas direcciones URL.
2. Modifique la configuración de personalización de la interfaz de usuario de página en su directiva de registro existente y especifique las direcciones URL anteriores.

Antes de empezar:

- Cree una de las aplicaciones de inicio rápido de Azure AD B2C que aparecen [aquí](active-directory-b2c-overview.md). Como parte de esto, creará una directiva de registro que modificará aquí.
- Descargue la herramienta auxiliar [aquí](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Paso 1: Cargar nuestro contenido de ejemplo en las ubicaciones adecuadas

1. Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com/).
2. Haga clic en **+ Nuevo** -> **Datos + almacenamiento** -> **Cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de Almacenamiento de blobs de Azure. Puede registrar una prueba gratuita [aquí](https://azure.microsoft.com/es-ES/pricing/free-trial/).
3. Asigne un **Nombre** para la cuenta de almacenamiento (por ejemplo, "contoso.core.windows.net") y elija las selecciones adecuadas para el **Nivel de precios**, **Grupo de recursos** y **Suscripción**. Asegúrese de que tiene la opción **Anclar a Panel de inicio** activada. Haga clic en **Crear**.
4. Regrese al panel de inicio y haga clic en la cuenta de almacenamiento que acaba de crear.
5. En la sección **Resumen**, haga clic en **Contenedores** y luego en **+Agregar**.
6. Asigne un **Nombre** al contenedor (por ejemplo, "b2c") y seleccione **Blob** como el **Tipo de acceso**. Haga clic en **OK**.
7. El contenedor que creó aparecerá en la lista de la hoja **Blobs**. Anote la URL del contenedor, por ejemplo, debería ser similar a `https://contoso.blob.core.windows.net/b2c`. Cierre la hoja **Blobs**.
8. En la hoja de cuenta de almacenamiento, haga clic en **Claves** y anote los valores de los campos **Nombre de cuenta de almacenamiento** y **Clave de acceso principal**.

    > [AZURE.NOTE]La **Clave de acceso principal** es una credencial de seguridad importante.

9. Ejecute la herramienta auxiliar y asígnele los valores **Nombre de cuenta de almacenamiento** y **Clave de acceso principal** que se copian en el paso anterior. Esto cargará nuestro contenido de ejemplo en su cuenta de almacenamiento.
10. Compruebe que el contenido se ha cargado correctamente al intentar tener acceso a `https://contoso.blob.core.windows.net/b2c/idp.html` en un explorador. Use también [http://test-cors.org/](http://test-cors.org/) para asegurarse de que el contenido está ahora habilitado con CORS (busque `XHR status: 200` en el resultado).

### Paso 2: Modificar las opciones de personalización de la interfaz de usuario de página en la directiva de inscripción

1. Inicie sesión en el directorio del [portal de vista previa de Azure](https://portal.azure.com) y navegue hasta la hoja de características de B2C.
2. Haga clic en **Directivas de registro** y luego en la directiva de registro (por ejemplo, "B2C\_1\_SiIn").
3. Haga clic en **Personalización de la interfaz de usuario de página** y luego en **Página de selección de proveedores de identidades**.
4. Activa o desactiva el conmutador **Usar plantilla personalizada** en **Sí**. En el campo **URI de página personalizada** escriba la dirección URL adecuada del contenido cargado en su cuenta de almacenamiento. Por ejemplo: `https://contoso.blob.core.windows.net/b2c/idp.html`. Haga clic en **Aceptar**.
5. Haga clic en la **página de suscripción de la cuenta Local**. Active o desactive el conmutador **Usar plantilla personalizada** en **Sí**. En el campo **URI de página personalizada**, escriba la dirección URL adecuada del contenido cargado en su cuenta de almacenamiento. Por ejemplo: `https://contoso.blob.core.windows.net/b2c/su.html`. Haga clic en **Aceptar** dos veces.
6. Haga clic en **Guardar**.
7. Haga clic en el comando **Ejecutar ahora** en la parte superior de la hoja. Seleccione "Aplicación B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**. Haga clic en el botón **Ejecutar ahora**.
8. Se abrirá una nueva pestaña del explorador y podrá ejecutar por la experiencia del usuario de registrarse para su aplicación con el nuevo contenido implementado.

> [AZURE.NOTE]Tenga en cuenta que se tarda hasta un minuto en que los cambios de directiva surtan efecto.

<!---HONumber=Sept15_HO3-->