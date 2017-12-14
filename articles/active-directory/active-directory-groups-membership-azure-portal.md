---
title: "Administración de los grupos a los que pertenece un grupo en Azure Active Directory | Microsoft Docs"
description: "Los grupos pueden contener otros grupos en Azure Active Directory. Aquí se muestra cómo administrar las pertenencias a grupos."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 251289d2a70f824714789fdf2fb484949745d6d7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Administración de los grupos a los que pertenece un grupo en el inquilino de Azure Active Directory
Los grupos pueden contener otros grupos en Azure Active Directory. Aquí se muestra cómo administrar las pertenencias a grupos.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>¿Cómo puedo encontrar los grupos de los que es miembro mi grupo?
1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos**.

   ![Apertura de la imagen de usuarios y grupos](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Seleccione **Todos los grupos**.

   ![Selección de la imagen de grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Seleccione un grupo.
2. Seleccione **Pertenencia a grupos**.

   ![Apertura de la imagen Pertenencia a grupos](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Para agregar un grupo como miembro de otro grupo, en la hoja **Grupo - Pertenencia a grupos** seleccione el comando **Agregar** comando.
2. Seleccione un grupo de la hoja **Seleccionar grupo** y, después, haga clic en el botón **Seleccionar** situado en la parte inferior de dicha hoja. Solo se pueden agregar grupos a otros grupos de uno en uno. El cuadro **Usuario** filtra la visualización en función de si coincide lo que ha escrito con cualquier parte del nombre de un usuario o dispositivo. No se aceptan caracteres comodín en el cuadro.

   ![Adición de una pertenencia a grupos](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Para quitar un grupo como miembro de otro grupo, en la hoja **Grupo - Pertenencia a grupos** , seleccione uno.
9. Seleccione el comando **Quitar** y confirme la elección en el símbolo del sistema.

   ![Comando de eliminación de pertenencia a grupo](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Cuando termine de cambiar las pertenencias a grupos de su grupo, seleccione **Guardar**.

## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Ver los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
