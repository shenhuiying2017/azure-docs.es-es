---
title: "Replicación de máquinas virtuales de Azure entre regiones de Azure para las necesidades de recuperación ante desastres: de Azure a Azure | Microsoft Docs"
description: "Se resumen los pasos que necesarios para replicar máquinas virtuales de Azure entre regiones de Azure (de Azure a Azure) con el servicio de Azure Site Recovery para las necesidades de recuperación ante desastres."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Replicación de máquinas virtuales de Azure entre regiones con Azure Site Recovery

>[!NOTE]
>
> La replicación de Azure Site Recovery en máquinas virtuales (VM) de Azure está actualmente en versión preliminar.

En este artículo se describe cómo replicar máquinas virtuales de Azure entre regiones de Azure mediante el servicio de [Site Recovery](site-recovery-overview.md) de Azure Portal.

Cualquier comentario o pregunta que tenga puede publicarlo en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Recuperación ante desastres en Azure

Las características y funcionalidades de infraestructura de Azure integradas contribuyen a una estrategia de disponibilidad sólida y resistente para las cargas de trabajo que se ejecutan en máquinas virtuales de Azure. Sin embargo, hay muchas razones por las que deberá planear por su cuenta la recuperación ante desastres entre regiones de Azure:

- Debe cumplir las instrucciones de cumplimiento para aplicaciones y cargas de trabajo específicas que requieren una estrategia de continuidad empresarial y recuperación ante desastres (BCDR).
- Quiere la posibilidad de proteger y recuperar máquinas virtuales de Azure en función de sus decisiones empresariales y no solo de la funcionalidad de Azure integrada.
- Debe probar la conmutación por error y la recuperación de acuerdo con las necesidades empresariales y de cumplimiento, sin afectar al entorno de producción.
- Debe conmutar por error a la región de recuperación en caso de desastre y conmutar por recuperación a la región de origen original sin problemas.

Use Site Recovery para la replicación de máquinas virtuales de Azure a Azure a fin de ayudarle a realizar todas estas tareas.


## <a name="why-use-site-recovery"></a>¿Por qué usar Site Recovery?      

Site Recovery ofrece una manera sencilla de replicar máquinas virtuales de Azure entre regiones:

- **Implementación automática**. A diferencia de un modelo de replicación activo/activo, no hay necesidad de una infraestructura cara y compleja en la región secundaria. Cuando se habilita la replicación, Site Recovery crea automáticamente los recursos necesarios en la región de destino, en función de la configuración de la región de origen.
- **Controla de las regiones**. Con Site Recovery, puede replicar de una región a otra dentro de un continente. En comparación con esto, el almacenamiento con redundancia geográfica con acceso de lectura replica de forma asincrónica solo entre [regiones emparejadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) estándar. El almacenamiento con redundancia geográfica con acceso de lectura proporciona acceso de solo lectura a los datos de la región de destino.
- **Replicación automatizada**. Site Recovery proporciona replicación continua automatizada. Con un solo clic es posible desencadenar la conmutación por error y por recuperación.
- **RTO y RPO**. Site Recovery aprovecha la infraestructura de red de Azure que conecta las regiones para mantener el RTO y el RPO en unos niveles muy bajos.
- **Prueba**. Puede ejecutar maniobras de recuperación ante desastres con conmutaciones por error de prueba a petición, como y cuando sea necesario, sin que las cargas de trabajo de producción o la replicación en curso se vean afectadas.
- **Planes de recuperación**. Puede usar planes de recuperación para orquestar la conmutación por error y la conmutación por recuperación de toda la aplicación que se ejecuta en varias máquinas virtuales. La característica del plan de recuperación presenta una completa integración de primera clase con los runbooks de automatización de Azure.


## <a name="deployment-summary"></a>Resumen de implementación

A continuación se muestra un resumen de lo que debe hacer para configurar la replicación de las máquinas virtuales entre regiones de Azure:

1. Cree un almacén de Recovery Services. El almacén contiene valores de configuración y orquesta la replicación.
2. Habilite la replicación en las máquinas virtuales de Azure.
3. Ejecute una conmutación por error de prueba para asegurarse de que todo funcione según lo esperado.

