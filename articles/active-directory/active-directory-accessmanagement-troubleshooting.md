---
title: "Solución de problemas de pertenencia dinámica para grupos| Microsoft Docs"
description: "Solución de problemas para la pertenencia dinámica para grupos en Azure AD."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 89bb04b6-a379-49c2-8465-fe386641816a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: c796550f4e47793285ba7d97cbb866c3820f32cf


---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solución de problemas relacionados con las pertenencias dinámicas para grupos
**He configurado una regla en un grupo pero las pertenencias no se actualizan en el grupo**.<br/>Compruebe que la opción **Habilitar la administración de grupos delegados** está establecida en **Sí** en la pestaña **Configurar**. Esta opción solo se verá si ha iniciado sesión como un usuario a quien se ha asignado una licencia de Active Directory Premium de Azure. Compruebe los valores de atributos de usuario en la regla: ¿hay usuarios que cumplen la regla?

**He configurado una regla, pero ahora se han quitado los miembros existentes de la regla**.<br/> Este es el comportamiento esperado. Los miembros existentes del grupo se quitan cuando una regla se habilita o se cambia. Los usuarios devueltos tras la evaluación de la regla se agregan como miembros al grupo.     

**No veo los cambios en la pertenencia al instante cuando agrego o cambio una regla, ¿por qué pasa esto?**<br/>La evaluación de pertenencia dedicada se realiza periódicamente en un proceso asincrónico en segundo plano. La duración del proceso viene determinada por el número de usuarios del directorio y el tamaño del grupo creado como resultado de la regla. Normalmente, los directorios con un número pequeño de usuarios verán los cambios en la pertenencia al grupo en unos pocos minutos. Los directorios con un gran número de usuarios pueden tardar 30 minutos o más en completarse.

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)




<!--HONumber=Jan17_HO1-->


