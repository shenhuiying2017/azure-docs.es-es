---
title: "Agrupación de máquinas para su evaluación con Azure Migrate | Microsoft Docs"
description: "En este artículo se describe cómo agrupar máquinas antes de ejecutar una evaluación con el servicio Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Agrupación de máquinas para su evaluación

En este artículo se describe cómo crear un grupo de máquinas para su evaluación mediante [Azure Migrate](migrate-overview.md). Azure Migrate evalúa máquinas del grupo para comprobar si son adecuadas para su migración a Azure, y proporciona estimaciones de ajuste de tamaño y costo con relación a la ejecución de la máquina en Azure.


## <a name="create-a-group"></a>Creación de un grupo

1. En el **panel** del proyecto de Azure Migrate, haga clic en **Grupos** > **+ grupo** y especifique un nombre para el grupo.
2. Agregue una o variad máquinas al grupo y haga clic en  **Crear**. 
3. También puede elegir ejecutar una nueva evaluación para el grupo. 

    ![Creación de un grupo](./media/how-to-create-a-group/create-group.png)

Una vez creado el grupo, puede modificarlo seleccionándolo en la página **Grupos**, y agregando o quitando máquinas.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo usar la [asignación de dependencias de máquina](how-to-create-group-machine-dependencies.md) para crear grupos más detallados.
- [Aprenda más](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
