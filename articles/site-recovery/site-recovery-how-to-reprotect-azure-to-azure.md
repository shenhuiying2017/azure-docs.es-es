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
ms.openlocfilehash: 32f5d2d142940bc515849dcd0edb1bb1f152aa6d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>Volver a proteger desde una región de Azure conmutada por error de nuevo en la región principal



>[!NOTE]
>
> La replicación de Site Recovery para máquinas virtuales de Azure está actualmente en vista previa.


## <a name="overview"></a>Información general
Cuando se [conmutan por error](site-recovery-failover.md) las máquinas virtuales de una región de Azure en otra, las máquinas virtuales están en un estado desprotegido. Si quiere volver a llevarlas a la región principal, primero debe protegerlas y luego volver a conmutar por error. No hay ninguna diferencia entre cómo se conmuta por error en una dirección u otra. Del mismo modo, para habilitar la protección de las máquinas virtuales posteriormente, no hay ninguna diferencia entre volver a proteger después de la conmutación por error o por recuperación.
Para explicar los flujos de trabajo de reprotección y para evitar confusiones, se usará el sitio principal de las máquinas protegidas como región de Asia Oriental y el sitio de recuperación de las máquinas como región de Asia Suroriental. Durante la conmutación por error, las máquinas virtuales se conmutan por error en la región de Asia Suroriental. Antes de la conmutación por recuperación, debe volver a proteger las máquinas virtuales de Asia Suroriental en Asia Oriental. En este artículo se explican los pasos para volver a proteger.

> [!WARNING]
> Si ha [finalizado la migración](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), ha trasladado la máquina virtual a otro grupo de recursos o ha eliminado la máquina virtual de Azure, no puede entonces realizar la conmutación por recuperación.

Una vez que la reprotección ha finalizado y las máquinas virtuales protegidas se están replicando, puede iniciar una conmutación por error en las máquinas virtuales para llevarlas de nuevo a la región de Asia Oriental.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Requisitos previos
1. Las máquinas virtuales deben haberse confirmado.
2. El sitio de destino, en este caso la región de Azure de Asia Oriental, debe estar disponible y debe poder acceder a nuevos recursos en dicha región o crearlos.

## <a name="steps-to-reprotect"></a>Pasos para la reprotección

A continuación se indican los pasos para volver a proteger una máquina virtual con los valores predeterminados.

1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que ha sido objeto de la conmutación por error y seleccione **Reproteger**. También puede hacer clic en la máquina y seleccionar **Reproteger** con los botones de comando.

