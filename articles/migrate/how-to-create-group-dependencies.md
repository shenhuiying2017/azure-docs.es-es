---
title: "Refinación de un grupo de evaluación con la asignación de dependencias de grupo en Azure Migrate | Microsoft Docs"
description: "Describe cómo refinar una evaluación mediante la asignación de dependencia de grupo en el servicio Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinación de un grupo con la asignación de dependencias de grupo

En este artículo se describe cómo configurar la asignación de dependencias de grupo para la evaluación de [Azure Migrate](migrate-overview.md). Este método se suele utilizar normalmente cuando desea refinar la configuración de un grupo existente mediante la comprobación cruzada de las dependencias de grupo antes de ejecutar una evaluación. Los grupos en los que desea ejecutar la asignación de dependencias de grupo no deben contener más de 10 máquinas.  

## <a name="modify-a-group"></a>Modificación de un grupo

1. En el proyecto de Azure Migrate, en **Administrar**, haga clic en  **Grupos** y seleccione el grupo.
2. En la página de grupo, haga clic en  **Ver dependencias** para abrir la asignación de dependencias de grupo. 

     ![Visualización de grupos](./media/how-to-create-group-dependencies/create-group.png)

3. Para ver dependencias más granulares, haga clic en el intervalo de tiempo para modificarlo. De forma predeterminada, el intervalo es una hora. Puede modificar el intervalo de tiempo o especificar las fechas de inicio y finalización, y la duración.
4. Presione Ctrl y haga clic para seleccionar las máquinas en el mapa. Agregue o quite máquinas en la asignación.
    - Solo puede agregar máquinas que se han detectado.
    - Al agregar y quitar máquinas en un grupo, se invalidan sus evaluaciones anteriores.
    - Si lo desea, puede crear una evaluación cuando se modifica el grupo.
5. Haga clic en **Aceptar** para guardar el grupo.

    ![Adición y eliminación](./media/how-to-create-group-dependencies/add-remove.png)

Si desea comprobar las dependencias de una máquina específica que aparece en el mapa de dependencias de grupo, [configure la asignación de dependencias de máquina](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Pasos siguientes

[Aprenda más](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
