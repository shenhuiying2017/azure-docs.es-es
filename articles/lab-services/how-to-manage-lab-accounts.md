---
title: Administración de cuentas de laboratorio en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo crear o eliminar una cuenta de laboratorio y cómo ver todas las cuentas de laboratorio de una suscripción de Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 09303a4d4fc730aae6fc7c04148d64b0f57e69ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Administración de cuentas de laboratorio en Azure Lab Services (anteriormente Azure DevTest Labs)
En este artículo se describe cómo crear o eliminar una cuenta de laboratorio y cómo ver todas las cuentas de laboratorio.

## <a name="prerequisites"></a>requisitos previos
Azure Lab Services actualmente se encuentra en versión preliminar controlada. Para crear una cuenta de laboratorio, [regístrese para obtener la versión preliminar](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Creación de una cuenta de laboratorio
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú principal de la izquierda, seleccione **Crear un recurso**  (en la parte superior de la lista), seleccione **Herramientas de desarrollo** y haga clic en **Lab Services (versión preliminar)**.
1. En la ventana **Create a lab account** (Crear una cuenta de laboratorio), seleccione **Crear**.
2. En la ventana **Lab account** (Cuenta de laboratorio), realice las acciones siguientes: 
    1. En **Lab account name** (Nombre de la cuenta de laboratorio), escriba un nombre. 
    2. Seleccione la **suscripción de Azure** donde desea crear la cuenta de laboratorio.
    3. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos.
    4. En **Ubicación**, seleccione la ubicación o región en la que desea crear la cuenta de laboratorio. 
    5. Seleccione **Crear**. 

        ![Ventana de creación de una cuenta de laboratorio](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Si no ve la página de la cuenta de laboratorio, seleccione el botón **Notificaciones** y haga clic en el botón **Ir al recurso** en las notificaciones. 

    ![Ventana de creación de una cuenta de laboratorio](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Verá la siguiente página de a **cuenta de laboratorio**:

    ![Página de la cuenta de laboratorio](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Incorporación de un usuario al rol Creador de laboratorio
Para proporcionar a los educadores el permiso para crear laboratorios para sus clases agréguelos al rol Creador de laboratorios:

1. En la página de la **cuenta de laboratorio**, seleccione **Control de acceso (IAM)** y haga clic en **+ Agregar** en la barra de herramientas. 

    ![Página de la cuenta de laboratorio](./media/tutorial-setup-lab-account/access-control.png)
2. En la página **Agregar permisos**, seleccione **Creador de laboratorio** en **Rol**, seleccione el usuario que desea agregar al rol Creador de laboratorio y seleccione **Guardar**. 

    ![Incorporación del usuario al rol Creador de laboratorio](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Vista de las cuentas de laboratorio
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los recursos** en el menú. 
3. Seleccione **Servicios de laboratorio** como el **tipo**. 
    También puede filtrar por suscripción, grupo de recursos, ubicaciones y etiquetas. 

## <a name="delete-a-lab-account"></a>Eliminación de una cuenta de laboratorio
Siga las instrucciones que aparecen en la sección anterior que muestra una lista de las cuentas de laboratorio. Use las instrucciones siguientes para eliminar una cuenta de laboratorio: 

1. Seleccione la **cuenta de laboratorio** que desee eliminar. 
2. Seleccione **Eliminar** en la barra de herramientas. 
3. Escriba **Sí** para confirmar.
4. Seleccione **Eliminar**. 

## <a name="next-steps"></a>Pasos siguientes
Introducción a la configuración de un laboratorio con Azure Lab Services:

- [Configuración de un laboratorio educativo](tutorial-setup-classroom-lab.md)
- [Configuración de un laboratorio personalizado](tutorial-create-custom-lab.md)