![Haga clic con el botón derecho para volver a proteger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. En la hoja, observe que la dirección de protección, **Asia Suroriental a Asia Oriental**, ya está seleccionada.

![Volver a proteger la hoja](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Revise la información de **Grupo de recursos, Red, Almacenamiento y Conjuntos de disponibilidad** y haga clic en Aceptar. Si hay algún recurso marcado (nuevo), se creará como parte de la reprotección.

Esto desencadenará un trabajo de reprotección que primero propagará el sitio de destino (región de Asia Suroriental en este caso) con los datos más recientes y, una vez hecho esto, replicará los valores delta antes de conmutar por error de nuevo en Asia Suroriental.

### <a name="reprotect-customization"></a>Volver a proteger la personalización
Si quiere elegir la cuenta de almacenamiento de extracción o la red durante la reprotección, puede hacerlo mediante la opción de personalización proporcionada en la hoja de reprotección.

![Opción de personalización](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Puede personalizar las siguientes propiedades de la máquina virtual de destino durante la reprotección.

![Personalizar la hoja](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propiedad |Notas  |
|---------|---------|
|Grupo de recursos de destino     | Puede cambiar el grupo de recursos de destino en el que se va a crear la máquina virtual. Como parte de la reprotección, se eliminará la máquina virtual de destino, así que puede elegir un nuevo grupo de recursos en el que crear la máquina virtual posterior a la conmutación por error.         |
|Red virtual de destino     | No se puede cambiar la red durante la reprotección. Para cambiar la red, vuelva a hacer la asignación de red.         |
|Almacenamiento de destino     | Puede cambiar la cuenta de almacenamiento en la que se creará la máquina virtual después de la conmutación por error.         |
|Almacenamiento en caché     | Puede especificar una cuenta de almacenamiento en caché que se usará durante la replicación. Si usa los valores predeterminados, se creará una nueva cuenta de almacenamiento en caché, si aún no existe.         |
|Conjunto de disponibilidad     |Si la máquina virtual de Asia Oriental forma parte de un conjunto de disponibilidad, puede elegir un conjunto de disponibilidad para la máquina virtual de destino de Asia Suroriental. Los valores predeterminados buscarán el conjunto de disponibilidad de la región de Asia Suroriental existente e intentarán usarlo. Durante la personalización, puede especificar un conjunto de disponibilidad completamente nuevo.         |


### <a name="what-happens-during-reprotect"></a>¿Qué ocurre durante la reprotección?

Igual que después de habilitar la protección por primera vez, estos son los artefactos que se crean si usa los valores predeterminados.
1. Se crea una cuenta de almacenamiento en caché en la región de Asia Oriental.
2. Si la cuenta de almacenamiento de destino (la cuenta de almacenamiento original de la máquina virtual de Asia Suroriental) no existe, se crea una nueva. El nombre es la cuenta de almacenamiento de la máquina virtual de Asia Oriental con el sufijo "asr".
3. Si no existe el conjunto de disponibilidad de destino y los valores predeterminados detectan que hay que crear un nuevo conjunto de disponibilidad, se crea como parte del trabajo de reprotección. Si ha personalizado la reprotección, se usa el conjunto de disponibilidad seleccionado.
4.

A continuación se indican los pasos que se producen al desencadenar un trabajo de reprotección, siempre que la máquina virtual de destino exista.

1. Los artefactos necesarios se crean como parte de la reprotección. Si ya existen, se vuelven a usar.
2. Si se está ejecutando, primero se desactiva la máquina virtual de destino (Asia Suroriental).
3. Azure Site Recovery copia el disco de la máquina virtual de destino en un contenedor como un blob de inicialización.
4. Luego se elimina la máquina virtual de destino.
5. La máquina virtual de origen actual (Asia Oriental) usa el blob de inicialización para replicar. Esto garantiza que solo se repliquen los valores delta.
6. Se sincronizan los cambios más importantes entre el disco de origen y el blob de inicialización. Esta operación puede tardar un tiempo en finalizar.
7. Una vez que finaliza el trabajo de reprotección, comienza la replicación de valores delta que crea un punto de recuperación según la directiva.

> [!NOTE]
> No se puede proteger en un nivel de plan de recuperación. Solo se puede volver a proteger en un nivel de máquina virtual.

Una vez que la reprotección se haya completado correctamente, la máquina virtual entrará en un estado protegido.

## <a name="next-steps"></a>Pasos siguientes

Una vez que la máquina virtual ha entrado en un estado protegido, puede iniciar una conmutación por error. La conmutación por error apagará la máquina virtual de la región de Azure de Asia Oriental y luego creará e iniciará la máquina virtual de la región de Asia Suroriental. Por lo tanto, hay un breve tiempo de inactividad para la aplicación. Así pues, elija para la conmutación por error un momento en que la aplicación pueda tolerar un tiempo de inactividad. Se recomienda probar primero la conmutación por error de la máquina virtual para asegurarse de que se recupera correctamente antes de iniciar una conmutación por error.

-   [Pasos para iniciar una conmutación por error de prueba de la máquina virtual](site-recovery-test-failover-to-azure.md)

-   [Pasos para iniciar una conmutación por error de la máquina virtual](site-recovery-failover.md)
