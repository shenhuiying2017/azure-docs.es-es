---
title: "Creación de un proyecto de servicio en la nube de Azure con Visual Studio | Microsoft Docs"
description: "Obtenga información sobre cómo crear un proyecto de servicio en la nube de Azure con Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 1f6ded87b551f660853ea4eb0548f3d942e28fa8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Creación de un proyecto de servicio en la nube de Azure con Visual Studio
Azure Tools para Visual Studio proporciona una plantilla de proyecto que permite crear un servicio en la nube de Azure. Una vez creado el proyecto, Visual Studio le permite configurar, depurar e implementar el servicio en la nube en Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Pasos para crear un proyecto de servicio en la nube de Azure en Visual Studio
En esta sección se le enseñará cómo crear un proyecto de servicio en la nube de Azure en Visual Studio con uno o más roles web.  

1. Inicie Visual Studio como administrador.

1. En el menú principal, seleccione **Archivo** > **Nuevo** > **Proyecto**.

1. Seleccione **Nube** en los nodos de plantillas de proyectos de Visual C# o Visual Basic y, luego, seleccione **Servicio en la nube de Azure** en la lista de plantillas.

    ![Nuevo servicio en la nube de Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Especifique la versión de .NET Framework que quiere usar para desarrollar el proyecto.

1. Escriba un nombre y una ubicación para el proyecto y un nombre para la solución. 

1. Seleccione **Aceptar**.

1. En el cuadro de diálogo **Nuevo servicio en la nube de Microsoft Azure**, seleccione los roles que desea agregar y elija el botón de flecha a la derecha para agregarlos a la solución.

    ![Selección de nuevos roles de servicio en la nube de Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Para cambiar el nombre de un rol que haya agregado, mantenga el puntero sobre el rol en el cuadro de diálogo **Nuevo servicio en la nube de Microsoft Azure** y, en el menú contextual, seleccione **Cambiar nombre**. También puede cambiar el nombre de un rol dentro de la solución (en el **Explorador de soluciones**) una vez que lo haya agregado.

    ![Cambio de nombre de rol de servicio en la nube de Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

El proyecto de Azure de Visual Studio tiene asociaciones a los proyectos de rol de la solución. El proyecto incluye también el *archivo de definición de servicio* y el *archivo de configuración de servicio*:

- **Archivo de definición de servicio**: define la configuración del entorno de tiempo de ejecución de la aplicación, incluidos los roles que se requieren, los puntos de conexión y el tamaño de máquina virtual. 
- **Archivo de configuración de servicio**: configura el número de instancias de un rol que se ejecutan y los valores de la configuración definida para un rol. 

Para más información sobre estos archivos, consulte [Configuración de los roles para un servicio en la nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Pasos siguientes
- [Administración de roles en los proyectos de servicio en la nube de Azure con Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
