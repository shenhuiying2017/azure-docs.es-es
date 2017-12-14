---
title: "Revisión del acceso con las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Obtenga información sobre cómo revisar el acceso mediante el uso de las revisiones de acceso de Azure Active Directory."
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
ms.openlocfilehash: 98658a2b9fdd0ede98b3f6c10a19af527a8677cc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Revisión del acceso con las revisiones de acceso de Azure AD

Azure Active Directory (Azure AD) simplifica el modo en que las empresas administran el acceso a las aplicaciones y los miembros de los grupos en Azure AD y otras instancias de Microsoft Online Services, con una característica denominada revisiones de acceso. Puede que haya recibido un correo electrónico de Microsoft que le solicite que revise el acceso de los miembros de un grupo o los usuarios con acceso a una aplicación. 

## <a name="open-an-access-review"></a>Apertura de una revisión de acceso

Para ver las revisiones de acceso pendientes, seleccione el vínculo del correo electrónico. Si no tiene el correo electrónico, puede encontrar las revisiones de acceso siguiendo estos pasos:

1. Inicie sesión en el [panel de acceso de Azure AD](https://myapps.microsoft.com).

2. Seleccione el símbolo de usuario en la esquina superior derecha de la página, que muestra la organización predeterminada y el nombre de usuario. Si aparece más de una organización, seleccione la que haya solicitado una revisión de acceso.

3. Si hay un icono con la etiqueta **Acceder a las revisiones** en el lado derecho de la página, selecciónelo. Si el icono no está visible, no hay ninguna revisión de acceso para esa organización y no se requiere ninguna acción en este momento.

## <a name="fill-out-an-access-review"></a>Completado de una revisión de acceso

Cuando seleccione una revisión de acceso de la lista, verá los nombres de los usuarios que tienen que revisarse. Puede que solo vea un nombre, el suyo, si la solicitud era para revisar su propio acceso.

Para cada fila de la lista, puede decidir si aprobar o denegar el acceso del usuario. Seleccione la fila y elija si desea aprobarla o denegarla. (Si no conoce el usuario, puede indicarlo también).

El revisor puede requerir que proporcione una justificación para aprobar un acceso continuo o una pertenencia al grupo.

## <a name="next-steps"></a>Pasos siguientes

El acceso denegado del usuario no se quita de inmediato. Se puede quitar cuando la revisión ha finalizado o cuando un administrador detiene la revisión. Si desea cambiar la respuesta y aprobar un usuario denegado anteriormente o denegar el acceso a un usuario aprobado antes, seleccione la fila, restablezca la respuesta y seleccione una nueva. Puede realizar este paso hasta que se complete la revisión de acceso.



