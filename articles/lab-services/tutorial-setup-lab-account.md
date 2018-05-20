---
title: Configuración de una cuenta de laboratorio con Azure Lab Services | Microsoft Docs
description: En este tutorial, va a configurar una cuenta de laboratorio con Azure Lab Services (anteriormente DevTest Labs).
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 5af348bfdccb9392948af962cf582e33ebd40872
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>Tutorial: Configuración de una cuenta de laboratorio con Azure Lab Services (anteriormente Azure DevTest Labs)
En este tutorial, va a actuar como administrador de laboratorio para crear una cuenta de laboratorio con Azure Lab Services. Después, va a proporcionar a los educadores el permiso para crear laboratorios para sus clases en esta cuenta de laboratorio. El educador puede configurar un laboratorio para una clase mediante el [sitio web de Azure Lab Services](https://labs.azure.com).   

En este tutorial realizará lo siguiente:

> [!div class="checklist"]
> * Creación de una cuenta de laboratorio
> * Adición de un usuario al rol Creador de laboratorio

## <a name="prerequisites"></a>requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- Azure Lab Services actualmente se encuentra en versión preliminar controlada. Para crear una cuenta de laboratorio, [regístrese para obtener la versión preliminar](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Creación de una cuenta de laboratorio
Los pasos siguientes muestran cómo usar Azure Portal para crear una cuenta de laboratorio con Azure Lab Services. 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú principal de la izquierda, seleccione **Crear un recurso**  (en la parte superior de la lista), seleccione **Herramientas de desarrollo** y haga clic en **Lab Services (versión preliminar)**.
1. En la ventana **Create a lab account** (Crear una cuenta de laboratorio), seleccione **Crear**.
2. En la ventana **Lab account** (Cuenta de laboratorio), realice las acciones siguientes: 
    1. En **Lab account name** (Nombre de la cuenta de laboratorio), escriba un nombre. 
    2. Seleccione la **suscripción de Azure** donde desea crear la cuenta de laboratorio.
    3. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos.
    4. En **Ubicación**, seleccione la ubicación o región en la que desea crear la cuenta de laboratorio. 
    5. Seleccione **Crear**. 

        ![Ventana de creación de una cuenta de laboratorio](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. Si no ve la página de la cuenta de laboratorio, seleccione el botón **Notificaciones** y haga clic en el botón **Ir al recurso** en las notificaciones. 

    ![Ventana de creación de una cuenta de laboratorio](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Verá la siguiente página de a **cuenta de laboratorio**:

    ![Página de la cuenta de laboratorio](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adición de un usuario al rol Creador de laboratorio
Para proporcionar a los educadores el permiso para crear laboratorios para sus clases agréguelos al rol Creador de laboratorios:

1. En la página de la **cuenta de laboratorio**, seleccione **Control de acceso (IAM)** y haga clic en **+ Agregar** en la barra de herramientas. 

    ![Página de la cuenta de laboratorio](./media/tutorial-setup-lab-account/access-control.png)
2. En la página **Agregar permisos**, seleccione **Creador de laboratorio** en **Rol**, seleccione el usuario que desea agregar al rol Creador de laboratorio y seleccione **Guardar**. 

    ![Adición del usuario al rol Creador de laboratorio](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha creado una cuenta de laboratorio. Para aprender a crear un laboratorio como profesor, pase al siguiente tutorial:

> [!div class="nextstepaction"]
> [Configuración de un laboratorio de clase](tutorial-setup-classroom-lab.md)

