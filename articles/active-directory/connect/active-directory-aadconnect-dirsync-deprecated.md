---
title: "Actualización desde DirSync y Azure AD Sync | Microsoft Docs"
description: "Describe cómo actualizar desde DirSync y Azure AD Sync a Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3674670e10500d2992539ab60fbdb31b666fcf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Actualización de la sincronización de Microsoft Azure Active Directory ("DirSync") y la sincronización de Azure Active Directory ("Azure AD Sync")
Azure AD Connect es la mejor manera de conectar su directorio local con Azure AD y Office 365. Es una ocasión ideal para actualizar a Azure AD Connect desde la sincronización de Microsoft Azure Active Directory (DirSync) o desde la sincronización de Azure AD, ya que estas herramientas ahora están en desuso y el soporte técnico finalizará el 13 de abril de 2017.

Estas dos herramientas de sincronización de identidades que ahora están en desuso se ofrecían para los clientes de bosque único (DirSync) y para los de varios bosques y otros clientes avanzados (Azure AD Sync). Estas herramientas anteriores se han reemplazado por una única solución disponible para todos los escenarios: Azure AD Connect. Ofrece nuevas funcionalidades, mejoras en las características y soporte técnico para nuevos escenarios. Para poder seguir sincronizando los datos de identidades locales con Azure AD y Office 365, se recomienda encarecidamente que actualice a Azure AD Connect.

La última versión de DirSync se lanzó en julio de 2014 y la última versión de Azure AD Sync, en mayo de 2015.

## <a name="what-is-azure-ad-connect"></a>Qué es Azure AD Connect
Azure AD Connect es el sucesor de DirSync y Azure AD Sync. Combina todos los escenarios que admitían estas dos herramientas. Obtenga más información en [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Programación de degradación
| Date | Comentario |
| --- | --- |
| 13 de abril de 2016 |Se anuncia que la sincronización de Microsoft Azure Active Directory (“DirSync”) y la sincronización de Microsoft Azure Active Directory (“Azure AD Sync”) están en desuso. |
| 13 de abril de 2017 |Finaliza el soporte técnico. Los clientes ya no podrán abrir un caso de soporte técnico sin actualizar primero a Azure AD Connect. |
|31 de diciembre de 2017|Azure AD dejará de aceptar comunicaciones procedentes de la sincronización de Windows Azure Active Directory ("DirSync") y de la sincronización de Microsoft Azure Active Directory ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Cómo realizar una transición a Azure AD Connect
Si usa DirSync, hay dos formas de actualizar: mediante una actualización local y mediante una implementación paralela. Recomendamos la actualización local a la mayoría de los clientes, en caso de que tengan un sistema operativo reciente y menos de 50 000 objetos. En otros casos se recomienda realizar una implementación paralela, cuando la configuración de DirSync se mueve a un servidor nuevo que ejecuta Azure AD Connect.

Si usa Azure AD Sync, se recomienda una actualización local. Si le interesa, puede instalar un servidor nuevo de Azure AD Connect en paralelo y realizar una migración oscilante desde el servidor de Azure AD Sync a Azure AD Connect.

| Solución | Escenario |
| --- | --- |
| [Actualización desde DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Si tiene un servidor de DirSync existente que ya se está ejecutando.</li> |
| [Actualización desde Sincronización de Azure AD](active-directory-aadconnect-upgrade-previous-version.md) |<li>Si va a trasladarse desde Azure AD Sync.</li> |

Si quiere ver cómo se realiza una actualización local desde DirSync a Azure AD Connect, consulte este vídeo de Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Preguntas más frecuentes
**P: He recibido una notificación de correo electrónico del equipo de Azure o un mensaje del Centro de mensajes de Office 365, pero estoy usando Connect.**  
La notificación también se ha enviado a los usuarios de Azure AD Connect con un número de compilación 1.0.\*.0 (que utilicen una versión anterior a 1.1). Microsoft recomienda a los clientes que se mantengan al día con las versiones de Azure AD Connect. Con la versión 1.1, la característica de [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) facilita en gran medida contar siempre con una versión reciente de Azure AD Connect instalada.

**P: ¿Dejarán de funcionar DirSync o Azure AD Sync el 13 de abril de 2017?**  
DirSync/Azure AD Sync continuarán funcionando el 13 de abril de 2017.  Sin embargo, Azure AD ya no aceptará las comunicaciones procedentes de DirSync/Azure AD Sync el 31 de diciembre de 2017.

**P: ¿Desde qué versiones de DirSync se puede actualizar?**  
Se admite la actualización desde cualquier versión de DirSync que se use en la actualidad.

**P: ¿Qué sucede con el conector de Azure AD para FIM o MIM?**  
**No** se ha anunciado que el conector de Azure AD para FIM o MIM esté en desuso. Se trata de una **inmovilización de características**; es decir, no se le agregan funcionalidades nuevas y no recibe correcciones de errores. Microsoft recomienda a los clientes que lo usan que planeen pasarse a Azure AD Connect. Se recomienda encarecidamente que no lo usen para iniciar nuevas implementaciones. En el futuro se anunciará que este conector está en desuso.

## <a name="additional-resources"></a>Recursos adicionales
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
