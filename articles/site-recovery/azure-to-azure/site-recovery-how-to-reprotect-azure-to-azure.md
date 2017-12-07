---
title: "Cómo volver a proteger máquinas virtuales de Azure conmutadas por error de vuelta en la región principal de Azure | Microsoft Docs"
description: "Después de la conmutación por error de máquinas virtuales de una región de Azure en otra, puede usar Azure Site Recovery para proteger las máquinas en dirección inversa. Conozca los pasos para volver a proteger antes de una nueva conmutación por error."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>Reprotección de las máquinas virtuales de Azure en la región primaria



>[!NOTE]
>
> La replicación de Site Recovery en máquinas virtuales (VM) de Azure está actualmente en versión preliminar.


Cuando realiza una [conmutación por error](../site-recovery-failover.md) de máquinas virtuales de una región de Azure a otra, las máquinas virtuales conmutadas por error están desprotegidas. Si quiere volver a llevarlas a la región primaria, primero debe empezar a replicar las máquinas virtuales y, luego, volver a realizar la conmutación por error. No hay ninguna diferencia entre la conmutación por error en una dirección o en otra. Del mismo modo, una vez que habilita la replicación de máquinas virtuales, no hay diferencia entre la reprotección posterior a la conmutación por error o posterior a la conmutación por recuperación.

Para explicar el proceso de reprotección, como ejemplo se supone que el sitio principal de las máquinas virtuales protegidas es la región de Asia Oriental y que el sitio de recuperación es el Sudeste Asiático. Durante la conmutación por error, conmuta por error las máquinas virtuales en la región del Sudeste Asiático. Antes de realizar la conmutación por recuperación, debe replicar las máquinas virtuales desde el Sudeste Asiático de vuelta a Asia Oriental. En este artículo se describen los pasos necesarios para realizar la reprotección.

> [!WARNING]
> Si completó la migración y migró la máquina virtual a otro grupo de recursos (o eliminó la máquina virtual de Azure), no puede realizar la conmutación por recuperación.

Una vez que finaliza la reprotección y que las máquinas virtuales se replican, puede iniciar una conmutación por error en las máquinas virtuales para llevarlas nuevamente a la región de Asia Oriental.

## <a name="prerequisites"></a>Requisitos previos
1. Se debe haber confirmado la máquina virtual.
2. El sitio de destino (Asia Oriental) debe estar disponible y debe ser posible acceder o crear recursos nuevos en esa región.

## <a name="reprotect"></a>Reprotección

Siga estos pasos para volver a proteger una máquina virtual con la configuración predeterminada.

