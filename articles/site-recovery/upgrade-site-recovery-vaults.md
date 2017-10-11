---
title: "Actualización de un almacén de Site Recovery en un almacén de Recovery Services"
description: "Aprenda a actualizar el almacén de Azure Site Recovery en un almacén de Recovery Services"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Actualización de un almacén de Site Recovery en un almacén de Recovery Services basado en Azure Resource Manager

En este artículo se describe cómo actualizar almacenes de Azure Site Recovery en almacenes de Recovery Service basados en Azure Resource Manager sin que ello influya en la replicación en curso. Para más información sobre las características y las ventajas de Azure Resource Manager, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introducción
El almacén de Recovery Services es un recurso de Azure Resource Manager para administrar las operaciones de copia de seguridad y recuperación ante desastres de forma nativa en la nube. Se trata de un almacén unificado que puede usarse en la nueva versión de Azure Portal, que reemplaza a los almacenes clásicos de Backup y Site Recovery.

Los almacenes de Recovery Services ofrecen diversas características, como son:

* Soporte técnico de Azure Resource Manager: puede proteger las máquinas virtuales y las máquinas físicas en la pila de Azure Resource Manager y realizar una conmutación por error de ellas.

* Excluir el disco: si tiene archivos temporales o datos de renovación elevada que no desea que usen todo el ancho de banda, puede excluir volúmenes de la replicación. Esta funcionalidad se ha habilitado actualmente en *VMware a Azure* y en *Hyper-V a Azure*, y pronto se extenderá también a otros escenarios.

* Soporte técnico del almacenamiento con redundancia local (LRS) y Premium Storage: ahora puede proteger los servidores en cuentas de Premium Storage que permiten a los clientes proteger las aplicaciones con más operaciones de E/S por segundo. Esta funcionalidad se ha habilitado actualmente en *VMware a Azure*.

* Experiencia de iniciación simplificada: la experiencia de iniciación mejorada se ha diseñado para facilitar la configuración de la recuperación ante desastres.

* Administración de Backup y Site Recovery desde el mismo almacén: ahora puede proteger los servidores para la recuperación ante desastres o la realización de copias de seguridad desde el mismo almacén, lo que permite reducir significativamente la sobrecarga de administración.

