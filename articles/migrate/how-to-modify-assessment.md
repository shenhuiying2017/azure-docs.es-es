---
title: "Personalización de la configuración de evaluación de Azure Migrate | Microsoft Docs"
description: "Describe cómo configurar y ejecutar una evaluación para migrar máquinas virtuales de VMware a Azure con Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>Personalización de una evaluación

[Azure Migrate](migrate-overview.md) crea evaluaciones con la configuración predeterminada. Después de crear una evaluación, puede modificar esta configuración predeterminada con las instrucciones de este artículo.


## <a name="edit-assessment-values"></a>Edición de valores de evaluación

1. En la página **Evaluaciones** del proyecto de Azure Migrate, seleccione la evaluación y haga clic en **Editar propiedades**.
2. Modifique la configuración de acuerdo con la tabla siguiente.

    **Configuración** | **Detalles** | **Valor predeterminado**
    --- | --- | ---
    **Ubicación de destino** | La ubicación de Azure a la que desea realizar la migración. |  Oeste de EE. UU. 2 es la ubicación predeterminada.
    **Redundancia de almacenamiento** | El tipo de almacenamiento que las máquinas virtuales de Azure usarán después de la migración. | Actualmente, solo se admite la replicación del [almacenamiento con redundancia local (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage).
    **Factor de confort** | El factor de confort es un búfer que se usa durante la evaluación. Úselo para tener en cuenta elementos como el uso estacional, el breve historial de rendimiento o el probable aumento en el uso futuro. | La configuración predeterminada es 1.3x.
    **Historial de rendimiento** | Tiempo empleado en la evaluación del historial de rendimiento. | El valor predeterminado es un mes.
    **Uso de percentil** | Valor de percentil para tener en cuenta para el historial de rendimiento. | El valor predeterminado es 95 %.
    **Plan de tarifa** | Puede especificar el [plan de tarifa](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) para una máquina virtual.  | De forma predeterminada se usa el plan [Estándar](../virtual-machines/windows/sizes-general.md).
    **Oferta** | [Ofertas de Azure](https://azure.microsoft.com/support/legal/offer-details/) que se aplican. | [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) es el valor predeterminado.
    **Moneda** | Divisa de facturación. | El valor predeterminado es el dólar estadounidense.
    **Descuento (%)** | Cualquier descuento específico de la suscripción que reciba además de una oferta. | La configuración predeterminada es 0 %.
    **Ventaja de uso híbrido de Azure** | Indica si está inscrito en la [Ventaja de uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si se establece en Sí, los precios de Azure que no son de Windows se toman en cuenta para las máquinas virtuales de Windows. | El valor predeterminado es Yes.

3. Haga clic en **Guardar** para actualizar la evaluación.


## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
