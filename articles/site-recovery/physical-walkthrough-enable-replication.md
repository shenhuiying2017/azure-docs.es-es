---
title: "Habilitación de replicación para replicar servidores físicos en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para habilitar la replicación en Azure para máquinas virtuales de servidores físicos con el servicio de Azure Site Recovery"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>Paso 10: Habilitación de la replicación de servidores físicos en Azure


En este artículo, se describe cómo habilitar la replicación de servidores físicos Windows/Linux locales en Azure con el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de comenzar

- Los servidores deben tener [instalado el componente Mobility Service](physical-walkthrough-install-mobility.md).
- Si se prepara una máquina virtual para la instalación de inserción, el servidor de procesos instala automáticamente el servicio de movilidad cuando habilita la replicación.
- Cuando se habilita una máquina para la replicación, su cuenta de usuario de Azure necesita [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) específicos para asegurarse de que puede usar Azure Storage y crear máquinas virtuales de Azure.
- Cuando agregue o modifique direcciones IP para servidores, los cambios pueden tardar 15 minutos o más en aplicarse y aparecer en el portal.


## <a name="exclude-disks-from-replication"></a>Excluir discos de la replicación

De manera predeterminada, se replican todos los discos de una máquina. Puede excluir discos de la replicación. Por ejemplo, es posible que no desee replicar los discos con datos temporales, o los datos que se actualizan cada vez que una máquina o aplicación se reinicia (por ejemplo, pagefile.sys o tempdb de SQL Server). [Más información](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>Replicación de servidores

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas físicas**.
4. Seleccione el servidor de procesos. Si no ha creado ningún servidor de procesos adicional, este será el servidor de configuración. A continuación, haga clic en **Aceptar**.
5. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.
6. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación. Si no desea usar una cuenta que ya haya configurado, puede crear una.
7. Seleccione la **red** y la **subred de Azure** a la que se conectarán las VM de Azure después de la conmutación por error. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. Si no desea usar una red existente, puede crear una.

    ![Habilitar replicación](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. En **Máquinas físicas**, haga clic en **Agregar máquina física** y escriba el **Nombre** y la **Dirección IP**. Elija el sistema operativo de la máquina que quiere replicar. Se tardará unos minutos hasta que las máquinas se detecten y se muestren en la lista.
9. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que utilizará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.
10. De manera predeterminada se replican todos los discos. Haga clic en **Todos los discos** y desactive todos los discos que no desea replicar. y, a continuación, haga clic en **Aceptar**. Puede establecer otras propiedades de las máquinas virtuales más adelante.

    ![Habilitar replicación](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta. Si modifica una directiva, los cambios se aplicarán a la máquina que se replica y a las nuevas máquinas.
12. Habilite la **coherencia de múltiples VM** si desea recopilar las máquinas en un grupo de replicación y especifique un nombre para el grupo. y, a continuación, haga clic en **Aceptar**. Observe lo siguiente:

    * Todas las máquinas de un grupo de replicación se replican al mismo tiempo y comparten puntos de recuperación coherentes con los bloqueos y con la aplicación cuando conmutan por error.
    * Se recomienda que recopile los servidores físicos juntos para que reflejen las cargas de trabajo. Habilitar la coherencia de múltiples máquinas virtuales puede afectar al rendimiento de la carga de trabajo y solo debe utilizarse si las máquinas ejecutan la misma carga de trabajo y necesita coherencia.

13. Haga clic en **Enable Replication**. Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .

## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 11: Ejecución de una conmutación por error de prueba](physical-walkthrough-test-failover.md)
