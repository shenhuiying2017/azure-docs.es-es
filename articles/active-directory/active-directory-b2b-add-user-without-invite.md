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
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075630"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Incorporación de usuarios de colaboración B2B sin invitación

> [!NOTE]
> Ahora, los usuarios invitados ya no necesitan el correo electrónico de invitación, excepto en algunos casos especiales. Para más información, consulte [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md).  

Puede permitir que a un usuario, por ejemplo, un representante del asociado comercial, agregue a los usuarios del asociado a su organización sin necesidad de canjear invitaciones. Todo lo que debe hacer es conceder a ese usuario privilegios de enumeración en el directorio que esté usando para la organización de asociado. 

Conceda estos privilegios cuando se den estas condiciones:

1. Un usuario de la organización anfitriona (por ejemplo, WoodGrove) invita a un usuario de la organización asociada (por ejemplo, Sam@litware.com) como invitado.
2. El administrador de la organización anfitriona [configura directivas](active-directory-b2b-delegate-invitations.md) que permiten a Sam identificar y agregar otros usuarios de la organización del asociado (Litware).
3. Ahora, Sam puede agregar otros usuarios de Litware al directorio, los grupos o las aplicaciones de WoodGrove sin necesidad de canjear invitaciones. Si Sam tiene los privilegios de enumeración adecuados en Litware, se hará automáticamente.

### <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
- [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
- [Delegación de invitaciones de la colaboración B2B de Azure Active Directory](active-directory-b2b-delegate-invitations.md)