1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que se conmutó por error y, luego, seleccione **Volver a proteger**. También puede hacer clic en la máquina y seleccionar **Reproteger** con los botones de comando.

  ![Reprotección](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Compruebe que está seleccionada la dirección de replicación **Sudeste Asiático a Asia Oriental**.

  ![Reprotección](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Revise la información de **Grupo de recursos, Red, Almacenamiento y Conjuntos de disponibilidad** y haga clic en **Aceptar**. Si hay algún recurso marcado (nuevo), se creará durante la reprotección.

Esta acción desencadena un trabajo de reprotección que inicializa el sitio de destino con los datos más recientes y, una vez que se completa el proceso, replica los valores delta antes de la conmutación por recuperación en el Sudeste Asiático.

### <a name="reprotect-customization"></a>Volver a proteger la personalización
Si quiere elegir la cuenta de almacenamiento de extracción o la red durante la reprotección, puede hacerlo mediante la opción de personalización proporcionada en la página de reprotección.

![Opción de personalización](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Puede personalizar las siguientes propiedades de la máquina virtual de destino durante la reprotección.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propiedad |Notas  |
|---------|---------|
|Grupo de recursos de destino     | Puede cambiar el grupo de recursos de destino en el que se va a crear la máquina virtual. Como parte de la reprotección, se eliminará la máquina virtual de destino, así que puede elegir un nuevo grupo de recursos en el que crear la máquina virtual posterior a la conmutación por error.         |
|Virtual Network de destino     | No se puede cambiar la red durante la reprotección. Para cambiar la red, vuelva a hacer la asignación de red.         |
|Almacenamiento de destino     | Puede cambiar la cuenta de almacenamiento en la que se creará la máquina virtual después de la conmutación por error.         |
|Almacenamiento en caché     | Puede especificar una cuenta de almacenamiento en caché que se usará durante la replicación. Si usa los valores predeterminados, se creará una nueva cuenta de almacenamiento en caché, si aún no existe.         |
|Conjunto de disponibilidad     |Si la máquina virtual de Asia Oriental forma parte de un conjunto de disponibilidad, puede elegir un conjunto de disponibilidad para la máquina virtual de destino de Asia Suroriental. Los valores predeterminados buscarán el conjunto de disponibilidad de la región de Asia Suroriental existente e intentarán usarlo. Durante la personalización, puede especificar un conjunto de disponibilidad completamente nuevo.         |


### <a name="what-happens-during-reprotect"></a>¿Qué ocurre durante la reprotección?

Al igual que después de habilitar la protección por primera vez, estos son los artefactos que se crean si usa los valores predeterminados.
1. Se crea una cuenta de almacenamiento en caché en la región de Asia Oriental.
2. Si la cuenta de almacenamiento de destino (la cuenta de almacenamiento original de la máquina virtual de Asia Suroriental) no existe, se crea una nueva. El nombre es la cuenta de almacenamiento de la máquina virtual de Asia Oriental con el sufijo "asr".
3. Si no existe el conjunto de disponibilidad de destino y los valores predeterminados detectan que hay que crear un nuevo conjunto de disponibilidad, se crea como parte del trabajo de reprotección. Si personalizó la reprotección, se usa el conjunto de disponibilidad seleccionado.


A continuación, se indican los pasos que se producen al desencadenar un trabajo de reprotección, siempre que la máquina virtual de destino exista.

1. Los artefactos necesarios se crean como parte de la reprotección. Si ya existen, se vuelven a usar.
2. Si se está ejecutando, primero se desactiva la máquina virtual de destino (Asia Suroriental).
3. Azure Site Recovery copia el disco de la máquina virtual de destino en un contenedor como un blob de inicialización.
4. Luego se elimina la máquina virtual de destino.
5. La máquina virtual de origen actual (Asia Oriental) usa el blob de inicialización para replicar. Esto garantiza que solo se repliquen los valores delta.
6. Se sincronizan los cambios más importantes entre el disco de origen y el blob de inicialización. Esta operación puede tardar un tiempo en finalizar.
7. Una vez que finaliza el trabajo de reprotección, comienza la replicación de valores delta que crea un punto de recuperación según la directiva.

> [!NOTE]
> No se puede proteger en un nivel de plan de recuperación. Solo se puede volver a proteger en un nivel de máquina virtual.

Cuando la reprotección se haya realizado correctamente, la máquina virtual entrará en un estado protegido.

## <a name="next-steps"></a>Pasos siguientes

Una vez que la máquina virtual ha entrado en un estado protegido, puede iniciar una conmutación por error. La conmutación por error apagará la máquina virtual de la región de Azure de Asia Oriental y luego creará e iniciará la máquina virtual de la región de Asia Suroriental. Por lo tanto, hay un breve tiempo de inactividad para la aplicación. Así pues, elija para la conmutación por error un momento en que la aplicación pueda tolerar un tiempo de inactividad. Se recomienda que primero pruebe la conmutación por error de la máquina virtual para asegurarse de que se recupera correctamente antes de iniciar una conmutación por error.

-   [Pasos para iniciar una conmutación por error de prueba de la máquina virtual](../site-recovery-test-failover-to-azure.md)

-   [Pasos para iniciar una conmutación por error de la máquina virtual](../site-recovery-failover.md)
