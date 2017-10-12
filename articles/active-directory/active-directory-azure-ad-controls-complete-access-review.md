---
title: "Realización de una revisión de acceso de los miembros de un grupo o del acceso de un usuario a una aplicación con Azure AD | Microsoft Docs"
description: "Aprenda a realizar una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación en Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Realización de una revisión de acceso de los miembros de un grupo o del acceso de un usuario a una aplicación con Azure AD

Los administradores pueden solicitar una revisión de los miembros del grupo o los usuarios asignados a una aplicación [creando una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md). Azure AD envía automáticamente a los revisores un correo electrónico que les solicita que revisen el acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en el articulo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).  Una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión de acceso, siga los pasos descritos en este artículo para ver los resultados y aplicarlos.

## <a name="viewing-an-access-review-in-the-azure-portal"></a>Visualización de una revisión de acceso en Azure Portal

1. Vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), cambie a la pestaña **Programas** y seleccione el programa que contenga el control de revisión de acceso.
2. Haga clic en Administrar y seleccione el control de revisión de acceso.  Si hay varios controles en el programa, puede filtrar solo los controles de un tipo específico, ordenar por su estado o buscar por el nombre del control de la revisión de acceso o por el nombre para mostrar del propietario que lo creó. 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>Detención de una revisión que no se ha completado

Si la revisión aún no ha alcanzado la fecha de finalización programada, un administrador puede detenerla haciendo clic en el botón **Detener** para terminarla antes.  Si para entonces algunos de los usuarios no se han revisado, no podrán hacerlo una vez que se haya detenido la revisión. No se puede reiniciar una revisión después de que se ha detenido.

## <a name="applying-the-changes"></a>Aplicación de los cambios 

Una vez completa una revisión de acceso, bien porque se haya llegado a la fecha de finalización o porque el administrador la haya detenido manualmente, el botón **Aplicar** implementa el resultado de la revisión, actualizando al grupo o la aplicación. Si el acceso de un usuario se denegó en la revisión, cuando un administrador haga clic en este botón, Azure AD quitará la asignación de pertenencia o de la aplicación. 

Hacer clic en el botón Aplicar no tendrá efecto para un grupo que se origine en un directorio local o en un grupo dinámico.  Si desea cambiar un grupo que se origina en un directorio local, descargue los resultados y aplique esos cambios a la representación del grupo en ese directorio.

## <a name="downloading-the-results-of-the-review"></a>Descarga de los resultados de la revisión

Para recuperar los resultados de la revisión, cambie a la pestaña Aprobaciones y haga clic en el botón **Descargar**.  El archivo CSV resultante puede verse en Excel o en otros programas que abren archivos CSV.

## <a name="optional-deleting-a-review"></a>Opcional: eliminación de una revisión
Si ya no le interesa más la revisión, elimínela. El botón **Eliminar** quita la revisión de Azure AD.

> [!IMPORTANT]
> No se produce ninguna advertencia antes de la eliminación, así que asegúrese de que realmente quiere eliminar esa revisión.
> 
> 

## <a name="next-steps"></a>Pasos siguientes

- [Administración del acceso de los usuarios con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Administración del acceso de los invitados con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Administración de los programas y los controles para las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)
- [Creación de una revisión de acceso de los usuarios en un rol administrativo de Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
