---
title: "Configuración de la replicación de máquinas virtuales de Azure en Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo configurar la replicación de máquinas virtuales de Azure de una región de Azure a otra mediante Site Recovery."
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: 39d81ed6408e5f2c434a4fbaa681efc4c0b19a63
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicación de máquinas virtuales de Azure en otra región de Azure


>[!NOTE]
>
> La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.

En este artículo se describe cómo habilitar la replicación de máquinas virtuales de Azure de una región de Azure a otra.

## <a name="prerequisites"></a>requisitos previos

En este artículo se supone que ya configuró Site Recovery para este escenario como se describe en [Configuración de la recuperación ante desastres para las máquinas virtuales de Azure en una región secundaria de Azure (versión preliminar)](azure-to-azure-tutorial-enable-replication.md). Asegúrese de haber preparado los requisitos previos y de haber creado el almacén de Recovery Services.



## <a name="enable-replication"></a>Habilitar replicación

Habilite la replicación. En este procedimiento se supone que la región principal de Azure es Asia Pacífico, y la secundaria es Asia Suroriental.

1. En el almacén, haga clic en **+ Replicar**.
2. Especifique los siguientes campos:
    - **Origen**: el punto de origen de las máquinas virtuales, que en este caso es **Azure**.
    - **Ubicación de origen**: la región de Azure desde la que se quieren proteger las máquinas virtuales. En este ejemplo, la ubicación de origen es "Asia Oriental".
    - **Modelo de implementación**: el modelo de implementación de Azure de las máquinas de origen.
    - **Grupo de recursos**: el grupo de recursos al que pertenecen las máquinas virtuales de origen. Todas las máquinas virtuales del grupo de recursos seleccionado se enumeran para su protección en el paso siguiente.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. En **Máquinas virtuales > Seleccionar máquinas virtuales**, haga clic en cada máquina virtual que quiera replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.
    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. En **Configuración**, tiene la opción de configurar el sitio de destino:

    - **Ubicación de destino**: ubicación donde se replicarán los datos de la máquina virtual de origen. Según la ubicación de las máquinas seleccionadas, Site Recovery proporcionará la lista de regiones de destino adecuadas. Se recomienda mantener como la ubicación de destino la misma ubicación del almacén de Recovery Services.
    - **Grupo de recursos de destino**: el grupo de recursos al que pertenecen todas las máquinas virtuales replicadas. De forma predeterminada, Azure Site Recovery crea un nuevo grupo de recursos en la región de destino con un nombre con el sufijo "asr". En caso de que el grupo de recursos que cree Azure Site Recovery ya exista, se vuelve a usar. También puede elegir personalizarlo tal como se muestra en la sección siguiente.
    - **Red virtual de destino**: de forma predeterminada, Site Recovery crea una nueva red virtual en la región de destino con un nombre con el sufijo "asr". Esta se asigna a la red de origen y se usa para todas las protecciones futuras. [más información](site-recovery-network-mapping-azure-to-azure.md) sobre la asignación de red.
    - **Cuentas de almacenamiento de destino**: de forma predeterminada, Site Recovery crea la cuenta de almacenamiento de destino mediante la imitación de la configuración de almacenamiento de la VM de origen. En caso de que ya exista una cuenta de almacenamiento, esta se vuelve a usar.
    - **Cuentas de almacenamiento en caché**: Site Recovery necesita una cuenta de almacenamiento adicional denominada almacenamiento en caché en la región de origen. Todos los cambios que se producen en las máquinas virtuales de origen se siguen y se envían a la cuenta de almacenamiento en caché antes de su replicación en la ubicación de destino.
    - **Conjunto de disponibilidad**: de forma predeterminada, Azure Site Recovery crea un conjunto de disponibilidad en la región de destino con un nombre con el sufijo "asr". En caso de que el conjunto de disponibilidad que cree Azure Site Recovery ya exista, se vuelve a usar.
    - **Directiva de replicación**: define la configuración del historial de retención del punto de recuperación y de la frecuencia de instantánea coherente con la aplicación. De manera predeterminada, Azure Site Recovery crea una directiva de replicación nueva con la configuración predeterminada de 24 horas para la retención del punto de recuperación y 60 minutos para la frecuencia de instantánea coherente con la aplicación.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizar los recursos de destino

Puede modificar la configuración de destino predeterminada utilizada por Site Recovery.

1. Haga clic en **Personalizar:** para modificar la configuración predeterminada:
    - En **Grupo de recursos de destino**, seleccione el grupo de recursos en la lista de todos los grupos de recursos que existen en la ubicación de destino de la suscripción.
    - En **Red virtual de destino**, seleccione la red en una lista de todas las redes virtuales en la ubicación de destino.
    - En **Conjunto de disponibilidad**, puede agregar la configuración del conjunto de disponibilidad a la máquina virtual en caso de que forme parte de un conjunto de disponibilidad de la región de origen.
    - En **Cuentas de almacenamiento de destino**, seleccione la cuenta que desea usar.

        ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Haga clic en **Crear recurso de destino** > **Habilitar replicación**.
3. Después de que las máquinas virtuales están habilitadas para la replicación, puede comprobar el estado de mantenimiento de las máquinas virtuales en **Elementos replicados**.

>[!NOTE]
>Durante la replicación inicial, el estado de la máquina virtual puede tardar un tiempo en actualizarse, sin ningún progreso. Haga clic en el botón **Actualizar** para ver el estado más reciente.
>

# <a name="next-steps"></a>Pasos siguientes

[Más información](site-recovery-test-failover-to-azure.md) sobre la ejecución de una conmutación por error de prueba.
