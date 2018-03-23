---
title: Expiración de grupos de Office 365 en Azure Active Directory | Microsoft Docs
description: Configuración de la expiración de grupos de Office 365 en Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 95593eaacd73316ab527ffda8f977fbf0eb15558
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configuración de la directiva de expiración de grupos de Office 365

Ahora puede administrar el ciclo de vida de los grupos de Office 365 mediante el establecimiento de una directiva de expiración. Solo puede configurar la directiva de expiración para grupos de Office 365 en Azure Active Directory (Azure AD). 

Una vez establecido un grupo para establecer la expiración:
-   Los propietarios del grupo reciben una notificación para renovar el grupo ya que se acerca la expiración
-   Se eliminará cualquier grupo que no se renueve
-   Los propietarios del grupo o el administrador pueden restaurar, dentro de un plazo de 30 días, los grupos de Office 365 que se hayan eliminado

> [!NOTE]
> Para configurar y usar la directiva de expiración en grupos de Office 365, es necesario tener a mano las licencias de Azure AD Premium de todos los miembros de los grupos a los que se aplica la directiva de expiración.

Para más información sobre cómo descargar e instalar los cmdlets de PowerShell de Azure AD, consulte [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell para Graph 2.0.0.137).

## <a name="roles-and-permissions"></a>Roles y permisos
Estos son los roles en los que puede configurar y usar la expiración de grupos de Office 365 en Azure AD.

Rol | Permisos
-------- | --------
Administrador global o administrador de cuentas de usuario | Puede crear, consultar, actualizar o eliminar la configuración de la directiva de expiración de grupos de Office 365.<br>Puede renovar cualquier grupo de Office 365
Usuario | Puede renovar un grupo de Office 365 de su propiedad.<br>Puede restaurar un grupo de Office 365 de su propiedad.<br>Puede leer la configuración de la directiva de expiración

