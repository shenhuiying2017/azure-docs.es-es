<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Registro de la aplicación | Microsoft Azure"
	description="Registro de la aplicación con Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Registro de la aplicación

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Requisito previo

Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Obtenga su propio inquilino siguiendo los pasos descritos [aquí](active-directory-b2c-get-started.md). Si ha seguido todos los pasos de este artículo, debe tener la hoja de características B2C anclada en el panel de inicio.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navegación a la hoja Características B2C

Puede navegar a la hoja de características B2C de dos maneras.

### 1\. Directamente en el Portal de vista previa de Azure

Si dispone de la hoja de características B2C anclada en el Panel de inicio, la verá en cuanto inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com/) como administrador global del inquilino B2C.

También puede tener acceso a la hoja de características B2C yendo directamente a [https://portal.azure.com/{inquilino}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{inquilino}.onmicrosoft.com](https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com) donde **{inquilino}** debe sustituirse por el nombre usado al crear el inquilino (por ejemplo, contosob2c), e iniciando sesión como administrador global del inquilino B2C. Puede marcar este vínculo para futura referencia (vea la nota siguiente).

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a User from any tenant cannot access it.

### 2\. Acceso a través del Portal de Azure

Inicie sesión en el [Portal de Azure](https://manage.windowsazure.com/) como administrador de suscripciones (que es la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para suscribirse a Azure). Vaya a la extensión de Active Directory de la izquierda y haga clic en el inquilino B2C. En la pestaña **Inicio rápido** (la primera pestaña que se abre), haga clic en **Administrar la configuración B2C** en **Administar**. Se abrirá la hoja de características B2C en una nueva ventana de explorador o en una nueva pestaña.

También puede encontrar el vínculo **Administrar la configuración B2C** (de la sección **Administración B2C**) en la pestaña **Configurar**.

## Registrar una aplicación

1. En la hoja de características B2C en el Portal de vista previa de Azure, haga clic en **Aplicaciones**.
2. Haga clic en **+Agregar** en la parte superior de la hoja.
3. El **Nombre** de la aplicación servirá de descripción de la aplicación para los consumidores. Por ejemplo, escriba "Contoso B2C app".
4. Si va a escribir una aplicación basada en web, mueva el conmutador **Incluir aplicación web/API web** a **Sí**. Las **Direcciones URL de respuesta** son extremos en los que Azure AD B2C devolverá los tokens que solicite su aplicación. Por ejemplo, escriba: `https://localhost:44321/`. Si la aplicación incluye un componente de servidor (API) que se debe proteger, es conveniente que cree (y copie) también un **Secreto de aplicación** haciendo clic en el botón **Generar clave**.

    > [AZURE.NOTE]El **secreto de aplicación** es una credencial de seguridad importante.

5. Si va a escribir una aplicación móvil, mueva el conmutador **Incluir cliente nativo** a **Sí**. Copie el **URI de redirección** predeterminado creado automáticamente para usted.
6. Haga clic en **Crear** para registrar la aplicación.
7. Haga clic en la aplicación que acaba de crear y copie el **Id. de aplicación** único global que usará más adelante en el código.

## Creación de una aplicación de inicio rápido

Ahora que tiene una aplicación de Microsoft registrada en Azure AD B2C, puede realizar uno de nuestros tutoriales de inicio rápido para ponerse en marcha rápidamente. Estas son algunas recomendaciones:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=Oct15_HO3-->