Para más información sobre las características y experiencia actualizadas, consulte el [blog de almacenamiento, copia de seguridad y recuperación](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Características más destacadas

* Ningún impacto en la replicación en curso: las replicaciones en curso continúan sin ninguna interrupción durante la actualización y después.

* Sin costo adicional: obtenga un conjunto completo de funcionalidades actualizadas sin costo adicional alguno.

* Sin pérdida de datos: puesto que este proceso es una actualización y no una migración, la configuración y los puntos de recuperación de la replicación existentes permanecen intactos durante la actualización y después.


## <a name="what-happens-during-the-vault-upgrade"></a>¿Qué ocurre durante la actualización del almacén?

Durante la actualización, no puede realizar operaciones tales como registrar un nuevo servidor o habilitar la replicación para una máquina virtual (VM). Las operaciones que conlleven la lectura de datos del almacén o la escritura de datos en él, como la replicación en curso de elementos protegidos en el almacén, continúan sin interrupciones.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Cambios en la automatización y herramientas después de la actualización
A medida que actualiza el tipo de almacén del modelo de implementación clásica al de Resource Manager, actualice la automatización o las herramientas existentes para asegurarse de que siguen funcionando después de la actualización.

### <a name="prepare-your-environment-for-the-upgrade"></a>Preparación del entorno para la actualización

* [Instale PowerShell o actualícelo a la versión 5 o posterior](https://www.microsoft.com/download/details.aspx?id=50395)
* [Instale la versión más reciente de Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases)
* [Descargue el script de actualización del almacén de Recovery Services](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Requisitos previos
Para actualizar los almacenes de Site Recovery en almacenes de Recovery Services basados en Azure Resource Manager, se deben cumplir los siguientes requisitos previos:

* Versión mínima del agente: la versión del proveedor de Azure Site Recovery instalada en el servidor sea la 5.1.1700.0, o una posterior.

* Configuración admitida: no se puede configurar el almacén con la red de área de almacenamiento (SAN) o grupos de disponibilidad AlwaysOn de SQL Server. Se admiten todas las demás configuraciones.

    >[!NOTE]
    >Después de la actualización, puede administrar la asignación de almacenamiento solo a través de PowerShell.

* Escenario de implementación admitido: el almacén no debe estar en el modelo de implementación heredada *VMware a Azure*. Antes de continuar, pase primero al modelo de implementación mejorada.

* Ningún trabajo activo iniciado por el usuario que conlleve operaciones de planeamiento de administración: como el acceso al plan de administración está restringido durante la actualización, complete todas las acciones de planeamiento de la administración antes de activar la actualización. Este proceso no incluye la replicación en curso.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Esta actualización afecta a la replicación en curso?**

No. La replicación en curso continúa sin interrupciones durante la actualización y después.

**¿Qué ocurre con la configuración de red, como la VPN de sitio a sitio o la configuración de la dirección IP?**

La actualización no afecta a la configuración de red. Todas las conexiones de Azure a local permanecen intactas.

**¿Qué ocurre con los almacenes si se prevé realizar una actualización próximamente?**

El almacén de Site Recovery en la versión anterior de Azure Portal dejará de ser admitido partir de septiembre de 2017. Se recomienda encarecidamente que use la característica de actualización para migrar al nuevo portal.

**¿Qué supone este plan de migración para las herramientas existentes?**  

La actualización de sus herramientas al modelo de implementación de Resource Manager es uno de los cambios más importantes que debe considerar en sus planes de actualización. Los almacenes de Recovery Services se basan en el modelo de implementación de Resource Manager. 

**¿Cuánto dura el tiempo de inactividad del plan de administración?**

La actualización normalmente tarda entre 15 y 30 minutos, y puede tardar hasta una hora, como máximo.

**¿Puedo revertir la actualización?**

No. No se admite la reversión una vez actualizados correctamente los recursos.

**¿Puedo validar mi suscripción o mis recursos para comprobar si se pueden actualizar?**

Sí. En la opción de actualización compatible con la plataforma, el primer paso para la actualización consiste en validar si los recursos pueden actualizarse. Si la validación no puede realizarse, se recibirán las advertencias o mensajes de error apropiados.

**¿Cómo informo de un problema con la actualización?**

Si experimenta errores durante la actualización, anote el identificador de operación que se muestra en el error. El soporte técnico de Microsoft trabajará proactivamente para resolver el problema. También puede ponerse en contacto con el equipo de soporte técnico y proporcionarle el identificador de la suscripción, el nombre del almacén y el identificador de la operación. Se procurará resolver el problema lo antes posible. No vuelva a intentar la operación a menos que Microsoft se lo indique explícitamente.

## <a name="run-the-script"></a>Ejecute el script

En PowerShell, ejecute el siguiente comando:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionId: identificador de la suscripción asociado con el almacén que se va a actualizar.

* VaultName: nombre del almacén que se va a actualizar.

* Location: ubicación del almacén que se va a actualizar.

* ResourceType: valor de HyperVRecoveryManagerVault para los almacenes de Site Recovery.

* TargetResourceGroupName: grupo de recursos en el que se desea colocar el almacén actualizado. El valor de TargetResourceGroupName puede ser un grupo de recursos existente de Azure Resource Manager u otro nuevo. En caso de que el valor de TargetResourceGroupName proporcionado no exista, se crea como parte de la actualización en la misma ubicación que el almacén. Para más información, vea la sección "Grupos de recursos" en [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >La denominación de los grupos de recursos está sujeta a determinadas restricciones. Para evitar errores durante la actualización del almacén, asegúrese de seguir la convención de nomenclatura.
    >
    >Por ejemplo:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

Como alternativa, puede ejecutar el script siguiente. Escriba los valores para los parámetros necesarios.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. El script de PowerShell le pide que escriba sus credenciales. Escríbalas dos veces, una para la cuenta del modelo de implementación clásica y otra para la cuenta de Azure Resource Manager.

2. Después de especificar las credenciales, el script ejecuta una comprobación para determinar si la instalación de la infraestructura cumple los requisitos mencionados anteriormente.

3. Después de comprobar y confirmar los requisitos previos, se le pide que continúe con la actualización del almacén. En el proceso de actualización se empieza a actualizar el almacén. La actualización completa puede durar entre 15 y 30 minutos.

4. Cuando la actualización haya finalizado correctamente, puede acceder al almacén actualizado en la nueva versión de Azure Portal.

## <a name="post-upgrade-vault-management"></a>Administración del almacén posterior a la actualización

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Replicación mediante Azure Site Recovery en el almacén Recovery Services

* Ahora puede proteger las máquinas virtuales de Azure de una región a otra. Para más información, consulte [Replicación de máquinas virtuales de Azure entre regiones con Azure Site Recovery](site-recovery-azure-to-azure.md).

* Para más información acerca de la replicación de máquinas virtuales VMware en Azure, consulte [Replicación de máquinas virtuales de VMware en Azure con Site Recovery](vmware-walkthrough-overview.md).

* Para más información acerca de la replicación de máquinas virtuales de Hyper-V (sin WMM) en Azure, consulte [Replicación de máquinas virtuales de Hyper-V (sin WMM) en Azure](hyper-v-site-walkthrough-overview.md).

* Para más información acerca de la replicación de máquinas virtuales de Hyper-V (con WMM) en Azure, consulte [Replicación de máquinas virtuales de Hyper-V que están en nubes VMM en Azure mediante Site Recovery en Azure Portal](vmm-to-azure-walkthrough-overview.md).

* Para más información acerca de la replicación de máquinas virtuales de Hyper-VM (con WMM) en un sitio secundario, consulte [Replicación de máquinas virtuales de Hyper-V que están en nubes VMM en un sitio VMM secundario mediante Azure Portal](site-recovery-vmm-to-vmm.md).

* Para más información sobre la replicación de máquinas virtuales de VMware en un sitio secundario, consulte [Replicación de máquinas virtuales locales de VMware o de servidores físicos en un sitio secundario en el Portal de Azure clásico](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>Visualización de los elementos replicados

La imagen siguiente muestra la página del panel de información del almacén de Recovery Services con las entidades clave del almacén. Para ver la lista de entidades protegidas en el almacén, haga clic en **Site Recovery** > **Elementos replicados**.


![Elementos replicados](./media/upgrade-site-recovery-vaults/replicateditems.png)

La siguiente imagen muestra el flujo de trabajo para ver los elementos replicados y el comando **Failover** para iniciar una conmutación por error.

![Elementos replicados](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>Visualización de la configuración de replicación

En el almacén de Site Recovery, cada grupo de protección se configura con la frecuencia de copia, retención de punto de recuperación, frecuencia de las instantáneas coherentes de la aplicación, y otras opciones de replicación. En el almacén de Recovery Services, estas opciones se configuran como una directiva de replicación. El nombre de la directiva es el del grupo de protección o *primarycloud_Policy*.

Para más información acerca de la directiva de replicación, consulte [Administración de una directiva de replicación de VMware en Azure](site-recovery-setup-replication-settings-vmware.md).
