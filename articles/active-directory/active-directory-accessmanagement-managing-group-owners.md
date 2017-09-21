---
title: "Administración de propietarios de grupos en Azure Active Directory | Microsoft Docs"
description: "Administración de propietarios de grupos y uso de estos grupos para administrar el acceso a un recurso."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>Administración de propietarios de un grupo
Una vez que el propietario de un recurso haya asignado acceso a un recurso para un grupo de Azure AD, el propietario del grupo será quien administre los miembros del grupo. El propietario del recursos delega de manera eficaz el permiso para asignar usuarios al recurso al propietario del grupo.

## <a name="add-an-owner-to-a-group"></a>Adición de un propietario a un grupo

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios y grupos**.
2. Seleccione **Todos los grupos** y abra el grupo al que desea agregar propietarios.
3. Seleccione **Propietarios** y, a continuación, seleccione **Agregar propietarios**.
4. En la página **Agregar propietarios**, seleccione el usuario que desea agregar como propietario de este grupo y haga clic o puntee en **Seleccionar**. 

## <a name="remove-an-owner-from-a-group"></a>Eliminación de un propietario de un grupo

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Usuarios y grupos**.
2. Seleccione **Todos los grupos** y abra el grupo del que desea quitar propietarios.
3. Seleccione **Propietarios**, seleccione el propietario que desee quitar de este grupo y luego haga clic o puntee en **Seleccionar**.
4. En el panel abierto para el propietario seleccionado, seleccione **Quitar**.

## <a name="additional-information"></a>Información adicional
En estos artículos se proporciona información adicional sobre los grupos de Azure Active Directory.

* [Ver los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)

