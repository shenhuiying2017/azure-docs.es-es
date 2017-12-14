---
title: "Refinación de un grupo de evaluación con la asignación de dependencias de grupo en Azure Migrate | Microsoft Docs"
description: "Describe cómo refinar una evaluación mediante la asignación de dependencia de grupo en el servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: c30d6546e7c2d471d4b262a8af1ce593b2c1c3fb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
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

[Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
