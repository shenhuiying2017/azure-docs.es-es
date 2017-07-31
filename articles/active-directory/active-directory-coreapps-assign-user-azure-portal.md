---
title: "Asignación de un usuario o un grupo a una aplicación empresarial en Azure Active Directory | Microsoft Docs"
description: "Procedimiento para seleccionar una aplicación empresarial para asignarla a un usuario o un grupo en la versión preliminar de Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: ee784704ada9238b5cd048f99aaa4cb192ec7d57
ms.contentlocale: es-es
ms.lasthandoff: 05/05/2017


---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Asignación de un usuario o un grupo a una aplicación empresarial en Azure Active Directory
Asignar un usuario o un grupo a una aplicación empresarial de Azure Active Directory (Azure AD) es fácil. Debe tener los permisos adecuados para administrar la aplicación de empresa y debe ser administrador global en el directorio.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>¿Cómo se asigna a los usuarios acceso a una aplicación empresarial?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba Azure Active Directory en el cuadro de texto y seleccione **Entrar**.
3. En la hoja **Azure Active Directory - *nombreDelDirectorio*** (es decir, la hoja de Azure AD del directorio que está administrando), seleccione **Aplicaciones empresariales**.

    ![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. En la hoja **Aplicaciones empresariales**, seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.
5. En la hoja **Enterprise applications (Aplicaciones empresariales) - Todas las aplicaciones** , seleccione una aplicación.
6. En la hoja ***nombre de la aplicación*** (es decir, la hoja con el nombre de la aplicación seleccionada en el título), seleccione **Usuarios y grupos**.

    ![Selección del comando Todas las aplicaciones](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. En la hoja ***nombreDeAplicación*** **- User & Group Assignment** (Asignación de usuario y de grupo), seleccione el comando **Agregar**.
8. En la hoja **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Asignación de un usuario o un grupo a la aplicación](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. En la hoja **Usuarios y grupos**, seleccione uno o varios usuarios o grupos de la lista y luego haga clic en el botón **Seleccionar** de la parte inferior de la hoja.
10. En la hoja **Agregar asignación**, seleccione **Rol**. Después, en la hoja **Seleccionar rol**, seleccione el rol que desea aplicar a los usuarios o grupos seleccionados, y luego haga clic en el botón **Aceptar** de la parte inferior de la hoja.
11. En la hoja **Agregar asignación**, haga clic en el botón **Asignar** de la parte inferior de la hoja. Los usuarios o grupos asignados tendrán definidos los permisos por el rol seleccionado para esta aplicación empresarial.

## <a name="next-steps"></a>Pasos siguientes
* [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
* [Eliminación de asignaciones de usuario o grupo de una aplicación empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](active-directory-coreapps-disable-app-azure-portal.md)
* [Cambio del nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)

