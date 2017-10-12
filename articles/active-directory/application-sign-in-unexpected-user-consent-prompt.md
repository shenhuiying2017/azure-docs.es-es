---
title: "Petición de consentimiento inesperada al iniciar sesión en una aplicación | Microsoft Docs"
description: "Cómo solucionar problemas cuando un usuario ve una solicitud de consentimiento para una aplicación que ha integrado con Azure AD que no esperaba"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e5b823e1251a7221f73efe6838d439f827f9665d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Petición de consentimiento inesperada al iniciar sesión en una aplicación

Muchas aplicaciones que se integran con Azure Active Directory requieren permisos para acceder a diversos recursos para poder ejecutarse. Cuando estos recursos también se integran con Azure Active Directory, suelen solicitarse permisos para acceder a ellos mediante el marco de consentimiento de Azure AD. 

Esto da como resultado que la primera vez que se usa una aplicación aparece una solicitud de consentimiento, que suele ser una operación única. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Escenarios en los que los usuarios ven solicitudes de consentimiento

Se pueden esperar más solicitudes en distintos escenarios:

* El conjunto de permisos que requiere la aplicación ha cambiado.

* El usuario que originalmente otorgó su consentimiento a la aplicación no era administrador, y ahora un usuario diferente (no administrador) está usando la aplicación por primera vez.

* El usuario que originalmente otorgó su consentimiento a la aplicación era administrador, pero no otorgó el consentimiento en nombre de toda la organización.

* La aplicación usa [consentimiento incremental y dinámico](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) para solicitar permisos adicionales después de haberse otorgado inicialmente el consentimiento. Esto se suele usar cuando las características opcionales de una aplicación adicional requieren permisos más allá de los que se requieren para la funcionalidad de línea base.

* Se ha revocado el consentimiento después de que inicialmente se otorgó.

* El desarrollador ha configurado la aplicación para que requiera una solicitud de consentimiento cada vez que se utilice (nota: esto no es recomendable).

## <a name="next-steps"></a>Pasos siguientes

-   [Aplicaciones, permisos y consentimiento en Azure Active Directory (punto de conexión v1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Ámbitos, permisos y consentimiento en Azure Active Directory (punto de conexión v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


