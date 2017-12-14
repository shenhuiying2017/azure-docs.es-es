---
title: Uso de un grupo para administrar el acceso a aplicaciones SaaS| Microsoft Docs
description: "Cómo usar grupos en Azure Active Directory Premium o Basic para asignar el acceso a aplicaciones SaaS integradas con Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 49a2c86516f0882f341597876d2a44ea3a312ea8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Uso de un grupo para administrar el acceso a las aplicaciones SaaS
Con Azure Active Directory (Azure AD) y una licencia de Azure AD Premium o Azure AD Basic, puede usar grupos para asignar el acceso a una aplicación SaaS integrada con Azure AD. Por ejemplo, si desea asignar acceso al departamento de marketing para utilizar cinco aplicaciones SaaS diferentes, puede crear un grupo que contenga a los usuarios del departamento de marketing y, a continuación, asignar ese grupo a las cinco aplicaciones SaaS requeridas por el departamento de marketing. De este modo, ahorrará tiempo administrando la pertenencia a grupos del departamento de marketing en un solo lugar. A continuación, los usuarios se asignan a la aplicación cuando se agregan como miembros del grupo de marketing, y se eliminan sus asignaciones de la aplicación cuando se eliminan del grupo de marketing. Esta función se puede utilizar con cientos de aplicaciones que puede agregar desde la Galería de aplicaciones de Azure AD.

> [!IMPORTANT]
> Solo puede utilizar esta característica después de iniciar una evaluación de Azure AD Premium o de adquirir las licencias premium o básica de Azure AD.
> No se admiten las pertenencias a grupos anidadas para la asignación basada en grupos a aplicaciones en este momento.

**Para asignar acceso para un usuario o un grupo a una aplicación SaaS**

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Aplicaciones empresariales**.
2. Seleccione una aplicación que haya agregado desde la Galería de aplicaciones para abrirla.
3. Seleccione **Usuarios y grupos** y, luego, **Agregar usuario**.
4. En **Agregar asignación**, seleccione **Usuarios y grupos** para abrir la lista de selección **Usuarios y grupos**.
6. Seleccione todos los grupos o usuarios que desee y, a continuación, haga clic o puntee en **Seleccionar** para agregarlos a la lista **Agregar asignación**. También puede asignar un rol a un usuario en esta fase.
7. Seleccione **Asignar** para asignar los usuarios o los grupos a la aplicación de empresa seleccionada.

### <a name="next-steps"></a>Pasos siguientes
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
