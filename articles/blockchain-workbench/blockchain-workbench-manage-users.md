---
title: Administración de usuarios en Azure Blockchain Workbench
description: Procedimientos de administración de usuarios en Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80f20e438b1d923e688aeef9e6b353642bd74c27
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158942"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Administración de usuarios en Azure Blockchain Workbench

Azure Blockchain Workbench incluye administración de usuarios para las personas y organizaciones que forman parte del consorcio.

## <a name="prerequisites"></a>requisitos previos

Se requiere una implementación de Blockchain Workbench. Consulte [Implementación de Azure Blockchain Workbench](blockchain-workbench-deploy.md) para detalles acerca de la implementación.

## <a name="add-azure-ad-users"></a>Adición de usuarios de Azure AD

Azure Blockchain Workbench usa Azure Active Directory (Azure AD) para la autenticación, el control de acceso y los roles. Los usuarios del inquilino de Azure AD de Blockchain Workbench pueden autenticarse y utilizar Blockchain Workbench. Agregue usuarios al rol de aplicación de administrador para interactuar y realizar acciones.

Los usuarios de Blockchain Workbench deben existir en el inquilino de Azure AD antes de que se les pueda asignar a aplicaciones y roles. Para agregar usuarios a Azure AD, siga estos pasos:

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com).
2.  Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD asociado a Azure Blockchain Workbench.
3.  Seleccione **Azure Active Directory > Usuarios**. Verá una lista de los usuarios del directorio.
4.  Para agregar usuarios al directorio, seleccione **Nuevo usuario**. Para usuarios externos, seleccione **Nuevo usuario invitado**.

    ![Nuevo usuario](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  Rellene los campos obligatorios para el nuevo usuario. Seleccione **Crear**.

Visite la documentación de [Azure AD](../active-directory/add-users-azure-active-directory.md) para más información sobre cómo administrar usuarios en Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Administración de administradores de Blockchain Workbench

Una vez que los usuarios se han agregado al directorio, el siguiente paso es elegir los usuarios que serán administradores de Blockchain Workbench. Los usuarios del grupo **Administrador** están asociados al **rol de administrador de aplicación** en Blockchain Workbench. Los administradores pueden agregar o quitar usuarios, asignar usuarios a escenarios específicos y crear nuevas aplicaciones.

Para agregar usuarios al grupo **Administrador** en el directorio de Azure AD:

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com).
2.  Compruebe que se encuentra en el inquilino de Azure AD asociado a Blockchain Workbench seleccionando su cuenta en la esquina superior derecha.
3.  Seleccione **Azure Active Directory > Aplicaciones empresariales**.
4.  Seleccione la aplicación cliente de Azure AD para Blockchain Workbench
    
    ![Todos los registros de aplicaciones empresariales](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  Seleccione **Usuarios y grupos > Agregar usuario**.
6.  En **Agregar asignación**, seleccione **Usuarios**. Elija o busque el usuario que desea agregar como administrador. Haga clic en **Seleccionar** cuando termine de elegir.

    ![Agregar asignación](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  Compruebe que **Rol** está establecido en **Administrador**
10. Seleccione **Asignar**. Los usuarios agregados se muestran en la lista con el rol de administrador asignado.

    ![Usuarios de la aplicación cliente de Blockchain](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Administración de miembros de Blockchain Workbench

Utilice la aplicación Blockchain Workbench para administrar los usuarios y las organizaciones que forman parte del consorcio. Puede agregar o quitar usuarios de las aplicaciones y los roles.

1. [Abra Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) en el explorador e inicie sesión como administrador.

    ![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

    Los miembros se agregan a cada aplicación. Los miembros pueden tener uno o varios roles de aplicación para iniciar contratos o realizar acciones.

2. Para administrar los miembros de una aplicación, seleccione un icono de aplicación en el panel **Aplicaciones**.

    El número de miembros asociados a la aplicación seleccionada se refleja en el icono de miembros.

    ![Seleccionar aplicación](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adición de un miembro a la aplicación

1. Seleccione el icono de miembro para mostrar una lista de los miembros actuales.
2. Seleccione **Agregar miembros**.

    ![Adición de miembros](media/blockchain-workbench-manage-users/application-add-members.png)

3. Busque el nombre del usuario.  Solo se muestran los usuarios de Azure AD que existen en el inquilino de Blockchain Workbench. Si no se encuentra el usuario, deberá [agregar usuarios de Azure AD](#add-azure-ad-users).

    ![Adición de miembros](media/blockchain-workbench-manage-users/find-user.png)

4. Seleccione un **Rol** en la lista desplegable.

    ![Seleccionar miembros del rol](media/blockchain-workbench-manage-users/application-select-role.png)

5. Seleccione **Agregar** para agregar el miembro con el rol asociado a la aplicación.

#### <a name="remove-member-from-application"></a>Eliminación de un miembro de la aplicación

1. Seleccione el icono de miembro para mostrar una lista de los miembros actuales.
2. Para el usuario que desea eliminar, elija **Eliminar** en la lista desplegable de roles.

    ![Eliminar miembro](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Cambio o adición de rol

1. Seleccione el icono de miembro para mostrar una lista de los miembros actuales.
2. Para el usuario que desea cambiar, haga clic en la lista desplegable y seleccione el nuevo rol.

    ![Cambio de rol](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido cómo a administrar los usuarios en Azure Blockchain Workbench. Para aprender a crear una aplicación de cadena de bloques, continúe con el siguiente artículo de procedimientos.

> [!div class="nextstepaction"]
> [Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench](blockchain-workbench-create-app.md)
