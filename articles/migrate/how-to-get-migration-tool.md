---
title: "Migración de máquinas después de su evaluación con Azure Migrate | Microsoft Docs"
description: "Describe cómo obtener recomendaciones para migrar equipos después de ejecutar una evaluación con el servicio de Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: e6e32e9bd2384987a1d0315bfbef913c46fc5dbb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-machines-after-assessment"></a>Migración de máquinas después de una evaluación


[Azure Migrate](migrate-overview.md) evalúa máquinas locales para comprobar si son adecuadas para su migración a Azure, y proporciona estimaciones de ajuste de tamaño y costo con relación a la ejecución de la máquina en Azure. Actualmente, Azure Migrate solamente evalúa máquinas para la migración. La migración propiamente dicha se realiza actualmente con otros servicios de Azure.

En este artículo se describe cómo obtener sugerencias para una herramienta de migración una vez ejecutada una evaluación de la migración.

## <a name="migration-tool-suggestion"></a>Sugerencia de la herramienta de migración

Para obtener sugerencias relativas a las herramientas de migración, debe realizar una detección en profundidad del entorno local. La detección en profundidad se realiza mediante la instalación de agentes en los equipos locales.  

1. Cree un proyecto de Azure Migrate, detecte las máquinas locales y cree una evaluación de la migración. [Más información](tutorial-assessment-vmware.md).
2. Descargue e instale los agentes de Azure Migrate en cada máquina local para la que desee ver un método de migración recomendado. [Siga este procedimiento](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) para instalar los agentes.
2. Identifique las máquinas locales que sean adecuadas para migrar mediante lift-and-shift. Estas son las máquinas virtuales que no requieren ningún cambio en aplicaciones que se ejecutan en ellas y se pueden migrar tal cual.
3. Para la migración lift-and-shift, se recomienda utilizar Azure Site Recovery. [Más información](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Como alternativa, puede usar herramientas de otros fabricantes que admiten la migración a Azure.
4. Si tiene máquinas locales que no son adecuadas para una migración lift-and-shift, es decir, si desea migrar una aplicación específica en lugar de una máquina virtual completa, puede usar otras herramientas de migración. Por ejemplo, se recomienda el [servicio Azure Database Migration](https://azure.microsoft.com/campaigns/database-migration/) si desea migrar bases de datos locales como SQL Server, MySQL u Oracle a Azure.


## <a name="review-suggested-migration-methods"></a>Revisión de los métodos de migración sugeridos

1. Antes de obtener un método de migración sugerido, debe crear un proyecto de Azure Migrate, detectar máquinas locales y ejecutar una evaluación de la migración. [Más información](tutorial-assessment-vmware.md).
2. Una vez creada la evaluación, puede verla en el proyecto > **Introducción** > **Panel**. Haga clic en **Preparación para Azure**.

    ![Preparación para Azure](./media/tutorial-assessment-vmware/assessment-report.png)  

3. En **Herramienta sugerida**, revise las sugerencias para las herramientas que puede usar para la migración.

    ![Herramienta sugerida](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
