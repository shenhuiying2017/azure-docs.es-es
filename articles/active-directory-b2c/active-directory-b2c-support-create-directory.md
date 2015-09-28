<properties
	pageTitle="Azure Active Directory: creación de un tema de soporte técnico de directorio | Microsoft Azure"
	description="Creación de un directorio de Azure Active Directory o un directorio de Azure Active Directory B2C: problemas y soluciones"
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

# Creación de un directorio de Azure AD o un directorio de Azure AD B2C: problemas y soluciones

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de un directorio de Azure AD

Si no puede crear un directorio de Azure AD la primera vez, vuelva a intentarlo. Si el problema persiste, póngase en contacto con el servicio de soporte técnico.

## Creación de un directorio de Azure AD B2C (vista previa)

Se sabe de varios problemas que puedan surgir durante la [creación de un directorio de Azure AD B2C](active-directory-b2c-get-started).

- Si el directorio de Azure AD B2C no aparece en la lista de directorios, inténtelo de nuevo.
- Si el directorio de Azure AD B2C no aparece en la lista de directorios, pero ve un mensaje de error que dice "No se pudo completar la creación del directorio B2C 'contosob2c'. Visite este [vínculo](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obtener más instrucciones", lleve a cabo una de las siguientes acciones:
    - Vaya a la hoja de características B2C del Portal de vista previa de Azure mediante este vínculo [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com), donde **{directory}** se sustituirá por el nombre usado en el momento de creación del directorio (por ejemplo, contosob2c) e inicie sesión como administrador global. Haga clic en **Habilitar características B2C** en la parte superior de la hoja y haga clic en **Aceptar**. Su directorio de Azure AD B2C deberá estar listo para usarse.
	- Elimine el directorio que acaba de crear y vuelva a intentarlo.
- Si ninguna de las soluciones anteriores funciona, póngase en contacto con el soporte técnico. Lea [este artículo](active-directory-b2c-support.md) sobre cómo presentar solicitudes de soporte técnico para Azure AD B2C.

<!---HONumber=Sept15_HO3-->