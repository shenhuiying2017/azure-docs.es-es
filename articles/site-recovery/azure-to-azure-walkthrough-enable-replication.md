---
title: "Habilitación de la replicación para máquinas virtuales de Azure en otra región de Azure con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos que hay que seguir para habilitar la replicación en otra región de Azure para máquinas virtuales de Azure con el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.openlocfilehash: 8691ff9dbdb79ef445482b355b00859072d539e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-enable-replication-for-azure-vms"></a>Paso 5: Habilitación de la replicación para máquinas virtuales de Azure


Después de configurar un [almacén de Recovery Services](azure-to-azure-walkthrough-vault.md), use este artículo para habilitar la replicación de máquinas virtuales (VM) en otra región de Azure, con [Azure Site Recovery](site-recovery-overview.md). Para habilitar la replicación, configure los valores de origen y destino, compruebe la directiva de replicación y seleccione las máquinas virtuales que desee replicar.

- Cuando termine el artículo, las máquinas virtuales de Azure se debe replicar en la región secundaria de Azure.
- Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

>[!NOTE]
>
> La replicación de la máquina virtual de Azure actualmente se encuentra en versión preliminar.


## <a name="select-the-source"></a>Seleccione el origen

1. En los almacenes de Recovery Services, haga clic en el nombre del almacén y > **+Replicar**. 
2. En **Origen**, seleccione **Azure - VERSIÓN PRELIMINAR**.
2. En **Ubicación de origen**, seleccione la región de Azure de origen donde se ejecutan actualmente sus máquinas virtuales.
3. Seleccione el **modelo de implementación de las máquinas virtuales de Azure**: **Resource Manager** o **Clásico**.
4. Seleccione el **grupo de recursos de origen** para las máquinas virtuales de Resource Manager o el **servicio en la nube** para las máquinas virtuales clásicas.
5. Haga clic en **Aceptar** para guardar la configuración.

    ![Configuración del origen](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>Seleccione las máquinas virtuales

Site Recovery recupera una lista de las máquinas virtuales asociadas a la suscripción y el servicio en la nube/grupo de recursos.

1. En **Máquinas virtuales**, seleccione las máquinas virtuales que quiere replicar.
2. Haga clic en **Aceptar**.

    ![Selección de las máquinas virtuales](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>Definición de la configuración

Configuración predeterminada de las disposiciones de Site Recovery para la región de destino (según la configuración de región de origen) y la directiva de replicación:

   - **Ubicación de destino**: La región de destino que desea usar para la recuperación ante desastres. Se recomienda que la ubicación de destino coincida con la ubicación del almacén de Site Recovery.
   - **Grupo de recursos de destino**: Grupo de recursos al que las máquinas virtuales de Azure de la región de destino pertenecerán después de la conmutación por error. De forma predeterminada, Site Recovery crea un nuevo grupo de recursos en la región de destino con un sufijo "asr".
   - **Red virtual de destino**: La red en las máquinas virtuales de Azure en la región de destino se ubicará después de la conmutación por error. De forma predeterminada, Site Recovery crea una nueva red (y subredes) virtual en la región de destino con un sufijo "asr". Esta red está asignada a la red de origen. Tenga en cuenta que puede asignar una dirección IP específica después de la conmutación por error de una máquina virtual, si tiene que conservar la misma dirección IP en las ubicaciones de origen y de destino.
   - **Cuentas de almacenamiento en caché**: Site Recovery utiliza una cuenta de almacenamiento en la región de origen. Los cambios en máquinas virtuales de origen se envían a esta cuenta, antes de la réplica en la ubicación de destino.
   - **Cuentas de almacenamiento de destino**: De forma predeterminada, Site Recovery crea una nueva cuenta de almacenamiento en la región de destino, para reflejar la cuenta de almacenamiento de la máquina virtual de origen.
   -  **Conjuntos de disponibilidad de destino:** De forma predeterminada, Site Recovery creará un nuevo conjunto de disponibilidad en la región de destino con el sufijo "asr".
   - **Nombre de la directiva de replicación**: Nombre de la directiva.
   - **Retención del punto de recuperación**: De forma predeterminada, Site Recovery conserva los puntos de recuperación durante 24 horas. Puede configurar un valor entre 1 y 72 horas.
   - **Frecuencia de instantáneas coherentes con la aplicación**: De forma predeterminada, Site Recovery toma una instantánea coherente con la aplicación cada 4 horas. Puede configurar cualquier valor entre 1 y 12 horas. Los datos se replican continuamente:
    - Los puntos de recuperación coherentes para el bloqueo mantienen una orden de escritura de datos coherente cuando se crea. Este tipo de punto de recuperación generalmente es suficiente si la aplicación está diseñada para recuperarse de un bloqueo sin incoherencias en los datos
    - Los puntos de recuperación coherentes para el bloqueo se generan cada pocos minutos. Con estos puntos de recuperación para conmutar por error y recuperar las máquinas virtuales se proporciona un objetivo de punto de recuperación (RPO) del orden de minutos.
    - Puntos de recuperación coherentes con la aplicación (además de la coherencia de orden de escritura) aseguran que las aplicaciones de ejecución completan todas las operaciones y vuelcan los búferes en el disco (modo de inactividad de aplicación). Se recomienda usar estos puntos de recuperación para aplicaciones de base de datos como SQL Server, Oracle y Exchange.

    ![Definición de la configuración](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>Modificar la configuración

Si desea modificar la configuración de directiva de replicación y de destino, realice lo siguiente:

1. Para ver o modificar la configuración de destino, haga clic en **Configuración**.
2. Para invalidar la configuración de destino de forma predeterminada, haga clic en **Personalizar**. Puede especificar un grupo de recursos de destino, la red virtual, el conjunto de disponibilidad y la cuenta de almacenamiento de destino. Solo puede agregar conjuntos de disponibilidad si las máquinas virtuales forman parte de un conjunto en la región de origen.

    ![Definición de la configuración](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. Para invalidar la configuración de replicación de instantáneas coherentes con la aplicación y los puntos de recuperación, haga clic en **Personalizar** junto a **Directiva de replicación**.

    ![Definición de la configuración](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. Para iniciar el aprovisionamiento de los recursos de destino, haga clic en **Crear recursos de destino**. El aprovisionamiento tarda aproximadamente un minuto. No cierre la hoja durante la operación o deberá empezar de nuevo.




## <a name="enable-replication"></a>Habilitar replicación

1. En **Configuración**, haga clic en **Habilitar replicación**. Esto permite la replicación inicial de las máquinas virtuales que ha seleccionado. El estado de la replicación inicial puede tardar algún tiempo en actualizarse. Haga clic en **Actualizar** para obtener el estado más reciente.

2. Puede hacer un seguimiento del progreso del trabajo **Habilitar la protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.

3. En **Configuración** > **Elementos replicados**, puede ver el estado de las máquinas virtuales y el progreso inicial de la replicación. Haga clic en la máquina virtual para ir a los detalles de su configuración.



## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 6: Ejecución de una conmutación por error de prueba](azure-to-azure-walkthrough-test-failover.md)
