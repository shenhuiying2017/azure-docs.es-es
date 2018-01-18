---
title: "Expiración de grupos de Office 365 en Azure Active Directory | Microsoft Docs"
description: "Configuración de la expiración de grupos de Office 365 en Azure Active Directory (versión preliminar)"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 6b454ed7257e8d3f91e585cee2b559c54371fb15
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Configuración de la expiración de grupos de Office 365 (versión preliminar)

Ahora puede administrar el ciclo de vida de los grupos de Office 365 estableciendo sus características de expiración. Puede configurar la expiración solo para grupos de Office 365 en Azure Active Directory (Azure AD). Una vez establecido un grupo para establecer la expiración:
-   Los propietarios del grupo reciben una notificación para renovar el grupo ya que se acerca la expiración
-   Se eliminará cualquier grupo que no se renueve
-   Los propietarios del grupo o el administrador pueden restaurar, dentro de un plazo de 30 días, los grupos de Office 365 que se hayan eliminado

> [!NOTE]
> Configurar la expiración para los grupos de Office 365 requiere una licencia de Azure AD Premium para todos los miembros de los grupos a los que se aplica la configuración de expiración.

Para obtener información acerca de cómo descargar e instalar los cmdlets de PowerShell de Azure AD, consulte [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell para Graph - Versión preliminar pública 2.0.0.137).

## <a name="set-group-expiration"></a>Establecimiento de la expiración de grupo

1. Abra el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global del inquilino de Azure AD.

2. Abra Azure AD y seleccione **Usuarios y grupos**.

3. Seleccione **Configuración de grupo** y luego, **Expiración** para abrir la configuración de expiración.
  
  ![Hoja Expiración](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. En la hoja **Expiración**, puede:

  * Establecer la duración del grupo en días. Puede seleccionar uno de los valores predeterminados o un valor personalizado (debe ser de 31 días o más). 
  * Especificar una dirección de correo electrónico a la que deben enviarse notificaciones de renovación y expiración cuando el grupo no tiene ningún propietario. 
  * Seleccionar los grupos de Office 365 que expiran. Puede habilitar la expiración de **Todos** los grupos de Office 365, seleccionar de entre los grupos Office 365 o seleccionar **Ninguno** para deshabilitar la expiración de todos los grupos.
  * Guardar la configuración cuando haya terminado seleccionando **Guardar**.


Se envían notificaciones por correo electrónico como esta a los propietarios del grupo de Office 365 con 30 días, 15 días y 1 día de antelación a la expiración del grupo.

![Notificación por correo electrónico de expiración](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

El propietario del grupo puede luego seleccionar **Renew group** (Renovar grupo) para renovar el grupo de Office 365 o seleccionar **Ir al grupo** para ver los miembros del grupo y otros detalles sobre el mismo.

Cuando un grupo expira, el grupo se elimina un día después de la fecha de expiración. Se envía una notificación por correo electrónico como esta a los propietarios del grupo de Office 365 donde se informa sobre la expiración y la posterior eliminación del grupo de Office 365.

![Notificación por correo electrónico de eliminación del grupo](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Para restaurar el grupo, seleccione **Restore group** (Restaurar grupo) o use cmdlets de PowerShell, tal y como se describe en [Restauración de un grupo eliminado de Office 365 en Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Si el grupo que restaura contiene documentos, sitios de SharePoint u otros objetos persistentes, el proceso completo de restauración del grupo y su contenido podría demorar hasta 24 horas.

> [!NOTE]
> * Cuando se configura por primera vez la expiración, todos los grupos que sean más antiguos que el intervalo de expiración se establecen en 30 días hasta la expiración. El primer correo de notificación de renovación se envía en un día. 
>   Por ejemplo, el grupo A se creó hace 400 días y el intervalo de expiración se ha establecido en 180 días. Cuando se aplica la configuración de expiración, el grupo A tiene 30 antes de que se elimine, salvo que el propietario lo renueve.
> * Cuando un grupo dinámico se elimina y restaura, se considera un nuevo grupo y se vuelve a rellenar de acuerdo con la regla. Este proceso puede tardar hasta 24 horas.

## <a name="next-steps"></a>pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos de Azure AD.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Administración de la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
