---
title: "Actualización del almacén de Site Recovery al almacén de Recovery Services"
description: "Obtenga información sobre cómo actualizar el almacén de Azure Site Recovery al almacén de Recovery Services"
ddocumentationcenter: 
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
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: es-es
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>Actualización de los almacenes de Site Recovery en Azure Resource Manager basada en los almacenes de Recovery Services

En este artículo se describe cómo realizar la actualización de "Almacenes de Site Recovery" en Azure Resource Manager basada en "Almacenes de Recovery Services" sin que ello influya en la replicación en curso. Se puede leer más información sobre las características y los beneficios de Azure Resource Manager [aquí](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introducción
El almacén de Recovery Services es un recurso de Azure Resource Manager para administrar las operaciones de copia de seguridad y recuperación ante desastres necesarias de forma nativa en la nube. Se trata de almacén unificado que puede usarse en la nueva versión de Azure Portal, que reemplaza a los almacenes clásicos de Backup y Site Recovery.

Los almacenes de Recovery Services abren una matriz de características, entre ellas:

-   Soporte técnico de Azure Resource Manager: puede proteger las máquinas virtuales y las máquinas físicas en la pila de Azure Resource Manager y realizar una conmutación por error de ellas.

-   Excluir el disco: si tiene archivos temporales o datos de renovación elevada que no desea que se utilicen en el ancho de banda, puede excluir volúmenes de la replicación. Esta funcionalidad se ha habilitado actualmente en "VMware a Azure" y en "Hyper-V a Azure" y pronto se extendería también a otros escenarios.

- Soporte técnico para Premium Storage y almacenamiento con redundancia local (LRS): ahora puede proteger los servidores en cuentas de Premium Storage que permiten a los clientes proteger las aplicaciones con más E/S por segundo. Esta funcionalidad se ha habilitado actualmente en "VMware a Azure".

-   Experiencia de "Introducción" simplificada: la experiencia de introducción mejorada se ha adaptado para garantizar que la configuración de la recuperación ante desastres sea un proceso sencillo.

- Administración de Backup y Site Recovery desde el mismo almacén: ahora puede proteger los servidores para la recuperación ante desastres o la realización de copias de seguridad desde el mismo almacén, lo que permite reducir significativamente la sobrecarga de administración.

Para obtener más detalles sobre las características y la experiencia actualizadas, consulte este [blog](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Características más destacadas

- **Ningún impacto en la replicación en curso**: las replicaciones en curso continúan sin ninguna interrupción durante la actualización y después de ella.

- **Sin ningún costo adicional**: experimente un conjunto completo de nuevas funcionalidades sin ningún costo adicional.

- **Sin pérdida de datos**: puesto que se trata de una actualización y no de una migración, la información de replicación existente (puntos de recuperación, configuración de replicación, etc.) permanece intacta durante la actualización y después de ella.


## <a name="what-happens-during-the-upgrade"></a>¿Qué ocurre durante la actualización?

Durante la actualización, no se admiten operaciones como el registro de un servidor nuevo, habilitar la replicación de una máquina virtual, etc. Todas las operaciones que conlleven solamente la lectura de datos del almacén o la escritura de datos en él, como la replicación en curso de elementos protegidos en el almacén, continuarán sin interrupciones.

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>Cambios en la automatización y las herramientas después la actualización del almacén
Como parte del proceso de actualización del tipo de almacén del modelo de implementación clásica al de Resource Manager, debe actualizar la automatización o las herramientas existentes para asegurarse de que siguen funcionando después de la actualización.

## <a name="preparing-your-environment-for-vault-upgrade"></a>Preparación del entorno para la actualización del almacén

1.  Instale PowerShell o actualícelo a la versión 5 o posterior en este [vínculo](https://www.microsoft.com/download/details.aspx?id=50395).

2.  Instale el último archivo MSI de Azure PowerShell desde [aquí](https://github.com/Azure/azure-powershell/releases).

3.  [Descargue](https://aka.ms/vaultupgradescript) el script para la actualización del almacén.

## <a name="prerequisites-for-upgrade"></a>Requisitos previos para la actualización
Para actualizar los almacenes de Site Recovery a almacenes de Recovery Services basados en Azure Resource Manager, se deben cumplir los siguientes requisitos previos.

- Versión mínima del agente: para realizar la actualización, es necesario que la versión del proveedor de Azure Site Recovery instalada en el servidor sea la 5.1.1700.0 como mínimo.

- Configuración admitida: el almacén no debe configurarse con SAN ni con los Grupos de disponibilidad AlwaysOn de SQL. Se admiten todas las demás configuraciones.

>[!NOTE]
> La asignación de almacenamiento solo puede administrarse a través de PowerShell después de la actualización.

- Escenario de implementación compatible: el almacén no debe estar en el modelo de implementación heredada "VMware a Azure".  Primero debe cambiar al modelo de implementación mejorada antes de continuar.

- Ningún trabajo activo iniciado por el usuario que conlleve operaciones de planificación de administración: como el acceso al plan de administración está restringido durante la actualización, se deben completar todas las acciones de planificación de la administración y, después, activar la actualización. Esto no incluye la replicación en curso.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

- ¿Esta actualización afecta a la replicación en curso?

  Nº La replicación en curso continúa sin interrupciones durante la actualización y después de ella.

- ¿Qué ocurre con la configuración de red, como la VPN de sitio a sitio, la configuración de la dirección IP, etc.?

  La actualización no afecta a la configuración de red, por lo que todas las conexiones locales permanecerán intactas.
- ¿Qué ocurre con los almacenes si se prevé realizar una actualización próximamente?

  Para la compatibilidad con el almacén de Site Recovery, la versión anterior de Azure Portal estará en desuso a partir de septiembre. Por tanto, se recomienda encarecidamente a los clientes que usen la característica de actualización para migrar al nuevo portal.

- ¿Qué supone este plan de migración para las herramientas existentes?  

  La actualización de la herramientas para el modelo de implementación de Resource Manager en el que se basan los almacenes de Recovery Services es uno de los cambios más importantes que es necesario tener en cuenta en los planes de actualización.

- ¿Cuánto tiempo durará el tiempo de inactividad del plano de administración?

  La actualización dura entre quince y treinta minutos. Puede tardar hasta una hora como máximo.

- ¿Puedo revertir la actualización?

  Nº No se admite la reversión una vez actualizados correctamente los recursos.

- ¿Puedo validar mi suscripción o mis recursos para ver si son aptos para la actualización?

  Sí. En la opción de actualización compatible con la plataforma, el primer paso para la actualización consiste en validar si los recursos pueden actualizarse. En caso de error en la validación de los requisitos previos, se recibirán las advertencias o errores apropiados.

- ¿Cómo informo de un problema con la actualización?

  En caso de que se produzcan errores durante la actualización, tenga en cuenta el identificador OperationId indicado en el error. Soporte técnico de Microsoft funcionará proactivamente para resolver el problema. También puede ponerse en contacto con el equipo de soporte técnico y proporcionarle el identificador de la suscripción, el nombre del almacén y el identificador de la operación. Trabajaremos para resolver el problema lo antes posible. No vuelva a intentar la operación a menos que Microsoft se lo indique explícitamente.

## <a name="how-to-run-the-script"></a>¿Cómo se ejecuta el script?

Ejecute el comando siguiente en el símbolo del sistema de PowerShell:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionId: identificador de la suscripción asociado con el almacén que se va a actualizar.
- VaultName: nombre del almacén que se va a actualizar.
- Location: ubicación del almacén que se va a actualizar.
- ResourceType: valor HyperVRecoveryManagerVault para los almacenes de Site Recovery.
- TargetResourceGroupName: grupo de recursos en el que se desea colocar el almacén actualizado. El valor TargetResourceGroupName puede ser un grupo ResourceGroup de Azure Resource Manager u otro nuevo. En caso de que el valor de TargetResourceGroupName proporcionado no exista, se crea como parte de la actualización en la misma ubicación que el almacén. Para leer más sobre los grupos de recursos, haga clic [aquí](../azure-resource-manager/resource-group-overview.md#resource-groups):

>[!NOTE]
>Los nombres de grupos de recursos tienen restricciones. Se deben cumplir, ya que, de lo contrario, se pueden ocasionar errores en la actualización del almacén.

Ejemplo:

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


Como alternativa, puede ejecutar el script siguiente, y se le pedirá que proporcione entradas para todos los parámetros necesarios.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  El script de PowerShell le pedirá que escriba sus credenciales. Escriba las credenciales dos veces: una para la cuenta de ASM y otra para la cuenta de Azure Resource Manager.

2.  Después de especificar las credenciales, el script ejecuta una comprobación de requisitos previos para determinar que la instalación de la infraestructura cumple los requisitos previos mencionados anteriormente en este documento.

3.  Una vez comprobados los requisitos previos, se le solicita que confirme si desea continuar con la actualización del almacén. Tras confirmar, se inicia el proceso de actualización del almacén. La actualización completa puede durar entre quince y treinta minutos.

4.  Cuando la actualización finalice correctamente, puede acceder al almacén actualizado en la nueva versión de Azure Portal.

## <a name="management-experience-post-upgrade"></a>Experiencia de administración después de la actualización

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>Replicación del almacén de Recovery Services mediante Azure Site Recovery

- Ahora puede proteger las máquinas virtuales de Azure de una región a otra. Para obtener más información, consulte la documentación [aquí](site-recovery-azure-to-azure.md).

- Para obtener más información sobre la replicación de máquinas virtuales VMware en Azure, consulte la documentación [aquí](vmware-walkthrough-overview.md).

- Para obtener más información sobre la replicación de máquinas virtuales Hyper-V (sin VMM) en Azure, consulte la documentación [aquí](hyper-v-site-walkthrough-overview.md).

- Para obtener más información sobre la replicación de máquinas virtuales Hyper-V (con VMM) en Azure, consulte la documentación [aquí](vmm-to-azure-walkthrough-overview.md).

- Para obtener más información sobre la replicación de máquinas virtuales Hyper-V (con VMM) en un sitio secundario, consulte la documentación [aquí](site-recovery-vmm-to-vmm.md).

- Para obtener más información sobre la replicación de máquinas virtuales VMware en un sitio secundario, consulte la documentación [aquí](site-recovery-vmware-to-vmware.md).

### <a name="how-to-view-your-replicated-items"></a>Visualización de los elementos replicados

A continuación se muestra una pantalla en la que se muestra la página del panel de información del almacén de Recovery Services que muestra entidades clave del almacén. Haga clic en **Site Recovery** -> **Elementos replicados** para ver la lista de entidades protegidas del almacén.


![Elementos replicados](./media/upgrade-site-recovery-vaults/replicateditems.png)

En la pantalla siguiente se muestra el flujo de trabajo para ver los elementos replicados y cómo iniciar una conmutación por error.

![Elementos replicados](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>Visualización de la configuración de replicación

En el almacén de Site Recovery, cada grupo de protección tiene definida la configuración de replicación (frecuencia de copia, retención de punto de recuperación, frecuencia de las instantáneas coherentes de la aplicación, etc.). En el almacén de Recovery Services, estas opciones se configuran como una directiva de replicación. El nombre de la directiva es el nombre del grupo de protección o "primarycloud_Policy".

Para obtener más información sobre la directiva de replicación, consulte [aquí](site-recovery-setup-replication-settings-vmware.md).

