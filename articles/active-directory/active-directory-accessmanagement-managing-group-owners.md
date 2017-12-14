---
title: "Pasos siguientes para la administración de acceso mediante grupos | Microsoft Docs"
description: Procedimientos avanzados para administrar grupos de seguridad y uso de estos grupos para administrar el acceso a un recurso.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: 177e31b8b8aff90cd91135d02eb3a2d8c85e2c33
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="managing-owners-for-a-group"></a>Administración de propietarios de un grupo
Una vez que un propietario de recursos haya asignado acceso a un recurso a un grupo de Azure AD, el propietario del grupo será quien administre los miembros del grupo. El propietario del recursos delega de manera eficaz el permiso para asignar usuarios al recurso al propietario del grupo.

## <a name="add-an-owner-to-a-group"></a>Adición de un propietario a un grupo

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios y grupos**.
2. Seleccione **Todos los grupos** y abra el grupo al que desea agregar propietarios.
3. Seleccione **Agregar propietarios**.
4. En la página **Agregar propietarios**, seleccione el usuario que desea agregar como propietario del grupo y asegúrese de que este nombre se agrega al panel **Seleccionados**.

## <a name="remove-an-owner-from-a-group"></a>Eliminación de un propietario de un grupo

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios y grupos**.
2. Seleccione **Todos los grupos** y abra el grupo del que desea quitar propietarios.
3. Seleccione la pestaña **Propietarios** .
4. Seleccione el propietario que desea quitar del grupo y luego seleccione **Quitar**.

## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
