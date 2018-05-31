---
title: Actualización automática de Mobility Service de Azure en la recuperación ante desastres de Azure | Microsoft Docs
description: Proporciona una descripción general de la actualización automática de Mobility Service, que se utiliza para replicar máquinas virtuales de Azure mediante Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: rajanaki
ms.openlocfilehash: 45f2e2927f699769bb385038c04d4dd23e075a9a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779749"
---
# <a name="automatic-update-of-mobility-service-extension-in-azure-to-azure-replication"></a>Actualización automática de la extensión de Mobility Service en Azure para la replicación de Azure

Azure Site Recovery tiene una ritmo de publicación mensual en la que se agregan mejoras a las características existentes o se agregan nuevas características, y se reparan los problemas conocidos, si los hubiera. Esto significa que, para mantener el servicio al día, debe implementar estos parches de forma mensual. Para evitar verse desbordados con las actualizaciones, los usuarios pueden optar por permitir que Site Recovery administre las actualizaciones de los componentes. Tal como se detalla en la [referencia de la arquitectura ](azure-to-azure-architecture.md) de Azure para la recuperación ante desastres de Azure, Mobility Service se instala en todas las máquinas virtuales de Azure que tengan habilitada la replicación, a la vez que replica las máquinas virtuales de una región de Azure a otra. En este documento se detalla lo siguiente:

- ¿Cómo funcionan las actualizaciones automáticas?
- Habilitar las actualizaciones automáticas
- Problemas habituales y soluciones
 
## <a name="how-does-automatic-update-work"></a>Cómo funcionan las actualizaciones automáticas

Una vez que permite que Site Recovery administre las actualizaciones, se implementa un runbook global (que usan los servicios de Azure) a través de una cuenta de Automation que se crea en la misma suscripción que el almacén. Se utiliza una cuenta de Automation para un almacén específico. El runbook comprueba cada máquina virtual en un almacén que tenga las actualizaciones automáticas activadas, e inicia una actualización de la extensión de Mobility Service si hay una versión más reciente disponible. La programación predeterminada del runbook se repite diariamente a las 12:00 h según la zona horaria de la geoárea de la máquina virtual replicada. El calendario del runbook también se puede modificar a través de la cuenta de Automation del usuario, si es necesario. 

## <a name="enable-automatic-updates"></a>Habilitar las actualizaciones automáticas

Puede optar por permitir que Site Recovery administre las actualizaciones de las siguientes maneras:

- [Como parte del paso para habilitar la replicación.](#as-part-of-the-enable-replication-step)
- [Alternar la configuración de actualización de la extensión dentro del almacén.](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Como parte del paso para habilitar la replicación:

Cuando habilite la replicación de una máquina virtual, ya sea comenzando [desde la vista de máquina virtual](azure-to-azure-quickstart.md) o [desde el almacén de Recovery Services](azure-to-azure-how-to-enable-replication.md), tendrá la opción de permitir que Site Recovery administre las actualizaciones para la extensión de Site Recovery o de administrar manualmente las mismas.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Alternar la configuración de actualización de la extensión dentro del almacén

1. En el almacén, vaya a **Administrar**-> **Infraestructura de Site Recovery**.
2. En **Máquinas virtuales de Azure** -> **Configuración de actualización de la extensión**, haga clic en el botón de alternancia para elegir si quiere permitir que *ASR administre las actualizaciones* o *administrarlas manualmente*. Haga clic en **Save**(Guardar).

![vault-toggle-autuo-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Si elige la opción para *permitir que ASR administre* las actualizaciones, la configuración se aplica a todas las máquinas virtuales del almacén correspondiente.


> [!Note] 
> Ambas opciones le indicarán la cuenta de Automation que se usará para administrar las actualizaciones. Si va a habilitar esta característica por primera vez en un almacén, se creará una nueva cuenta de Automation. Todas las replicaciones habilitadas en el mismo almacén usarán la que se creó anteriormente.

## <a name="common-issues--troubleshooting"></a>Problemas habituales y soluciones

Si hay un problema con las actualizaciones automáticas, se le enviará una notificación en la sección "Problemas de configuración" del panel del almacén. 

En caso de que haya intentado habilitar las actualizaciones automáticas y no le haya sido posible, consulte a continuación la solución de problemas.

**Error**: no tiene permisos para crear una cuenta de ejecución de Azure (entidad de servicio) y conceder el rol Colaborador a la entidad de servicio. 
- Acción recomendada: asegúrese de que la cuenta en la que ha iniciado sesión está asignada al rol "Colaborador" y vuelva a intentar la operación.
 
Una vez que las actualizaciones automáticas están activadas, el servicio Site Recovery puede solucionar la mayoría de problemas; para ello, debe hacer clic en el botón "**Reparar**".

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

En caso de que el botón de reparación no esté disponible, consulte el mensaje de error que se muestra en el panel de configuración de la extensión.

 - **Error**: la cuenta de ejecución no tiene permiso para obtener acceso al recurso de Recovery Services.

    **Acción recomendada**: elimine y [vuelva a crear la cuenta de ejecución](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) o asegúrese de que la aplicación Azure Active Directory de la cuenta de ejecución de Automation obtenga acceso al recurso de Recovery Services.

- **Error**: no se encuentra la cuenta de ejecución. Es posible que la huella digital del certificado y la conexión no sea idéntica, o que alguno de los elementos siguientes se haya eliminado o no se haya creado: la aplicación Azure Active Directory, la entidad de servicio, el rol, el recurso de certificado de Automation o el recurso de conexión de Automation. 

    **Acción recomendada**: elimine y [vuelva a crear la cuenta de ejecución](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
