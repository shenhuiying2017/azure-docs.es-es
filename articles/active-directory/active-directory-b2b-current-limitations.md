---
title: Limitaciones de colaboración B2B de Azure Active Directory | Microsoft Docs
description: Limitaciones actuales de la colaboración B2B de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d4efa6ad1d2768a41a670fae4c490942b5a814ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitaciones de colaboración B2B de Azure AD
La colaboración B2B de Active Directory (Azure AD) de Azure está sujeta actualmente a las limitaciones descritas en este artículo.

## <a name="possible-double-multi-factor-authentication"></a>Posibilidad de doble autenticación multifactor
Con B2B de Azure AD, puede aplicar la autenticación multifactor en la organización del recurso (la organización que invita). Los motivos de este enfoque se detallan en [Acceso condicional para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md). Si un asociado ya tiene instalada la autenticación multifactor y la exige, los usuarios del asociado podrían tener que realizar la autenticación una vez en su organización principal y otra vez en la suya.

## <a name="instant-on"></a>Activación instantánea
En los flujos de colaboración B2B, hemos agregado usuarios al directorio y los hemos actualizado dinámicamente durante el canje de invitación, la asignación de aplicaciones y así sucesivamente. Las actualizaciones y escrituras se producen normalmente en una instancia de directorio y se deben replicar en todas las instancias. La replicación se completa una vez que se actualicen todas las instancias. En ocasiones, cuando el objeto se escribe o se actualiza en una instancia y la llamada para recuperar este objeto es a otra instancia, pueden producirse latencias en la replicación. Si esto sucede, actualice o vuelva a intentarlo. Si está escribiendo una aplicación mediante la API, los reintentos con algún retroceso es una buena práctica defensiva para mitigar este problema.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre la colaboración de B2B de Azure AD:

- [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)

