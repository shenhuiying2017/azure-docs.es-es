---
title: "Replicación de aplicaciones (Azure en Azure) | Microsoft Docs"
description: "En este artículo se explica cómo configurar la replicación de máquinas virtuales que se ejecutan en una región de Azure en otra región de Azure."
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
ms.date: 12/08/2017
ms.author: asgang
ms.openlocfilehash: 209ec47388ee7291f8107df022e0c2bb202ba6b5
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicación de máquinas virtuales de Azure en otra región de Azure



>[!NOTE]
>
> La replicación de Site Recovery para máquinas virtuales de Azure está actualmente en vista previa.

En este artículo se explica cómo configurar la replicación de máquinas virtuales que se ejecutan en una región de Azure en otra región de Azure.

## <a name="prerequisites"></a>Requisitos previos

* El artículo da por hecho que ya conoce Site Recovery y el almacén de Recovery Services. Es necesario haber creado de antemano un "almacén de Recovery Services".

    >[!NOTE]
    >
    > Se recomienda crear el "almacén de Recovery Services" en la ubicación donde quiere que se repliquen las máquinas virtuales. Por ejemplo, si la ubicación de destino es "Centro de EE. UU.", cree el almacén en "Centro de EE. UU.".

* Si usa reglas de grupos de seguridad de red (NSG) o proxy de firewall para controlar el acceso a la conectividad saliente de Internet en las máquinas virtuales de Azure, asegúrese de incluir en la lista blanca las direcciones URL o IP necesarias. Vea el [documento de instrucciones de redes](./site-recovery-azure-to-azure-networking-guidance.md) para obtener más detalles.

* Si tiene una conexión ExpressRoute o VPN entre la ubicación local y de origen en Azure, siga el documento [Site Recovery Considerations for Azure to on-premises ExpressRoute / VPN configuration](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration) (Consideraciones de Site Recovery para Azure para la configuración local de ExpressRoute o VPN).

