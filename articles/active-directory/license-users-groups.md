---
title: Licencia de usuarios en Azure Active Directory | Microsoft Docs
description: "Obtención de una licencia para usted y sus usuarios en Azure Active Directory."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: bc210b83a9eeb947a15b60548e43096bd9e11c45
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Inicio rápido: Licencia de usuarios en Azure Active Directory
Los servicios de Azure AD basados en licencia funcionan mediante la activación de una suscripción de Azure Active Directory (Azure AD) en el inquilino de Azure. Una vez activa la suscripción, los administradores de Azure AD pueden administrar las funcionalidades del servicio y los usuarios con licencia pueden usarlas. Cuando compra Enterprise Mobility + Security, Azure AD Premium o Azure AD Basic, el inquilino se actualiza con la suscripción, lo que incluye su período de validez y las licencias de prepago. La información de suscripción, incluido el número de licencias asignadas o disponibles, está disponible a través de Azure Portal en **Azure Active Directory** abriendo el icono **Licencias**. La hoja **Licencias** es también el mejor lugar para administrar las asignaciones de licencias.

Aunque para configurar las funcionalidades de pago todo lo que necesita es una suscripción, debe asignar licencias a los usuarios para las características de pago de Azure AD. Se debe asignar una licencia a los usuarios que deban disponer de acceso o que puedan administrarse a través de una característica de pago de Azure AD. Una asignación de licencia es una asignación entre un usuario y un servicio comprado, como Azure AD Premium, Basic o Enterprise Mobility + Security.

Puede usar [asignación de licencias basada en grupo](active-directory-licensing-whatis-azure-portal.md) para configurar reglas como las siguientes:
* Todos los usuarios del directorio reciben automáticamente una licencia
* Todos los usuarios con el puesto adecuado reciben una licencia
* Puede delegar la decisión en otros administradores de la organización (mediante [grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)).

> [!TIP]
> Para ver una explicación detallada sobre la asignación de licencias a grupos, incluidos escenarios avanzados y escenarios de concesión de licencias de Office 365, consulte [Asignación de licencias a usuarios según su pertenencia a un grupo en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Asignación de licencias a usuarios y grupos
Con una suscripción activa, debe asignarse una licencia a sí mismo en primer lugar y actualizar el explorador para asegurarse de que se vean todas las características esperadas incluidas con su suscripción. El siguiente paso es asignar licencias a los usuarios que necesitarán acceso a las características de pago de Azure AD. Una manera sencilla de asignar licencias es asignar licencias a grupos de usuarios en lugar de a usuarios. Al asignar licencias a un grupo, todos los miembros del grupo reciben una licencia. Si se agregan usuarios al grupo o se quitan de él, la licencia correspondiente se asigna o se quita automáticamente. 

> [!NOTE]
> Algunos servicios de Microsoft no están disponibles en todas las ubicaciones. Para poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad **Ubicación de uso** para el usuario. Esta propiedad se puede establecer en **Usuario** &gt; **Perfil** &gt; **Configuración** en Azure Portal. Cuando se usa la asignación de licencias de grupo, los usuarios cuya ubicación de uso no se especifique heredan la ubicación del directorio.

Para asignar una licencia, en **Azure Active Directory** &gt; **Licencias** &gt; **Todos los productos**, seleccione uno o varios productos y luego seleccione **Asignar** en la barra de comandos.

![Selección de una licencia para asignar](media/license-users-groups/select-license-to-assign.png)

Puede usar la hoja **Usuarios y grupos** para elegir varios usuarios o grupos o para deshabilitar planes de servicio en el producto. Use el cuadro de búsqueda de la parte superior para buscar nombres de usuarios y grupos.

![Selección de un usuario o grupo para la asignación de licencias](media/license-users-groups/select-user-for-license-assignment.png)

Al asignar licencias a un grupo, puede pasar algún tiempo antes de que todos los usuarios hereden la licencia, en función del tamaño del grupo. El estado de procesamiento se puede comprobar en la hoja **Grupo**, en el icono de **Licencias**.

![Estado de asignación de licencias](media/license-users-groups/license-assignment-status.png)

Se pueden producir errores de asignación durante la asignación de licencias de Azure AD, pero son relativamente poco frecuentes al administrar productos de Azure AD y Enterprise Mobility + Security. Los posibles errores de asignación se limitan a los siguientes:
- Conflicto de asignación: cuando se asignó previamente a un usuario una licencia incompatible con la actual. En este caso, para asignar la nueva licencia será necesario quitar la anterior.
- Número excesivo de licencias disponibles: cuando el número de usuarios en los grupos asignados supera las licencias disponibles, el estado de la asignación de los usuarios reflejará un error de asignación debido a que faltan licencias.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Concesión de licencias de colaboración B2B de Azure AD

La colaboración B2B permite invitar a usuarios invitados a su inquilino de Azure AD para proporcionarles acceso a los servicios de Azure AD y a los recursos de Azure que estén disponibles.  

No se aplica ningún cargo por invitar a los usuarios de B2B ni por asignarlos a una aplicación en Azure AD. Hasta 10 aplicaciones por usuario invitado y 3 informes básicos también son gratuitos para los usuarios de colaboración B2B. Si un usuario invitado tiene las licencias pertinentes asignadas en el inquilino de Azure AD del asociado, también las tendrá en el suyo.

Aunque no es necesario, si quiere proporcionar acceso a características de pago de Azure AD, a esos usuarios invitados de B2B se les deben proporcionar las licencias de Azure AD adecuadas. Un inquilino que invita con una licencia de pago de Azure AD puede asignar derechos de usuario de colaboración de B2B a cinco usuarios invitados adicionales al inquilino. Para ver escenarios e información, consulte [Guía de concesión de licencias de colaboración B2B](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Visualización de licencias asignadas

En **Azure Active Directory** &gt; **Licencias** &gt; **Todos los productos** se muestra una vista de resumen de las licencias asignadas y disponibles.

![Visualización del resumen de licencias](media/license-users-groups/view-license-summary.png)

Al seleccionar un producto específico aparece una lista detallada de los usuarios y grupos asignados. En la lista **Usuarios con licencia** se muestran todos los usuarios actualmente con licencia, tanto si la licencia se asignó directamente al usuario como si se heredó de un grupo.

![Visualización de los detalles de licencia](media/license-users-groups/view-license-detail.png)

De forma similar, en la lista **Grupos con licencias** se muestran todos los grupos con licencia asignada. Seleccione un usuario o grupo para abrir la hoja **Licencias**, que muestra todas las licencias asignadas a ese objeto.

## <a name="remove-a-license"></a>Eliminación de una licencia

Para quitar una licencia, vaya al usuario o grupo y abra el icono de **Licencias**. Seleccione la licencia y haga clic en **Quitar**.

![Eliminación de una licencia](media/license-users-groups/remove-license.png)

Las licencias heredadas por el usuario de un grupo no se pueden quitar directamente. En su lugar, quite el usuario del grupo desde el que haya heredado la licencia.


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo asignar licencias a usuarios y grupos en el directorio de Azure AD. 

Puede usar el vínculo siguiente para configurar las asignaciones de licencias de suscripción de Azure AD desde Azure Portal.

> [!div class="nextstepaction"]
> [Asignación de licencias de Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 