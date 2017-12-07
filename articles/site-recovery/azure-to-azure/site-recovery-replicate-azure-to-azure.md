---
title: "Replicación de máquinas virtuales de Azure a una región secundaria con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo replicar máquinas virtuales de Azure que se ejecutan en una región de Azure a otra región, mediante el servicio de Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicación de máquinas virtuales de Azure en otra región de Azure


En este artículo describe cómo replicar máquinas virtuales de Azure en una región de Azure a una región de Azure secundaria, mediante el servicio de Azure Site Recovery.

>[!NOTE]
>
> La replicación de máquinas virtuales de Azure con Site Recovery está actualmente en versión preliminar.

## <a name="prerequisites"></a>Requisitos previos

* Debe tener implementado un almacén de Recovery Services. Le recomendamos que cree el almacén en la región de destino a la que desea replicar las máquinas virtuales.
* Si usa reglas de grupos de seguridad de red (NSG) o proxy de firewall para controlar el acceso a la conectividad saliente de Internet en las máquinas virtuales de Azure, asegúrese de permitir las direcciones URL o IP necesarias. [Más información](./site-recovery-azure-to-azure-networking-guidance.md).
* Si tiene una conexión ExpressRoute o VPN entre el entorno local y la ubicación de origen en Azure, [obtenga información sobre cómo configurarlas](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Su cuenta de usuario de Azure debe tener [permisos específicos](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar la replicación de una máquina virtual de Azure.
Su suscripción de Azure debe estar habilitada para crear máquinas virtuales en la ubicación de destino que quiera usar como una región de recuperación ante desastres. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

## <a name="enable-replication"></a>Habilitar replicación

En este procedimiento, se usa Asia Oriental como la ubicación de origen, y Sudeste Asiático como el destino.

1. Haga clic en **+Replicar** en el almacén para habilitar la replicación de las máquinas virtuales.
2. Compruebe que **Origen:** está establecido en **Azure**.
3. Establezca **Ubicación de origen** en Asia Oriental.
4. En **Modelo de implementación**, seleccione **clásico** o **Resource Manager**.
5. En **Grupo de recursos**, seleccione el grupo al que pertenecen las máquinas virtuales de origen. Se muestran todas las máquinas virtuales bajo el grupo de recursos seleccionado.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. En **Máquinas virtuales > Seleccionar máquinas virtuales**, haga clic en cada máquina virtual que quiera replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. En **Configuración** > **Ubicación de destino**, especifique dónde se replican los datos de la máquina virtual de origen. Site Recovery proporciona una lista de regiones de destino adecuadas en función de la región de las máquinas virtuales seleccionadas.
8. Site Recovery establece la configuración de destino predeterminada. Estos valores se pueden modificar según sea necesario.

    - **Grupo de recursos de destino**. De forma predeterminada, Site Recovery crea un nuevo grupo de recursos en la región de destino con el sufijo "asr". Si el grupo de recursos que se crea ya existe, se vuelve a usar.
    - **Red virtual de destino**. De manera predeterminada, Site Recovery crea una red virtual en la región de destino con el sufijo "asr". Esta red está asignada a la red de origen. [más información](site-recovery-network-mapping-azure-to-azure.md) sobre la asignación de red.
    - **Cuentas de almacenamiento de destino**. De manera predeterminada, Site Recovery crea una cuenta de almacenamiento de destino nueva que coincide con la configuración del almacenamiento de máquina virtual de origen. Si la cuenta que se crea ya existe, se vuelve a usar.
    - **Cuentas de almacenamiento en caché**. Azure Site Recovery crea una cuenta de almacenamiento en caché adicional, la región de origen. Todos los cambios que se realicen en las máquinas virtuales de origen se siguen y se envían a la cuenta de almacenamiento en caché antes de su replicación en la ubicación de destino.
    - **Conjunto de disponibilidad**. De manera predeterminada, Site Recovery crea un conjunto de disponibilidad nuevo en la región de destino, con un sufijo "asr". Si el conjunto que se crea ya existe, se vuelve a usar.
    - **Directiva de replicación**. Site Recovery define la configuración del historial de retención del punto de recuperación y la frecuencia de instantánea coherente con la aplicación. De manera predeterminada, Site Recovery crea una directiva de replicación nueva con la configuración predeterminada de 24 horas para la retención del punto de recuperación y 60 minutos para la frecuencia de instantánea coherente con la aplicación.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Haga clic en **Habilitar replicación** para comenzar a proteger las máquinas virtuales.

## <a name="customize-target-resources"></a>Personalizar los recursos de destino

1. Modifique cualquiera de estos valores predeterminados de destino:

    - **Grupo de recursos de destino**. Seleccione cualquier grupo de recursos de la lista de todos los grupos de recursos en la ubicación de destino dentro de la suscripción.
    - **Red virtual de destino**. Seleccione de la lista de todas las redes virtuales en la ubicación de destino.
    - **Conjunto de disponibilidad** Solo puede agregar la configuración de conjuntos de disponibilidad a las máquinas virtuales ubicadas en un conjunto de la región de origen.
    - **Cuentas de almacenamiento de destino**: agregue cualquier cuenta disponible.

        ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Haga clic en **Crear recurso de destino** > **Habilitar replicación**. Durante la replicación inicial, el estado de la máquina virtual puede tardar un tiempo en actualizarse. Haga clic en **Actualizar** para obtener el estado más reciente.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. Una vez que se protejan las máquinas virtuales, compruebe el mantenimiento de la máquina virtual en **Elementos replicados**.



## <a name="next-steps"></a>Pasos siguientes
[Obtenga información](../azure-to-azure-tutorial-dr-drill.md) sobre cómo ejecutar una conmutación por error de prueba.