>[!IMPORTANT]
>
> Puede comprobar la [matriz de compatibilidad de la replicación de máquinas virtuales de Azure](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> Para más información sobre cómo configurar la conectividad de red de salida necesaria en las máquinas virtuales de Azure para la replicación de Site Recovery, consulte el [documento de instrucciones para redes](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Antes de comenzar

* Su cuenta de usuario de Azure debe tener ciertos [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar la replicación de una máquina virtual en Azure.
* Su suscripción de Azure debe estar habilitada para crear máquinas virtuales en la ubicación de destino que quiera usar como región de recuperación ante desastres. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Le recomendamos que cree el almacén de Recovery Services en la ubicación donde quiere que se repliquen las máquinas virtuales. Por ejemplo, si la ubicación de destino está en el centro de EE. UU., crear el almacén en **Centro de EE. UU**.

## <a name="enable-replication"></a>Habilitar replicación

En **Almacenes de Recovery**, haga clic en el nombre del almacén. En el almacén, haga clic en el botón **+ +Replicar** botón.

### <a name="step-1-configure-the-source"></a>Paso 1. Configuración del origen
1. En **Origen**, seleccione **Azure - VERSIÓN PRELIMINAR**.
2. En **Ubicación de origen**, seleccione la región de Azure de origen donde se ejecutan actualmente sus máquinas virtuales.
3. Seleccione el modelo de implementación de las máquinas virtuales: **Resource Manager** o **Clásico**.
4. Seleccione el **grupo de recursos de origen** para las máquinas virtuales de Resource Manager o **servicio en la nube** para las máquinas virtuales clásicas.

    ![Configuración del origen](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>Paso 2: Selección de máquinas virtuales

* Seleccione las máquinas virtuales que quiere replicar y, a continuación, haga clic en **Aceptar**.

    ![Selección de las máquinas virtuales](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>Paso 3: Definición de la configuración

1. Para invalidar la configuración de destino predeterminada y especificar la configuración de su elección, haga clic en **Personalizar**. Para más información, consulte [Customize target resources](site-recovery-replicate-azure-to-azure.md##customize-target-resources) (Personalización de los recursos de destino).

    ![Definición de la configuración](./media/site-recovery-azure-to-azure/settings.png)


2. De forma predeterminada, Site Recovery crea una directiva de replicación que toma cada 4 horas instantáneas coherentes con la aplicación y conserva los puntos de recuperación durante 24 horas. Para crear una directiva con una configuración diferente, haga clic en **Personalizar** junto a **Directiva de replicación**.

    ![Personalización de la directiva](./media/site-recovery-azure-to-azure/customize-policy.png)

3. Para iniciar el aprovisionamiento de los recursos de destino, haga clic en **Crear recursos de destino**. El aprovisionamiento tarda aproximadamente un minuto. No cierre la hoja durante la operación o deberá empezar de nuevo.

4. Para desencadenar la replicación de la máquina virtual seleccionada, haga clic en **Habilitar la replicación**.

5. Puede hacer un seguimiento del progreso del trabajo **Habilitar la protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.

6. En **Configuración** > **Elementos replicados**, puede ver el estado de las máquinas virtuales y el progreso inicial de la replicación. Haga clic en la máquina virtual para ir a los detalles de su configuración.

## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

Una vez terminada la configuración, ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada:

1. Para conmutar por error una sola máquina, en **Configuración** > **Elementos replicados**, haga clic en a máquina virtual **+Conmutación por error de prueba**.

2. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan **Conmutación por error de prueba**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md). 

3. En **Conmutación por error de prueba**, seleccione la red virtual de Azure a la que están conectadas las máquinas virtuales después de que se produce la conmutación por error.

4. Para iniciar la conmutación por error, haga clic en **Aceptar**. Para realizar el seguimiento del progreso, haga clic en la máquina virtual para abrir sus propiedades. También puede hacer clic en el trabajo **Conmutación por error de prueba** en el nombre del almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.

5. Una vez finalizada la conmutación por error, la máquina de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Debe asegurarse de que la VM tiene el tamaño adecuado, que está conectada a la red correspondiente y que se está ejecutando.

6. Para eliminar las máquinas virtuales que se crearon durante la conmutación por error de prueba, haga clic en **Cleanup test failover** (Limpiar conmutación por error de prueba) en el artículo replicado o el plan de recuperación. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba. 

[Más información](site-recovery-test-failover-to-azure.md) sobre las conmutaciones por error de prueba.


## <a name="next-steps"></a>Pasos siguientes

Después de probar la implementación:

- [Obtenga más información](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.
- Aprenda sobre el [uso de planes de recuperación](site-recovery-create-recovery-plans.md) para reducir el RTO.