* La cuenta de usuario de Azure debe tener ciertos [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar la replicación de una máquina virtual de Azure.

* La suscripción de Azure debe estar habilitada para crear máquinas virtuales en la ubicación de destino que quiere usar como región de recuperación ante desastres. Puede ponerse en contacto con el soporte técnico para habilitar la cuota necesaria.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Habilitar la replicación desde el almacén de Azure Site Recovery
En este ejemplo se replicarán máquinas virtuales en ejecución en la ubicación "Asia Oriental" de Azure en la ubicación "Asia Suroriental". Los pasos son los siguientes:

 Haga clic en **+Replicar** en el almacén para habilitar la replicación de las máquinas virtuales.

1. **Origen:** hace referencia al punto de origen de las máquinas, que en este caso es **Azure**.

2. **Ubicación de origen:** es la región de Azure desde la que se quieren proteger las máquinas virtuales. En este ejemplo, la ubicación de origen es "Asia Oriental".

3. **Modelo de implementación:** hace referencia al modelo de implementación de Azure de las máquinas de origen. Puede seleccionar clásicas o de Resource Manager y las máquinas que pertenezcan al modelo en cuestión se enumerarán para su protección en el paso siguiente.

      >[!NOTE]
      >
      > Solo puede replicar una máquina virtual clásica y recuperarla como máquina virtual clásica. No puede recuperarla como máquina virtual de Resource Manager.

4. **Grupo de recursos:** es el grupo de recursos al que pertenecen las máquinas virtuales de origen. Todas las máquinas virtuales del grupo de recursos seleccionado se enumerarán para su protección en el paso siguiente.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

En **Máquinas virtuales > Seleccionar máquinas virtuales**, haga clic en cada máquina que quiera replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en Aceptar.
    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


En la sección Configuración puede configurar las propiedades del sitio de destino.

1. **Ubicación de destino:** es la ubicación donde se replicarán los datos de la máquina virtual de origen. Según la ubicación de las máquinas seleccionadas, Site Recovery proporcionará la lista de regiones de destino adecuadas.

    > [!TIP]
    > Se recomienda conservar la ubicación de destino igual que el almacén de Recovery Services.

2. **Grupo de recursos de destino:** se trata del grupo de recursos al que pertenecen todas las máquinas virtuales replicadas. De forma predeterminada, Azure Site Recovery crea un nuevo grupo de recursos en la región de destino con un nombre con el sufijo "asr". En caso de que el grupo de recursos que cree Azure Site Recovery ya exista, se volverá a usar. También puede elegir personalizarlo tal como se muestra en la sección siguiente.    
3. **Red virtual de destino:** de forma predeterminada, Azure Site Recovery crea una nueva red virtual en la región de destino con un nombre con el sufijo "asr". Esta se asignará a la red de origen y se usará para todas las protecciones futuras.

    > [!NOTE]
    > [Compruebe los detalles de redes](site-recovery-network-mapping-azure-to-azure.md) para obtener más información sobre la asignación de red.

4. **Cuentas de almacenamiento de destino:** de forma predeterminada, Azure Site Recovery crea la nueva cuenta de almacenamiento de destino mediante la imitación de la configuración de almacenamiento de la VM de origen. En caso de que ya exista la cuenta de almacenamiento creada por Azure Site Recovery, se volverá a usar.

5. **Cuentas de almacenamiento en caché:** Azure Site Recovery necesita una cuenta de almacenamiento adicional denominada almacenamiento en caché en la región de origen. Todos los cambios que se producen en las máquinas virtuales de origen se siguen y se envían a la cuenta de almacenamiento en caché antes de su replicación en la ubicación de destino.

6. **Conjunto de disponibilidad:** de forma predeterminada, Azure Site Recovery crea un nuevo conjunto de disponibilidad en la región de destino con un nombre con el sufijo "asr". En caso de que el conjunto de disponibilidad que cree Azure Site Recovery ya exista, se vuelve a usar.

7.  **Directiva de replicación:** define la configuración del historial de retención del punto de recuperación y de la frecuencia de instantánea coherente con la aplicación. De manera predeterminada, Azure Site Recovery crea una directiva de replicación nueva con la configuración predeterminada de 24 horas para la retención del punto de recuperación y 60 minutos para la frecuencia de instantánea coherente con la aplicación.

    ![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizar los recursos de destino

Si quiere cambiar los valores predeterminados que usa Azure Site Recovery, puede modificar la configuración según sus necesidades.

1. **Personalizar:** haga clic en esta opción para cambiar los valores predeterminados que usa Azure Site Recovery.

2. **Grupo de recursos de destino:** puede seleccionar el grupo de recursos en la lista de todos los grupos de recursos que existen en la ubicación de destino dentro de la suscripción.

3. **Virtual Network de destino:** puede encontrar la lista de todas las redes virtuales en la ubicación de destino.

4. **Conjunto de disponibilidad:** solo puede agregar una configuración de conjuntos de disponibilidad a las máquinas virtuales que forman parte de la disponibilidad en la región de origen.

5. **Cuentas de almacenamiento de destino:**

![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/customize.PNG) Haga clic en **Crear recurso de destino** y en Habilitar replicación


Una vez protegidas las máquinas virtuales, puede comprobar su estado en **Elementos replicados**

>[!NOTE]
>Durante el tiempo de replicación inicial es posible que el estado tarde en actualizarse y no se vea el progreso durante un tiempo. Puede hacer clic en el botón Actualizar de la parte superior de la hoja para obtener el estado más reciente.
>

![Habilitar replicación](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Pasos siguientes
- [Más información](site-recovery-test-failover-to-azure.md) sobre la ejecución de una conmutación por error de prueba.
- [Aprenda más](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.
- Más información sobre el [uso de planes de recuperación](site-recovery-create-recovery-plans.md) para reducir el RTO.
- Más información sobre la [reprotección de máquinas virtuales de Azure](site-recovery-how-to-reprotect.md) después de una conmutación por error.
