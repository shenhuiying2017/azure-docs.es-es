---
title: Cmdlets de Azure Active Directory para configurar las opciones de grupo | Microsoft Docs
description: "Administración de la configuración de grupos mediante cmdlets de Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c18ef38661e31e16114b88bdfad36320776ef12b
ms.lasthandoff: 12/28/2016


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets de Azure Active Directory para configurar las opciones de grupo
La siguiente configuración de grupos unificados se puede aplicar en su directorio:

1. Clasificaciones: lista de clasificaciones separadas por coma que pueden configurar los usuarios en un grupo. Por ejemplo "Classified" (Sin clasificar), "Secret" (Secreto) y "Top Secret" (Alto secreto).
2. Usage Guidelines URL (URL de instrucciones de uso): una dirección URL que lleva a los usuarios a los términos de uso de los grupos unificados, según se define en su organización. Esta dirección URL se mostrará en la interfaz de usuario donde los usuarios utilizan grupos.
3. Group creation enabled (Creación de grupos habilitada): si a ningún usuario, a algunos o a todos ellos se les permite crear grupos unificados. Cuando se establece en activado, todos los usuarios pueden crear grupos. Cuando se establece en desactivado, ningún usuario puede crear grupos. Cuando esta opción está desactivada, también puede especificar un grupo de seguridad a cuyos usuarios se les siga permitiendo crear grupos.

Estas opciones se configuran mediante objetos Settings y SettingsTemplate. Inicialmente, no verá ningún objeto Settings en el directorio. Esto significa que el directorio está configurado con los valores predeterminados. Para cambiar la configuración predeterminada, creará un nuevo objeto de configuración mediante una plantilla de configuración. Las plantillas de configuración las define Microsoft.

Puede descargar el módulo que contiene los cmdlets usados para estas operaciones en el [sitio de Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Creación de una configuración en el nivel de directorio
Con estos pasos se crean configuraciones en el nivel de directorio, las cuales se aplican a todos los grupos de Office del directorio.

1. Si no sabe qué objeto SettingTemplate debe usar, este cmdlet devuelve la lista de plantillas de configuración:

    `Get-MsolAllSettingTemplate`

    ![Lista de plantillas de configuración](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. Para agregar una dirección URL de instrucciones de uso, primero debe obtener el objeto SettingsTemplate que define el valor pertinente, es decir, la plantilla Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. A continuación, cree un nuevo objeto de configuración basado en esa plantilla:

    `$setting = $template.CreateSettingsObject()`
4. Luego, actualice el valor de instrucciones de uso:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. Por último, aplique la configuración:

    `New-MsolSettings –SettingsObject $setting`

    ![Agregar una dirección URL de instrucción de uso](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Esta es la configuración definida en el objeto SettingsTemplate Group.Unified.

| **Configuración** | **Descripción** |
| --- | --- |
|  <ul><li>ClassificationList<li>Tipo: String<li>Valor predeterminado: “” |Una lista de valores de clasificación delimitados por coma que se puede aplicar a grupos unificados. |
|  <ul><li>EnableGroupCreation<li>Tipo: Boolean<li> Valor predeterminado: True. |Marca que indica si se permite la creación de grupos unificados en el directorio. |
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: String<li>Valor predeterminado: “” |GUID del grupo de seguridad al que se permite crear grupos unificados incluso cuando EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: String<li>Valor predeterminado: “” |Un vínculo a las instrucciones de uso de grupos. |

## <a name="read-settings-at-the-directory-level"></a>Lectura de la configuración en el nivel de directorio
Con estos pasos se lee la configuración en el nivel de directorio, la cual se aplica a todos los grupos de Office del directorio.

1. Lea toda la configuración de directorio existente:

    `Get-MsolAllSettings`
2. Lea toda la configuración de un grupo específico:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. Lea la configuración de directorio específica, mediante el GUID de SettingId:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID de identificador de configuración](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Actualización de la configuración en el nivel de directorio
Con estos pasos se actualiza la configuración en el nivel de directorio, la cual se aplica a todos los grupos de Office del directorio.

1. Obtenga el objeto Settings existente:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. Obtenga el valor que quiere actualizar:

    `$value = $Setting.GetSettingsValue()`
3. Actualice el valor:

    `$value["AllowToAddGuests"] = "false"`
4. Actualice la configuración:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Eliminación de la configuración en el nivel de directorio
Con estos pasos se elimina la configuración en el nivel de directorio, lo cual se aplica a todos los grupos de Office del directorio.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Referencia de sintaxis de cmdlet
Puede encontrar más documentación de Azure Active Directory PowerShell en el artículo sobre los [cmdlets de Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Referencia de objeto SettingsTemplate (objeto Group.Unified de SettingsTemplate)
* "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "Una marca booleana que indica si la característica de creación de grupos unificados está activada".
* "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "GUID del grupo de seguridad que está en la lista de permitidos para crear grupos unificados".
* "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "Una lista de valores de clasificación válidos delimitados por coma que se pueden aplicar a grupos unificados".
* "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "Un vínculo a las instrucciones de uso de grupos".

| name | type | defaultValue | Descripción |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"System.Boolean" |"true" |"Una marca booleana que indica si la característica de creación de grupos unificados está activada". |
| "GroupCreationAllowedGroupId" |"System.Guid" |"" |"GUID del grupo de seguridad que está en la lista de permitidos para crear grupos unificados". |
| "ClassificationList" |"System.String" |"" |"Una lista de valores de clasificación delimitados por coma que se pueden aplicar a grupos unificados". |
| "UsageGuidelinesUrl" |"System.String" |"" |"Un vínculo a las instrucciones de uso de grupos". |

## <a name="next-steps"></a>Pasos siguientes
Puede encontrar más documentación de Azure Active Directory PowerShell en el artículo sobre los [cmdlets de Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Se pueden encontrar instrucciones adicionales en [Rob's Groups Blog](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad), el blog de Rob Jong, jefe de programas de Microsoft.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

