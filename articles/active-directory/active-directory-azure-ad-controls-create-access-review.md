---
title: "Creación de una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación con Azure AD | Microsoft Docs"
description: "Aprenda a crear una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación."
services: active-directory
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
ms.openlocfilehash: c2c5aee3db11255be5a6fe405424c2cbb92c5b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Creación de una revisión de acceso de los miembros de un grupo o el acceso a la aplicación con Azure AD

Las asignaciones de roles se convierten en "obsoletas" cuando los usuarios tienen acceso con privilegios que ya no necesitan.  Para reducir el riesgo derivado de las asignaciones de acceso obsoletas, los administradores pueden solicitar una revisión de los miembros del grupo o de los usuarios asignados a una aplicación creando una revisión de acceso. Las guías para [administrar el acceso de los usuarios](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) y [administrar el acceso de los invitados](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) proporcionan más información acerca de estos escenarios.  

## <a name="how-to-create-an-access-review"></a>Creación de una revisión de acceso


1. Como administrador global, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) y cambie a la pestaña **Programas**.
2. Haga clic en el programa que contendrá el control de revisión de acceso que desea crear.  Hay un programa, "Programa predeterminado", siempre presente, o puede crear otro.  Por ejemplo, puede elegir tener un programa cada para iniciativa de cumplimiento de normas u objetivo empresarial.
3. Dentro del programa, haga clic en Controles y, a continuación, haga clic en el botón **Agregar** para agregar un control.
4. Cada revisión de acceso debe tener un nombre y, si lo desea, también una descripción.  El nombre se muestra a los revisores.  
5. De forma predeterminada, una revisión de acceso se inicia el mismo día que se crea y está programada para finalizar en un mes.  Puede cambiar las fechas de inicio y de finalización para hacer que una revisión de acceso se inicie en el futuro, transcurridos tantos días como desee.
6. Las revisiones de acceso pueden ser de los miembros de un grupo o de los usuarios que se hayan asignado a una aplicación.  Puede limitar aún más el ámbito de la revisión de acceso para revisar solo los usuarios invitados que sean miembros (o se hayan asignado a la aplicación), en lugar de revisar todos los usuarios miembros o que tengan acceso a la aplicación.
7. Puede seleccionar una o varias personas para revisar todos los usuarios del ámbito o hacer que los miembros revisen su propio acceso.  Si el recurso es un grupo, puede pedir a sus propietarios que hagan la revisión.  También se puede exigir que los revisores proporcionen un motivo al aprobar el acceso.
8. Finalmente, haga clic en **Iniciar**.


## <a name="managing-the-access-review"></a>Administración de la revisión de acceso

De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión.  Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de que los revisores sepan que hay una revisión de acceso esperando para que la lleven a cabo.  Puede mostrarles instrucciones para [revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md), o, si la revisión es para que los invitados revisen su acceso, instrucciones para [revisar su propio acceso](active-directory-azure-ad-controls-perform-access-review.md).

Tenga en cuenta que, si alguno de los revisores es un invitado, solo se notificará a los invitados por correo electrónico si ya han aceptado su invitación.


Puede seguir el progreso de las revisiones a medida que los revisores las realizan en el panel de Azure AD, en la sección de revisiones de acceso. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Pasos siguientes

Cuando se inicie una revisión de acceso, Azure AD enviará automáticamente a los revisores un correo electrónico solicitándoles que revisen el acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en el articulo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).  

Una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión, siga los pasos descritos en el artículo sobre [cómo realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md) para ver los resultados y aplicarlos.


