---
title: "Administración del acceso de los usuarios con las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Obtenga información acerca de cómo administrar el acceso de los usuarios, como la pertenencia a un grupo o la asignación a una aplicación con revisiones de acceso de Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Administración del acceso de los usuarios con las revisiones de acceso de Azure AD

Con Azure Active Directory, se puede asegurar fácilmente que los usuarios tienen el acceso adecuado, solicitando a los usuarios mismos o a alguien con capacidad de toma de decisiones, su participación en una revisión de acceso para recertificar (o "avalar") el acceso de los usuarios.  Los revisores pueden dar su aprobación para cada necesidad de acceso continuado de los usuarios, en función de las sugerencias de Azure AD. Cuando finaliza una revisión de los accesos, es posible hacer cambios y retirar accesos a los usuarios que ya no lo necesitan.

> [!NOTE]
> Si desea revisar solo el acceso de los usuarios invitados y no revisar el acceso de todos los tipos de usuarios, consulte cómo [administrar el acceso de usuarios invitados con revisiones de acceso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).  Si desea revisar la pertenencia del usuario a roles administrativos tales como administrador global, consulte [Cómo iniciar una revisión de acceso en Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Requisitos previos 

Las revisiones de acceso están disponibles con la edición Premium P2 de Azure Active Directory, incluida en EMS E5. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).  Cada usuario que interactúa con esta característica, para crear una revisión, revisar el acceso o aplicar una revisión, requiere una licencia.


## <a name="creating-and-performing-an-access-review"></a>Cómo crear y realizar una revisión de acceso

Puede tener uno o más usuarios como revisores en una revisión de acceso.  

1. Seleccione un grupo en Azure Active Directory que tenga uno o más miembros o bien una aplicación conectada a Azure Active Directory que tenga uno o más usuarios asignados a ella. 
2. Decida si cada usuario revisará su propio acceso o bien si uno o más usuarios revisarán el acceso de todos.
3. Habilite las revisiones de acceso para que aparezcan en los paneles de acceso del revisor.  Como administrador global, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Inicie la revisión de acceso. Para más información, consulte [cómo crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).
5. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibirá un correo electrónico de Azure AD con un vínculo al panel de acceso, en el que podrán [realizar la revisión de acceso](active-directory-azure-ad-controls-perform-access-review.md).
6. Si los revisores no han proporcionado información, puede pedir a Azure AD que les envíe un recordatorio.  De forma predeterminada, Azure AD enviará automáticamente un recordatorio hacia la mitad del tiempo hasta la fecha de finalización a los revisores que no hayan respondido todavía.
7. Cuando los revisores hayan proporcionado sus datos de entrada, detenga la revisión de acceso y aplique los cambios. Para más información, consulte cómo [realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Pasos siguientes

- [Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)




