---
title: "Limitaciones actuales de la colaboración B2B de Active Directory Azure | Microsoft Docs"
description: "Limitaciones actuales de versión preliminar de la colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Limitaciones actuales de la colaboración B2B de Active Directory Azure

1. Las API de invitación de la colaboración B2B de Azure Active Directory (Azure AD) se encuentran en fase de prueba beta. La superficie de esas API está avanzando, pero, como todas las betas, está sujeta al contrato de espacio de nombres de betas. La API se moverá a un número de versión con la versión GA.
2. Posibilidad de que se produzca una doble autenticación multifactor (MFA): si su asociado ya tiene una directiva MFA, se realizará la MFA de colaboración B2B y se administrará en la organización invitadora. Esto es deseable porque abarca todas las identidades y permite controlar el nivel de autenticación de los invitados de la colaboración B2B. Sin embargo, si tiene un asociado que ya tiene configurada y aplicada la MFA, es posible que los usuarios de dicho asociado tengan que realizar la MFA una vez en su organización principal y, luego, otra en la suya. En futuras versiones, presentaremos una directiva donde puede decidir si confiar en la MFA de determinados asociados para evitar el problema de la doble MFA.
3. Activación instantánea: en los flujos de colaboración B2B, hemos agregado usuarios al directorio y los hemos actualizado dinámicamente durante el canje de invitación, la asignación de aplicaciones y así sucesivamente. Las actualizaciones y escrituras, en general, se producen en la instancia de un directorio y tiene que replicarse a través de todas. Hemos observado que, en algunos casos, la cantidad finita de tiempo que puede tardar en completarse la replicación puede producir problemas de autorización. Estamos trabajando duro para minimizar o eliminar esta serie de problemas antes de la disponibilidad general. Mientras tanto, aunque no debería encontrarse con este problema, en caso de que se ocurra, normalmente ayuda a actualizar o volver a intentarlo.

  ## <a name="next-steps"></a>Pasos siguientes

  Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

  * [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
  * [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
  * [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
  * [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
  * [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
  * [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
  * [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
  * [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
  * [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
  * [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)

