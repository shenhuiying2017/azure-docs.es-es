---
title: "Reprotección de máquina virtuales de Azure conmutadas por recuperación en la región principal de Azure con Azure Site Recovery | Microsoft Docs"
description: "Se describe cómo reproteger las máquinas virtuales de Azure en una región secundaria, después de la conmutación por error desde una región principal, con Azure Site Recovery."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rajanaki
ms.openlocfilehash: d24376c57c468a562fc6d6dd52b4e9b01b53c3da
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Reprotección de máquinas virtuales de Azure conmutadas por error en la región principal


>[!NOTE]
>
> La replicación de Site Recovery en máquinas virtuales de Azure está actualmente en su versión preliminar.



Al [conmutar por error](site-recovery-failover.md) las máquinas virtuales de Azure desde una región a otra con [Azure Site Recovery](site-recovery-overview.md), las máquinas virtuales se inician en la región secundaria, con un estado desprotegido. Si conmuta por recuperación las máquinas virtuales en la región principal, necesita hacer lo siguiente:

- Vuelva a proteger las máquinas virtuales en la región secundaria, para que empiecen a replicarse en la región principal. 
- Una vez completada la reprotección y después de que las máquinas virtuales se estén replicando, puede realizar una conmutación por error de ellas desde la región secundaria a la principal.

> [!WARNING]
> Si [migró](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration) las máquinas de la región principal a la secundaria, movió la máquina virtual a otro grupo de recursos o eliminó la máquina virtual de Azure, puede reproteger la máquina virtual o realizar una conmutación por recuperación de ella.


## <a name="prerequisites"></a>requisitos previos
1. La conmutación por error de la máquina virtual de la región principal a la secundaria se debe confirmar.
2. El sitio de destino principal debe estar disponible y, además, debe tener la posibilidad de acceder o crear recursos en dicha región.

## <a name="reprotect-a-vm"></a>Reprotección de una máquina virtual

1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que se conmutó por error y, luego, seleccione **Volver a proteger**. La dirección de reprotección debe mostrarse desde la ubicación secundaria a la principal. 

  ![Reprotección](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Revise el grupo de recursos, la red, el almacenamiento y los conjuntos de disponibilidad. A continuación, haga clic en **Aceptar**. Si hay algún recurso marcado como nuevo, se crea como parte del proceso de reprotección.
3. Este trabajo de reprotección inicializa el sitio de destino con los datos más recientes. Una vez finalizado el proceso, se produce la replicación diferencial. Después, puede conmutar por recuperación al sitio principal. Puede seleccionar la cuenta de almacenamiento o la red que desea usar durante la reprotección mediante la opción de personalización.

  ![Opción de personalización](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalización de la configuración de reprotección

Puede personalizar las siguientes propiedades de la máquina virtual de destino durante la reprotección.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propiedad |Notas  |
|---------|---------|
|Grupo de recursos de destino     | Modifique el grupo de recursos de destino en que se crea la máquina virtual. Como parte de la reprotección, se elimina la máquina virtual de destino. Puede elegir un grupo de recursos nuevo en el que crear la máquina virtual tras la conmutación por error.        |
|Red virtual de destino     | No se puede cambiar la red de destino durante el trabajo de reprotección. Para cambiar la red, vuelva a hacer la asignación de red.         |
|Almacenamiento de destino     | Puede cambiar la cuenta de almacenamiento que la máquina virtual usa tras la conmutación por error.         |
|Almacenamiento en caché     | Puede especificar una cuenta de almacenamiento en caché para usarla durante la replicación. De forma predeterminada, se crea una cuenta de almacenamiento en caché en caso de que no exista.         |
|Conjunto de disponibilidad     |Si la máquina virtual de la región secundaria forma parte de un conjunto de disponibilidad, puede elegir un conjunto de disponibilidad para la máquina virtual de destino en la región principal. De forma predeterminada, Site Recovery intenta encontrar el conjunto de disponibilidad existente en la región principal para usarlo. Durante la personalización, puede especificar un nuevo conjunto de disponibilidad.         |


### <a name="what-happens-during-reprotection"></a>¿Qué ocurre durante la reprotección?

De forma predeterminada, sucede lo siguiente:

1. Se crea una cuenta de almacenamiento en caché en la región principal.
2. Si la cuenta de almacenamiento de destino (la cuenta de almacenamiento original de la región principal) no existe, se crea una. El nombre asignado para la cuenta de almacenamiento se corresponde con el nombre de la cuenta de almacenamiento que usa la máquina virtual secundaria, con el sufijo "asr".
3. Si el conjunto de disponibilidad de destino no existe, se crea uno como parte del trabajo de reprotección, en caso de que sea necesario. Si personalizó la configuración de reprotección, entonces se usa el conjunto seleccionado.

Cuando se desencadena un trabajo de reprotección y la máquina virtual de destino ya existe, ocurre lo siguiente:

1. Se crean los componentes necesarios como parte de la reprotección. Si ya existen, se vuelven a usar.
2. La máquina virtual de destino se apaga en caso de que esté en ejecución.
3. Site Recovery copia el disco de la máquina virtual de destino en un contenedor, como un blob de inicialización.
4. Después, se elimina la máquina virtual de destino.
5. La máquina virtual de origen actual (secundaria) usa el blob de inicialización para la replicación. Esto garantiza que solo se repliquen los valores delta.
6. Se sincronizan los cambios más importantes entre el disco de origen y el blob de inicialización. Esta operación puede tardar un tiempo en finalizar.
7. Una vez que finaliza el trabajo de reprotección, comienza la replicación diferencial y se crea un punto de recuperación según la directiva de replicación.
8. Si el trabajo de reprotección se completa satisfactoriamente, la máquina virtual adopta un estado protegido.

## <a name="next-steps"></a>pasos siguientes

Una vez protegida la máquina virtual, se puede iniciar una conmutación por error. La conmutación por error apaga la máquina virtual en la región secundaria y se crea y arranca la máquina virtual en la región principal, con un breve tiempo de inactividad. Se recomienda elegir una hora según corresponda y ejecutar una prueba de conmutación por error, pero iniciar una conmutación por error completa al sitio principal. [Más información](site-recovery-failover.md) sobre la conmutación por error

