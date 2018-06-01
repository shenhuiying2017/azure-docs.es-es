---
title: Incorporación de usuarios de colaboración B2B a Azure Active Directory sin invitación | Microsoft Docs
description: Puede permitir que un usuario invitado agregue otros usuarios invitados a Azure AD sin canjear una invitación en Colaboración de Azure Active Directory B2B.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259706"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Incorporación de usuarios de colaboración B2B sin invitación

> [!NOTE]
> Ahora, los usuarios invitados ya no necesitan el correo electrónico de invitación, excepto en algunos casos especiales. Para más información, consulte [Canje de invitación de colaboración B2B](redemption-experience.md).  

Puede permitir que a un usuario, por ejemplo, un representante del asociado comercial, agregue a los usuarios del asociado a su organización sin necesidad de canjear invitaciones. Todo lo que debe hacer es conceder a ese usuario privilegios de enumeración en el directorio que esté usando para la organización de asociado. 

Conceda estos privilegios cuando se den estas condiciones:

1. Un usuario de la organización anfitriona (por ejemplo, WoodGrove) invita a un usuario de la organización asociada (por ejemplo, Sam@litware.com) como invitado.
2. El administrador de la organización anfitriona [configura directivas](delegate-invitations.md) que permiten a Sam identificar y agregar otros usuarios de la organización del asociado (Litware).
3. Ahora, Sam puede agregar otros usuarios de Litware al directorio, los grupos o las aplicaciones de WoodGrove sin necesidad de canjear invitaciones. Si Sam tiene los privilegios de enumeración adecuados en Litware, se hará automáticamente.

### <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la colaboración de Azure AD B2B?](what-is-b2b.md)
- [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](add-users-information-worker.md)
- [Canje de invitación de colaboración B2B](redemption-experience.md)
- [Delegación de invitaciones de la colaboración B2B de Azure Active Directory](delegate-invitations.md)

