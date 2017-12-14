---
title: "Administración de los miembros de un grupo en Azure Active Directory | Microsoft Docs"
description: "Cómo agregar o quitar usuarios y dispositivos de un grupo en Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 49c975aa34f28c6c00591435726e538bb79a78cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Administración de la pertenencia a grupos de los usuarios del inquilino de Azure Active Directory
En este artículo se explica cómo administrar los miembros de un grupo en Azure Active Directory (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>¿Cómo puedo buscar miembros y administrarlos?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Todos los grupos**.

   ![Apertura de la hoja Grupos](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. En la hoja **Usuarios y grupos - Todos los grupos** , seleccione un grupo.
5. En la hoja **Grupo - *nombre del grupo*** , seleccione **Miembros**.

   ![Apertura de la hoja Miembros](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Para agregar miembros al grupo, en la hoja **Grupo - Miembros**, seleccione **Agregar miembros**.

   ![Comando Agregar miembros](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. En la hoja **Miembros**, seleccione uno o varios usuarios o dispositivos que quiera agregar al grupo y, luego, haga clic en el botón **Seleccionar** situado en la parte inferior de la hoja para agregarlos. El cuadro **Usuario** filtra la visualización en función de si coincide lo que ha escrito con cualquier parte del nombre de un usuario o dispositivo. No se aceptan caracteres comodín en el cuadro.
8. Para quitar miembros del grupo, en la hoja **Grupo - Miembros** , seleccione uno.
9. En la hoja ***nombreDelMiembro***, seleccione el comando **Quitar** y confirme la elección en el símbolo del sistema.

   ![Comando Quitar miembros](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Cuando termine de modificar miembros del grupo, seleccione **Guardar**.

## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Ver los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
