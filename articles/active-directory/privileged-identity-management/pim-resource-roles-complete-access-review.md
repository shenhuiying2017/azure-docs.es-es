---
title: 'Privileged Identity Management para Azure Resources: realización de la revisión de acceso para recursos de Azure | Microsoft Docs'
description: Se describe cómo realizar una revisión de acceso para recursos de Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management: finalización de la revisión de acceso de roles de recursos
Los administradores de roles con privilegios pueden revisar el acceso con privilegios cuando se [ha iniciado una revisión de seguridad](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) para Azure Resources envía automáticamente un correo electrónico para pedir a los usuarios que revisen su acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en [Privileged Identity Management de Azure AD: Realización de una revisión de seguridad](pim-resource-roles-perform-access-review.md).

Después de que acabe el período de revisión de seguridad o de que todos los usuarios hayan finalizado su autorrevisión, siga los pasos de este artículo para administrar la revisión y ver los resultados.

## <a name="manage-security-reviews"></a>Administración de las revisiones de seguridad
1. Vaya a [Azure Portal](https://portal.azure.com/) y seleccione la aplicación de **recursos de Azure** en el panel.
2. Seleccione el recurso.
3. Seleccione la sección **Revisiones de acceso** del panel.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Seleccione la revisión de acceso que quiere administrar.

En la hoja de detalles de la revisión de acceso, hay un número de opciones para administrar dicha revisión.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Todas las revisiones de acceso tienen una fecha de finalización, pero puede usar el botón **Detener** para terminarlas antes. Si para entonces algunos de los usuarios no se han revisado, no podrán hacerlo una vez que se haya detenido la revisión. No se puede reiniciar una revisión después de que se ha detenido.

### <a name="reset"></a>Reset
Puede restablecer una revisión de acceso para quitar todas las decisiones tomadas sobre ella. Una vez que haya restablecido una revisión de acceso, todos los usuarios se marcan de nuevo como no revisados. 

### <a name="apply"></a>Aplicar
Después de que finaliza una revisión de acceso, bien porque se ha llegado a la fecha de finalización o porque se ha detenido manualmente, el botón **Aplicar** implementa el resultado de la revisión. Si se denegó el acceso de un usuario en la revisión, este es el paso que se quitará su asignación de rol.  

### <a name="delete"></a>Eliminar
Si ya no le interesa más la revisión, elimínela. El botón **Eliminar** quita la revisión de la aplicación PIM.

## <a name="results"></a>Results
Vea y descargue una lista de los resultados de la revisión en la pestaña Resultados. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores
Vea y agregue revisores a la revisión de acceso existente. Recuerde a los revisores que realicen sus revisiones.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



