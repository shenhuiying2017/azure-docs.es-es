---
title: "Creación de una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación con Azure AD | Microsoft Docs"
description: "Aprenda a crear una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación."
services: active-directory
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b2f8985f12e17ac69543cfb3a33725f796eedde8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Creación de una revisión de acceso de los miembros de un grupo o el acceso a la aplicación con Azure AD

Las asignaciones de roles se convierten en "obsoletas" cuando los usuarios tienen acceso con privilegios que ya no necesitan. Para reducir el riesgo derivado de las asignaciones de acceso obsoletas, los administradores pueden usar Azure Active Directory (Azure AD) para crear una revisión de acceso para los miembros de grupo o usuarios asignados a una aplicación. Para más información sobre estos escenarios, vea [Administración del acceso de los usuarios con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) y [Administración del acceso de los invitados con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Creación de una revisión de acceso

1. Como administrador global, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) y seleccione **Programas**.

2. Seleccione el programa que contiene el control de revisión de acceso que desee crear. El **Programa predeterminado** siempre está presente, o puede crear otro. Por ejemplo, puede elegir tener un programa cada para iniciativa de cumplimiento de normas u objetivo empresarial.

3. En el programa, seleccione **Controles** y, a continuación, seleccione **Agregar** para agregar un control.

4. Nombre cada revisión de acceso. Opcionalmente, asigne a cada revisión una descripción. El nombre se muestra a los revisores.

5. Establezca las fechas de inicio y finalización. De forma predeterminada, una revisión de acceso se inicia el mismo día que se crea y finaliza en un mes. Puede cambiar las fechas de inicio y de finalización para hacer que una revisión de acceso se inicie en el futuro, transcurridos tantos días como desee.

6. Las revisiones de acceso pueden ser de los miembros de un grupo o de los usuarios que se hayan asignado a una aplicación. Puede limitar aún más el ámbito de la revisión de acceso para revisar solo los usuarios invitados que sean miembros (o se hayan asignado a la aplicación), en lugar de revisar todos los usuarios miembros o que tengan acceso a la aplicación.

7. Seleccione una o más personas para revisar todos los usuarios en el ámbito. También puede seleccionar que los miembros revisen su propio acceso. Si el recurso es un grupo, puede pedir a sus propietarios que hagan la revisión. También puede requerir que los revisores proporcionen un motivo cuando aprueben el acceso.

8. Por último, seleccione **Iniciar**.


## <a name="manage-the-access-review"></a>Administración de la revisión de acceso

De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión. Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de informar a los revisores de que hay una revisión de acceso esperando para que la lleven a cabo. Puede mostrarles las instrucciones sobre cómo [revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md). Si la revisión es para que los invitados revisen su acceso, muéstreles las instrucciones sobre cómo [revisar su propio acceso](active-directory-azure-ad-controls-perform-access-review.md).

Si alguno de los revisores es un invitado, solo se notificará a los invitados por correo electrónico si ya han aceptado su invitación.


Puede seguir el progreso de las revisiones a medida que los revisores las realizan en el panel de Azure AD, en la sección **Revisiones de acceso**. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Pasos siguientes

Cuando se inicie una revisión de acceso, Azure AD enviará automáticamente a los revisores un correo electrónico solicitándoles que revisen el acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en el articulo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md). 

Una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión de acceso, siga los pasos sobre [cómo realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md) para ver los resultados y aplicarlos.


