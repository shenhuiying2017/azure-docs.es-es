---
title: "Creación de un grupo para usuarios de Azure Active Directory | Microsoft Docs"
description: Procedimiento para crear un grupo en Azure Active Directory y agregar miembros al grupo
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d3d37761a9fdf9bd9801396d45f2fcd47efb0be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Creación de un grupo y adición de miembros en Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [Portal de Azure clásico](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

En este artículo se explica cómo crear y rellenar un nuevo grupo en Azure Active Directory. Use un grupo para realizar tareas de administración, como asignar licencias o permisos a un número de usuarios o dispositivos a la vez.

## <a name="how-do-i-create-a-group"></a>¿Cómo se crea un grupo?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios** y grupos en el cuadro de texto y, después, presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Todos los grupos**.

   ![Apertura de la hoja Grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. En la hoja **Usuarios y grupos - Todos los grupos**, seleccione el comando **Agregar**.

   ![Selección del comando Agregar](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. En la hoja **Grupo** , agregue un nombre y una descripción para el nuevo grupo.
6. Para seleccionar los miembros que se agregarán al grupo, seleccione **Asignado** en el cuadro **Membership type** (Tipo de pertenencia a grupos) y luego seleccione **Miembros**. Para obtener más información sobre cómo administrar la pertenencia de un grupo de forma dinámica, consulte el artículo sobre el [uso de atributos para crear reglas avanzadas de pertenencias a grupo](active-directory-groups-dynamic-membership-azure-portal.md).

   ![Selección de miembros para agregar](./media/active-directory-groups-create-azure-portal/select-members.png)
7. En la hoja **Miembros**, seleccione uno o varios usuarios o dispositivos que quiera agregar al grupo y, luego, haga clic en el botón **Seleccionar** situado en la parte inferior de la hoja para agregarlos. El cuadro **Usuario** filtra la visualización en función de si coincide lo que ha escrito con cualquier parte del nombre de un usuario o dispositivo. No se aceptan caracteres comodín en el cuadro.
8. Cuando termine de agregar miembros al grupo, seleccione **Crear** en la hoja **Grupo**.    

   ![Creación de la confirmación de grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Pasos siguientes
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Administración de la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
