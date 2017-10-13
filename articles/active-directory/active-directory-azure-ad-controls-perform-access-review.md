---
title: "Revisión del acceso con las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Revisión del acceso con las revisiones de acceso de Azure AD."
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
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>Revisión del acceso con las revisiones de acceso de Azure AD

Azure Active Directory simplifica el modo en que las empresas administran el acceso a las aplicaciones y los miembros de los grupos en Azure AD y otras instancias de Microsoft Online Services, con una característica denominada revisiones de acceso.  Puede haber recibido un correo electrónico de Microsoft que le pide que revise el acceso de los miembros de un grupo o los usuarios con acceso a una aplicación. 

## <a name="opening-an-access-review"></a>Apertura de una revisión de acceso

Puede ver las revisiones de acceso pendientes haciendo clic en el vínculo del correo electrónico.  Si no tiene el correo electrónico, puede encontrar las revisiones de acceso haciendo lo siguiente:

1. Iniciando sesión en el [panel de acceso de Azure AD](https://myapps.microsoft.com).
2. Haciendo clic en el icono en la esquina superior derecha de la página, que muestra la organización predeterminada y el nombre de usuario. Si hay más de una organización, seleccione la que haya solicitado una revisión de acceso.
3. Si hay un icono con la etiqueta **Acceder a las revisiones** en el lado derecho de la página, haga clic en él. En caso contrario, si el icono no está visible, no hay ninguna revisión de acceso para esa organización y no se requiere ninguna acción en este momento.

## <a name="filling-out-an-access-review"></a>Completar una revisión de acceso

Seleccione una revisión de acceso de la lista y verá los nombres de los usuarios que deban revisarse.  Puede que solo vea un nombre, el suyo, si la solicitud era para revisar su propio acceso.

Para cada fila de la lista, puede decidir si aprobar o denegar el acceso del usuario. Puede hacer clic en la fila y elegir si desea aprobarlo o rechazarlo. (Si no conoce el usuario, puede responder así también).

El revisor puede requerir que proporcione una justificación para aprobar un acceso continuo o una pertenencia al grupo.

## <a name="next-steps"></a>Pasos siguientes

Tenga en cuenta que el acceso denegado de ese usuario no se quita de inmediato, puede quitarse cuando se complete la revisión o un administrador la detenga. Por lo tanto, si desea cambiar la respuesta y aprobar un usuario denegado anteriormente o denegar el acceso a un usuario aprobado antes, haga clic en la fila, restablezca la respuesta y elija otra.  Puede hacerlo hasta que se complete la revisión de acceso.