Si necesita más información sobre los permisos para restaurar los grupos eliminados, consulte [Restauración de un grupo eliminado de Office 365 en Azure Active Directory](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Establecimiento de la expiración de grupo

1. Abra el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global del inquilino de Azure AD.

2. Seleccione **Grupos** y, luego, **Expiración** para abrir la configuración de expiración.
  
  ![Hoja Expiración](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. En la hoja **Expiración**, puede:

  * Establecer la duración del grupo en días. Puede seleccionar uno de los valores predeterminados o un valor personalizado (debe ser de 31 días o más). 
  * Especificar una dirección de correo electrónico a la que deben enviarse notificaciones de renovación y expiración cuando el grupo no tiene ningún propietario. 
  * Seleccionar los grupos de Office 365 que expiran. Puede seleccionar la opción **Todos** para habilitar la expiración en todos los grupos de Office 365, la opción **Seleccionados** para habilitarla en algunos grupos o la opción **Ninguno** para deshabilitarla en todos los grupos.
  * Guardar la configuración cuando haya terminado seleccionando **Guardar**.


Se envían notificaciones por correo electrónico como esta a los propietarios del grupo de Office 365 con 30 días, 15 días y 1 día de antelación a la expiración del grupo.

![Notificación por correo electrónico de expiración](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

En el correo electrónico de notificación **Renovar grupo**, los propietarios de los grupos pueden acceder directamente a la página de detalles del grupo en el Panel de acceso. Ahí, los usuarios pueden obtener más información sobre el grupo, como su descripción, cuando se renovó por última vez, cuándo expira y la posibilidad de renovarlo. La página de detalles del grupo ahora incluye también vínculos a los recursos de grupos de Office 365, así el propietario del grupo puede ver cómodamente el contenido y la actividad de su grupo.

Cuando un grupo expira, el grupo se elimina un día después de la fecha de expiración. Se envía una notificación por correo electrónico como esta a los propietarios del grupo de Office 365 donde se informa sobre la expiración y la posterior eliminación del grupo de Office 365.

![Notificación por correo electrónico de eliminación del grupo](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

El grupo se puede restaurar a los 30 días de su eliminación mediante la selección de **Restaurar grupo** o por medio de los cmdlets de PowerShell, tal y como se describe en [Restauración de un grupo eliminado de Office 365 en Azure Active Directory](active-directory-groups-restore-azure-portal.md).
    
Si el grupo que restaura contiene documentos, sitios de SharePoint u otros objetos persistentes, el proceso completo de restauración del grupo y su contenido podría demorar hasta 24 horas.

> [!NOTE]
> * Cuando se configura por primera vez la expiración, todos los grupos que sean más antiguos que el intervalo de expiración se establecen en 30 días hasta la expiración. El primer correo de notificación de renovación se envía en un día. 
>   Por ejemplo, el grupo A se creó hace 400 días y el intervalo de expiración se ha establecido en 180 días. Cuando se aplica la configuración de expiración, el grupo A tiene 30 antes de que se elimine, salvo que el propietario lo renueve.
> * Actualmente, solo se puede configurar una directiva de expiración para grupos de Office 365 en un inquilino.
> * Cuando un grupo dinámico se elimina y restaura, se considera un nuevo grupo y se vuelve a rellenar de acuerdo con la regla. Este proceso puede tardar hasta 24 horas.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Cómo funciona la expiración de grupos de Office 365 con un buzón en suspensión legal
Cuando un grupo expira y se elimina, 30 días después de su eliminación se eliminan de forma permanente los datos del grupo de aplicaciones como Planner, Sitios o Equipos, pero el buzón del grupo que está en suspensión legal se conserva y no se elimina de forma permanente. El administrador puede usar cmdlets de Exchange para restaurar el buzón para capturar los datos. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Cómo funciona la expiración de grupos de Office 365 con la directiva de retención
La directiva de retención se configura por medio del Centro de seguridad y cumplimiento. Si ha configurado una directiva de retención para grupos de Office 365, cuando un grupo expira y se elimina, sus conversaciones en el buzón de correo del grupo y los archivos en el sitio del grupo se conservan en el contenedor de retención durante los días especificados en la directiva de retención. Los usuarios no verán el grupo ni su contenido tras la expiración, pero pueden recuperar los datos del sitio y del buzón mediante eDiscovery.

## <a name="powershell-examples"></a>Ejemplos de PowerShell
Estos son ejemplos de cómo puede usar cmdlets de PowerShell para configurar los valores de expiración de los grupos de Office 365 en su inquilino:

1. Instale el módulo de versión preliminar de PowerShell v2.0 (2.0.0.137) e inicie sesión en el símbolo del sistema de PowerShell:
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. Configure los valores de expiración con New-AzureADMSGroupLifecyclePolicy: este cmdlet establece la duración de todos los grupos de Office 365 en el inquilino en 365 días. Las notificaciones de renovación de los grupos de Office 365 sin propietario se envían a "emailaddress@contoso.com".
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. Recupere la directiva existente con Get-AzureADMSGroupLifecyclePolicy: este cmdlet recupera la configuración actual de expiración de grupos de Office 365 que se ha definido. En este ejemplo, puede ver:
  * El identificador de directiva. 
  * La duración de todos los grupos de Office 365 en el inquilino se establece en 365 días.
  * Las notificaciones de renovación de grupos de Office 365 sin propietario se envían a "emailaddress@contoso.com".
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. Actualice la directiva existente con Set-AzureADMSGroupLifecyclePolicy: este cmdlet se usa para actualizar una directiva existente. En el ejemplo siguiente, se cambia la duración del grupo de la directiva existente de 365 días a 180 días. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Agregue grupos específicos a la directiva con Add-AzureADMSLifecyclePolicyGroup: este cmdlet agrega un grupo a la directiva de ciclo de vida. Por ejemplo: 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Quite la directiva existente con Remove-AzureADMSGroupLifecyclePolicy: este cmdlet elimina la configuración de expiración de los grupos de Office 365, pero requiere el identificador de directiva. Esto deshabilita la expiración de grupos de Office 365. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
Los siguientes cmdlets se pueden usar para configurar la directiva con más detalle. Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos de Azure AD.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Administración de la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
