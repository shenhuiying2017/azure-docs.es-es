---
title: "Personalización de la configuración de evaluación de Azure Migrate | Microsoft Docs"
description: "Describe cómo configurar y ejecutar una evaluación para migrar máquinas virtuales de VMware a Azure con Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: 8babdbc30e062c7b289e90a674cec3222943e48d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="customize-an-assessment"></a>Personalización de una evaluación

[Azure Migrate](migrate-overview.md) crea evaluaciones con propiedades predeterminadas. Después de crear una evaluación, puede modificar las propiedades predeterminadas siguiendo las instrucciones de este artículo.


## <a name="edit-assessment-properties"></a>Editar propiedades de evaluación

1. En la página **Evaluaciones** del proyecto de migración, seleccione la evaluación y haga clic en **Editar propiedades**.
2. Modifique las propiedades de acuerdo a la siguiente tabla:

    **Configuración** | **Detalles** | **Valor predeterminado**
    --- | --- | ---
    **Ubicación de destino** | La ubicación de Azure a la que desea realizar la migración. |  Oeste de EE. UU. 2 es la ubicación predeterminada.
    **Redundancia de almacenamiento** | El tipo de redundancia de almacenamiento que las máquinas virtuales de Azure usarán después de la migración. | [El almacenamiento con redundancia local (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) es el valor predeterminado. Azure Migrate solo admite las evaluaciones basadas en discos administrados y los discos administrados solo son compatibles con LRS, por lo tanto, la propiedad solo tiene actualmente la opción de LRS. 
    **Criterio de ajuste de tamaño** | El criterio que debe utilizar Azure Migrate para ajustar el tamaño de las máquinas virtuales para Azure. Puede hacer bien un ajuste de tamaño *basado en el rendimiento* o puede ajustar el tamaño de las máquinas virtuales *como en el entorno local*, sin tener en cuenta el historial de rendimiento. | El ajuste de tamaño basado en el rendimiento es el valor predeterminado.
    **Historial de rendimiento** | La duración a tener en cuenta para evaluar el rendimiento de las máquinas virtuales. Esta propiedad solo es aplicable cuando el criterio es el *ajuste de tamaño basado en el rendimiento*. | El valor predeterminado es un día.
    **Uso de percentil** | El valor de percentil del ejemplo de rendimiento establecido para determinar el tamaño adecuado. Esta propiedad solo es aplicable cuando el criterio es el *ajuste de tamaño basado en el rendimiento*.  | Valor predeterminado es percentil 95.
    **Plan de tarifa** | Puede especificar el [plan de tarifa (Básico o Estándar)](../virtual-machines/windows/sizes-general.md) de las máquinas virtuales de Azure de destino. Por ejemplo, si va a migrar un entorno de producción, debería tener en cuenta el plan Estándar, que proporciona máquinas virtuales con una latencia baja aunque con un costo más alto. Por otro lado, si tiene un entorno de desarrollo y pruebas, quizá debería considerar el plan Básico que tiene máquinas virtuales con una latencia mayor y un costo más bajo. | De forma predeterminada se usa el plan [Estándar](../virtual-machines/windows/sizes-general.md).
    **Factor de confort** | Azure Migrate tiene en cuenta un búfer (factor de confort) durante la evaluación. Dicho búfer se aplica además de los datos de uso de la máquina en las máquinas virtuales (CPU, memoria, disco y red). El factor de confort se tiene en cuenta en problemas como el uso estacional, un historial de rendimiento corto y los posibles aumentos en el uso futuro.<br/><br/> Por ejemplo, una máquina virtual de 10 núcleos con un uso del 20 % normalmente genera una máquina virtual de 2 núcleos. Sin embargo, con un factor de confort de 2.0 x, el resultado es una máquina virtual de 4 núcleos. | La configuración predeterminada es 1.3x.
    **Oferta** | [Oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/) en la que esté inscrito. | [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) es el valor predeterminado.
    **Moneda** | Divisa de facturación. | El valor predeterminado es el dólar estadounidense.
    **Descuento (%)** | Cualquier descuento específico de la suscripción que reciba además de la oferta de Azure. | La configuración predeterminada es 0 %.
    **Ventaja híbrida de Azure** | Especifique si dispone de Software Assurance y tiene derecho a [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si se establece en Sí, los precios de Azure que no son de Windows se toman en cuenta para las máquinas virtuales de Windows. | El valor predeterminado es Yes.

3. Haga clic en **Guardar** para actualizar la evaluación.


## <a name="next-steps"></a>pasos siguientes

[Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
