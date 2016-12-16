---
title: "Formato de archivo CSV para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Docs"
description: Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los partners empresariales accedan de forma selectiva a las aplicaciones corporativas.
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: bd2c5364-4164-407d-ac25-34088c175c4a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ed32c23b8c69664dd75eae9c831341c93e57ebb3


---
# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Vista previa de la colaboración B2B de Azure AD: formato de archivo CSV
La versión de vista previa de la colaboración de Azure AD B2B requiere un archivo CSV en el que se especifique la información del usuario del asociado. Dicho archivo CSV debe cargarse mediante el Portal de Azure AD. El archivo CSV debe incluir las siguientes etiquetas obligatorias, así como determinados campos opcionales según sea necesario. Modifique el archivo CSV de ejemplo (abajo) sin cambiar el texto de las etiquetas de la primera fila.

> [!NOTE]
> La primera fila de etiquetas (por ejemplo, Email, DisplayName, etc.) es necesaria para que el archivo CSV se analice correctamente. El nombre de las etiquetas debe coincidir exactamente con los campos que se especifican a continuación. Estas etiquetas identifican el contenido de las columnas. En el caso de los campos opcionales que no son obligatorios, las etiquetas se pueden eliminar del archivo CSV. La columna entera puede dejarse vacía.
> 
> 

## <a name="required-fields-br"></a>Campos obligatorios:  <br/>
**Email:** dirección de correo electrónico del usuario invitado. <br/>
**DisplayName:** el nombre para mostrar del usuario invitado (normalmente, el nombre y los apellidos).<br/>

## <a name="optional-fields-br"></a>Campos opcionales:  <br/>
**InvitationText:** adapte el texto del correo electrónico de invitación después de la personalización de marca de la aplicación y antes del vínculo de canje.<br/>
**InvitedToApplications:** los AppID para que las aplicaciones corporativas asignen usuarios. Los AppID son recuperables en PowerShell mediante una llamada a `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`.<br/>
**InvitedToGroups:** ObjectID de los grupos a los que agregar usuarios. Los ObjectID son recuperables en PowerShell mediante la llamada a `Get-MsolGroup | fl DisplayName, ObjectId`.<br/>
**InviteReplyURL:** dirección URL a la que se dirigirá un usuario invitado después de la aceptación de la invitación. Debería ser una URL específica de la compañía (por ejemplo [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si no se especifica este campo opcional, el usuario invitado se dirige al panel de acceso a las aplicaciones desde el que puede acceder a las aplicaciones corporativas seleccionadas. La URL del panel de acceso a la aplicación tiene el formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: dirección de correo electrónico a la que se enviará una copia del correo electrónico de invitación. Si se usa el campo CcEmailAddress, esta invitación no se podrá emplear para crear usuarios o inquilinos comprobados por correo electrónico.<br/>
**Language:** el idioma del correo electrónico de invitación y la experiencia de canje. Si no se especifica otro valor, se usa "en" (inglés) como predeterminado. Los otros 10 códigos de idioma compatibles son los siguientes:<br/>

1. de: alemán<br/>
2. es: español<br/>
3. fr: francés<br/>
4. it: italiano<br/>
5. ja: japonés<br/>
6. ko: coreano<br/>
7. pt-BR: portugués (Brasil)<br/>
8. ru: ruso<br/>
9. zh-HANS: chino simplificado<br/>
10. zh-HANT: chino tradicional<br/>

## <a name="sample-csv-file"></a>Archivo CSV de ejemplo
Este es un archivo CSV de ejemplo que puede modificar.

> [!NOTE]
> Copie y pegue esto en el Bloc de notas y guárdelo con una extensión de archivo .csv. A continuación, realice ediciones en Excel. Se estructurará en una tabla con las etiquetas en la primera fila.
> 
> Puede agregar más campos opcionales en Excel especificando la etiqueta y rellenando la columna situada debajo.
> 
> 

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Artículos relacionados
Consulte otros artículos nuestros sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Cómo funciona](active-directory-b2b-how-it-works.md)
* [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
* [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
* [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


