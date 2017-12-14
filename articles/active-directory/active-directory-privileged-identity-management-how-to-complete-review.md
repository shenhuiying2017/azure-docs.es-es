---
title: "Revisión de acceso | Microsoft Docs"
description: "Después de iniciar una revisión de acceso en Privileged Identity Management de Azure AD, aprenda a realizarla y ver los resultados."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: 3866438de8fba7a6c42777bbb57746eadf1158eb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Revisión de acceso en Privileged Identity Management de Azure AD
Los administradores de roles con privilegios pueden revisar el acceso con privilegios cuando se [ha iniciado una revisión de seguridad](active-directory-privileged-identity-management-how-to-start-security-review.md). Privileged Identity Management (PIM) de Azure AD enviará automáticamente un correo electrónico para pedir a los usuarios que revisen su acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en [Privileged Identity Management de Azure AD: Realización de una revisión de seguridad](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Después de que acabe el período de revisión de seguridad o de que todos los usuarios hayan finalizado su autorrevisión, siga los pasos de este artículo para administrar la revisión y ver los resultados.

## <a name="manage-security-reviews"></a>Administración de las revisiones de seguridad
1. Vaya al [Portal de Azure](https://portal.azure.com/) y seleccione la aplicación **Azure AD Privileged Identity Management** en el panel.
2. Seleccione la sección **Revisiones de acceso** del panel.
3. Seleccione la revisión de acceso que quiere administrar.

En la hoja de detalles de la revisión de acceso, hay un número de opciones para administrar dicha revisión.

![Botones de revisión de acceso de PIM: captura de pantalla][1]

### <a name="remind"></a>Recuerde
Si una revisión de acceso está configurada para que los usuarios revisen por sí mismos, el botón **Recordar** envía una notificación. 

### <a name="stop"></a>Detención
Todas las revisiones de acceso tienen una fecha de finalización, pero puede usar el botón **Detener** para terminarlas antes. Si para entonces algunos de los usuarios no se han revisado, no podrán hacerlo una vez que se haya detenido la revisión. No se puede reiniciar una revisión después de que se ha detenido.

### <a name="apply"></a>Aplicar
Después de que finaliza una revisión de acceso, bien porque se ha llegado a la fecha de finalización o porque se ha detenido manualmente, el botón **Aplicar** implementa el resultado de la revisión. Si se denegó el acceso de un usuario en la revisión, este es el paso que se quitará su asignación de rol.  

### <a name="export"></a>Exportación
Si quiere aplicar los resultados de la revisión de seguridad manualmente, puede exportar la revisión. El botón **Exportar** iniciará la descarga de un archivo CSV. Puede administrar los resultados en Excel u otros programas que abran archivos CSV.

### <a name="delete"></a>Eliminar
Si ya no le interesa más la revisión, elimínela. El botón **Eliminar** quita la revisión de la aplicación PIM.

> [!IMPORTANT]
> No recibirá ninguna advertencia antes de la eliminación, así que asegúrese de que realmente quiere eliminar esa revisión. 

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
