---
title: Roles de Azure AD Privileged Identity Management | Microsoft Docs
description: "Obtenga información sobre los roles que se usan para identidades con privilegios con la extensión de Privileged Identity Management de Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: bba26a947607a3679d191a2cd8164d27f61e2ba1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Rol administrativo diferente en Azure Active Directory PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

En su organización, puede asignar a usuarios a roles administrativos diferentes en Azure AD. Estas asignaciones de roles controlan las tareas, como agregar y quitar usuarios o cambiar la configuración de los servicios, que pueden realizar los usuarios en Azure AD, Office 365 y otros servicios Microsoft Online Services y aplicaciones conectadas.  

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD mediante el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal.

Un administrador global puede actualizar qué usuarios están **permanentemente** asignados a roles en Azure AD, mediante cmdlets de PowerShell, como `Add-MsolRoleMember` y `Remove-MsolRoleMember`, o a través del portal, tal como se describe en [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Privileged Identity Management (PIM) de Azure AD administra las directivas de acceso con privilegios para los usuarios en Azure AD. PIM asigna a usuarios a uno o varios roles de Azure AD y puede asignar una persona para que esté permanentemente en el rol o sea apta para el rol. Cuando un usuario se asigna permanentemente a un rol o activa una asignación de rol apta, puede administrar Azure Active Directory, Office 365 y otras aplicaciones con los permisos asignados a su rol.

No hay ninguna diferencia en el acceso proporcionado de forma permanente a una persona o una asignación de roles aptos. La única diferencia es que algunas personas no necesitan ese acceso todo el tiempo. Son aptas para el rol y, pueden activarlas y desactivarlas cada vez que lo necesiten.

## <a name="roles-managed-in-pim"></a>Roles administrados en PIM
Privileged Identity Management (PIM) permite asignar usuarios a roles de administrador comunes, como los siguientes:

* **administrador global** (también conocido como "administrador de la compañía") tiene acceso a todas las características administrativas. Puede tener más de un administrador global en su organización. La persona que se suscribe para comprar Office 365 automáticamente se convierte en un administrador global.
* **administrador de roles con privilegios** administra PIM de Azure AD y actualiza las asignaciones de roles para otros usuarios.  
* **Administrador de facturación** : hace compras, administra suscripciones, administra incidencias de soporte técnico y supervisa el estado del servicio.
* **Administrador de contraseñas** : restablece las contraseñas, administra las solicitudes de servicio y supervisa el estado del servicio. Los administradores de contraseñas están limitados a restablecer las contraseñas para los usuarios.
* **Administrador de servicios** : administra las solicitudes de servicio y supervisa el estado del servicio.
  
  > [!NOTE]
  > Si usa Office 365, antes de asignar el rol de administrador de servicio a un usuario, asigne primero al usuario permisos administrativos a un servicio, como Exchange Online.
  > 
  > 
* **administrador de control de usuarios** restablece las contraseñas, supervisa el estado del servicio y administra cuentas de usuario, grupos de usuarios y solicitudes de servicio. El administrador de usuarios no puede eliminar a un administrador global, crear otros roles de administrador o restablecer contraseñas de los administradores de facturación, globales y de servicios.
* **administrador de Exchange** tiene acceso administrativo a Exchange Online a través del Centro de administración de Exchange (EAC) y puede realizar casi cualquier tarea en Exchange Online.
* **administrador de SharePoint** tiene acceso administrativo a SharePoint Online a través del Centro de administración de SharePoint Online y puede realizar casi cualquier tarea en SharePoint Online.
* **administrador de Skype Empresarial** tiene acceso administrativo a Skype Empresarial a través del Centro de administración de Skype Empresarial y puede realizar casi cualquier tarea en Skype Empresarial Online.

Consulte estos artículos para más información sobre la [asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles-azure-portal.md) y de roles de administrador en [Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


En PIM, puede [asignar estos roles a un usuario](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que pueda [activar el rol cuando lo necesite](active-directory-privileged-identity-management-how-to-activate-role.md).

Si desea dar a otro usuario acceso para administrar en el mismo PIM, los roles que debe tener el usuario para PIM se describen con más detalle en [Concesión de acceso a PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Roles no administrados en PIM
Los roles de Exchange Online o SharePoint Online, excepto los mencionados anteriormente, no están representados en Azure AD y, por lo tanto, no son visibles en PIM. Para obtener más información sobre cómo cambiar las asignaciones de roles específicas de estos servicios de Office 365, consulte [Permisos en Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Las suscripciones de Azure y los grupos de recursos tampoco están representados en Azure AD. Para administrar las suscripciones de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing/billing-add-change-azure-subscription-administrator.md) y para más información sobre Azure RBAC, consulte [Control de acceso basado en roles de Azure](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Roles de usuario e inicio de sesión
En algunos servicios y aplicaciones de Microsoft, asignar a un usuario un rol puede no ser suficiente para permitir que el usuario sea un administrador.

El acceso a Azure Portal requiere que el usuario sea un administrador de servicios o el coadministrador de una suscripción de Azure, incluso si el usuario no necesita administrar las suscripciones de Azure.  Por ejemplo, para administrar la configuración de Azure AD, el usuario debe ser tanto un administrador global de Azure AD como un coadministrador de suscripción en una suscripción de Azure.  Para obtener más información sobre cómo agregar usuarios a las suscripciones de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing/billing-add-change-azure-subscription-administrator.md).

El acceso a Microsoft Online Services puede requerir que se le asigne una licencia al usuario para poder abrir el portal del servicio o realizar tareas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Asignación de una licencia a un usuario en Azure AD
1. Inicie sesión en [Azure Portal](http://portal.azure.com) con una cuenta de administrador global o una cuenta de coadministrador.
3. Seleccione Azure AD y el directorio con el que quiere trabajar y que tiene licencias asociadas.
4. Seleccione **Licencias** a la izquierda. Aparecerá la lista de licencias disponibles.
5. Seleccione el plan de licencias que contiene las licencias que quiere distribuir.
6. Seleccione **Asignar usuarios**.
7. Seleccione el usuario al que quiere asignar una licencia.
8. Haga clic en el botón **Asignar** .  El usuario ahora puede iniciar sesión en Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

