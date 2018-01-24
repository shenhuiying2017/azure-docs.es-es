---
title: "Administración de un área de trabajo de Azure Machine Learning | Microsoft Docs"
description: "Administrar el acceso a las áreas de trabajo de Azure Machine Learning, e implementar y administrar servicios web de la API de Machine Learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2e4b2869b6eac9670853832d58bc37f1cb0ed001
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Administración de un área de trabajo de Azure Machine Learning

> [!NOTE]
> Para obtener información acerca de cómo administrar servicios web en el portal Servicios web Machine Learning, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](manage-new-webservice.md).
> 
> 

Puede administrar áreas de trabajo de Machine Learning en Azure Portal.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

Para administrar un área de trabajo en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) mediante una cuenta de administrador de la suscripción de Azure.
2. En el cuadro de búsqueda de la parte superior de la página, escriba "áreas de trabajo de Machine Learning" y, después, seleccione **Áreas de trabajo de Machine Learning**.
3. Haga clic en el área de trabajo que desea administrar.

Además de la información de administración de recursos estándar y de las opciones disponibles, puede:

- Ver **Propiedades**: esta página muestra la información del área de trabajo y de los recursos, y puede cambiar la suscripción y el grupo de recursos con el que esta área de trabajo está conectado.
- **Resincronizar las claves de almacenamiento**: el área de trabajo mantiene las claves de la cuenta de almacenamiento. Si la cuenta de almacenamiento cambia las claves, puede hacer clic en **Resincronizar claves** para sincronizar las claves con el área de trabajo.

Para administrar los servicios web asociados a esta área de trabajo, use el Portal de servicios web Machine Learning. Para obtener una información más completa, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](manage-new-webservice.md).

> [!NOTE]
> Para implementar o administrar nuevos servicios web, es preciso que se le haya asignado un rol de colaborador o administrador en la suscripción en la que se implementa el servicio web. Si invita a otro usuario a un área de trabajo de Machine Learning, debe asignarle un rol de colaborador o administrador en la suscripción para que pueda implementar o administrar servicios web. 
> 
>Para más información sobre cómo establecer permisos de acceso, vea [Visualización de asignaciones de acceso para usuarios y grupos en Azure Portal](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="next-steps"></a>pasos siguientes
* Más información sobre cómo [implementar Machine Learning con plantillas de Azure Resource Manager](deploy-with-resource-manager-template.md). 