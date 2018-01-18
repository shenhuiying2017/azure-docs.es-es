---
title: "Actualización de un almacén de Backup a un almacén de Recovery Services | Microsoft Docs"
description: "Instrucciones e información de compatibilidad para actualizar el almacén de Azure Backup a un almacén de Recovery Services."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/4/2018
ms.author: sogup;markgal;arunak
ms.openlocfilehash: 8396a7276fde10eb95a22ed07fa61625acfdd77f
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Actualización de un almacén de Backup a un almacén de Recovery Services

En este artículo se explica cómo actualizar un almacén de Backup a un almacén de Recovery Services. El proceso de actualización no afecta a los trabajos de copia de seguridad que se están ejecutando y no se perderá ningún dato de la copia de seguridad. Las principales razones para actualizar un almacén de Backup a un almacén de Recovery Services:
- Todas las características de un almacén de Backup se conservan en un almacén de Recovery Services.
- Los almacenes de Recovery Services tienen más características que los de Backup, incluida una mejor seguridad, supervisión integrada, restauraciones más rápidas y restauraciones de nivel de elemento.
- Administran elementos de copia de seguridad desde un portal simplificado y mejorado.
- Las características nuevas solo se aplican a los almacenes de Recovery Services.

## <a name="impact-to-operations-during-upgrade"></a>Impacto en las operaciones durante la actualización

Cuando se actualiza un almacén de Backup a un almacén de Recovery Services, no hay ningún impacto en las operaciones del plan de datos. Todos los trabajos de copia de seguridad continúan con normalidad, y los trabajos de restauración activos continúan sin interrupción. Durante la actualización, las operaciones de administración sufren un corto período de inactividad, y no pueden proteger los elementos nuevos o crear trabajos de copias de seguridad de ad-hoc. Los trabajos de restauración para las máquinas virtuales IaaS no se ejecutan durante la actualización. La actualización del almacén tarda menos de una hora en completarse. Una vez finalizado, un almacén de Recovery Services reemplaza al almacén de Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Cambios en la automatización y la herramienta después de actualizar

Al preparar la infraestructura de la actualización del almacén, debe actualizar la automatización existente o las herramientas para asegurarse de que sigue funcionando después de la actualización.
Consulte las guías de referencia de los cmdlets de PowerShell para más información sobre el [modelo de implementación de Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Antes de la actualización

Compruebe lo siguiente antes de actualizar los almacenes de Backup a almacenes de Recovery Service.

- **Versión del agente mínima**: para actualizar el almacén, asegúrese de que el agente de Microsoft Azure Recovery Services (MARS) es al menos la versión 2.0.9083.0. Si el agente de MARS es anterior a 2.0.9083.0, actualícelo antes de iniciar el proceso de actualización.
- **Modelo de facturación basado en instancias**: los almacenes de Recovery Service solo admiten el modelo de facturación basado en instancias. Si tiene un almacén de Backup que utiliza el anterior modelo de facturación basado en almacenamiento, convierta el modelo durante la actualización.
- **Ninguna operación de configuración de copia de seguridad en curso**: durante la actualización, se restringe el acceso al plan de administración. Complete todas las acciones del plan de administración e inicie la actualización.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Uso de scripts de PowerShell para actualizar los almacenes

Puede utilizar scripts de PowerShell para actualizar los almacenes de Backup a Recovery Services. Compruebe que tiene los componentes necesarios de PowerShell para desencadenar la actualización del almacén. Los scripts de PowerShell para almacenes de Backup no funcionan en los almacenes de Recovery Services. Preparación del entorno para actualizar los almacenes:

1. Instale o actualice [Windows Management Framework (WMF) a la versión 5](https://www.microsoft.com/download/details.aspx?id=50395) o superior.
2. [Instale el MSI de Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Descargue el [script de PowerShell](https://aka.ms/vaultupgradescript2) para actualizar los almacenes.

### <a name="run-the-powershell-script"></a>Ejecución del script de PowerShell

Use el siguiente script para actualizar los almacenes. El siguiente script de ejemplo ofrece explicaciones de los parámetros.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID**: Número de identificación de la suscripción del almacén de credenciales que se está actualizando.<br/>
**VaultName**: Nombre del almacén de Backup que se está actualizando.<br/>
**Location**: Ubicación del almacén que se va a actualizar.<br/>
**ResourceType**: Usar BackupVault.<br/>
**TargetResourceGroupName**: Puesto que va a actualizar el almacén a una implementación basada en Resource Manager, especifique un grupo de recursos. Puede usar un grupo de recursos existente o crear uno proporcionando un nuevo nombre. Si se equivoca al escribir el nombre de un grupo de recursos, puede crear uno nuevo. Para más información sobre los grupos de recursos, lea la [introducción a Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Los nombres de grupos de recursos tienen restricciones. Asegúrese de seguir las instrucciones; si no lo hace, la actualización podría generar un error.
>
>Los clientes de **Azure Gobierno de EE. UU.** deben establecer el entorno en "AzureUSGovernment" para la ejecución del script.
>Los clientes de **Azure China** deben establecer este entorno en "AzureChinaCloud" mientras se ejecuta el script.

El siguiente fragmento de código es un ejemplo de cómo debería ser el comando de PowerShell:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

También puede ejecutar la secuencia de comandos sin ningún parámetro, y se le pedirá que proporcione valores para todos los parámetros necesarios.

El script de PowerShell le pide que escriba sus credenciales. Escriba sus credenciales dos veces: una vez para la cuenta de Service Manager y una segunda vez para la cuenta de Resource Manager.

### <a name="pre-requisites-checking"></a>Comprobación de los requisitos previos
Una vez que haya escrito sus credenciales de Azure, Azure comprobará que el entorno cumpla los requisitos previos siguientes:

- **Versión mínima del agente**: la actualización de los almacenes de Backup a almacenes de Recovery Services requiere que la versión del agente de MARS sea como mínimo la 2.0.9083.0. Si tiene elementos registrados en un almacén de Backup con un agente anterior a la versión 2.0.9083.0, se producirá un error en la comprobación de los requisitos previos. En ese caso, actualice al agente y vuelva a intentar la actualización. Puede descargar la versión más reciente del agente en [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Trabajos de configuración en curso**: si alguien está configurando un trabajo para un almacén de Backup que se va a actualizar, o registrando un elemento, la comprobación de requisitos previos generará un error. Complete la configuración o termine de registrar el elemento y, a continuación, inicie el proceso de actualización del almacén.
- **Modelo de facturación basado en almacenamiento**: los almacenes de Recovery Services admiten el modelo de facturación basado en instancias. Si ejecuta la actualización de un almacén de Backup que usa el modelo de facturación basado en almacenamiento, se le pedirá que actualice el modelo junto con el almacén. Si lo prefiere, puede actualizar primero el modelo de facturación y, después, actualizar el almacén.
- Identifique un grupo de recursos para el almacén de Recovery Services. Para aprovechar las características de implementación de Resource Manager, debe colocar un almacén de Recovery Services en un grupo de recursos. Si no sabe qué grupo de recursos usar, proporcione un nombre y el proceso de actualización creará el grupo de recursos automáticamente. El proceso de actualización también asocia el almacén al nuevo grupo de recursos.

Una vez que el proceso de actualización finalice la comprobación de los requisitos previos, el proceso le pedirá que inicie la actualización del almacén. Después de confirmarlo, el proceso de actualización suele tarda unos 15-20 minutos en completarse, según el tamaño de su almacén. Si tiene un almacén de gran tamaño, la actualización puede tardar hasta 90 minutos.

## <a name="managing-your-recovery-services-vaults"></a>Administración de almacenes de Recovery Services

En las pantallas siguientes se muestra un nuevo almacén de Recovery Services actualizado desde el almacén de Backup, en Azure Portal. En la primera pantalla se muestra el panel del almacén con las principales entidades para el almacén.

![ejemplo de almacén de Recovery Services actualizado desde un almacén de Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

En la segunda pantalla se muestran los vínculos de ayuda para empezar a usar el almacén de Recovery Services.

![vínculos de ayuda en la hoja Inicio rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Pasos posteriores a la actualización
El almacén de Recovery Services admite la especificación de información de zona horaria en la directiva de copia de seguridad. Después de que el almacén se actualice correctamente, vaya a las directivas de Backup desde el menú de configuración del almacén y actualice la información de zona horaria para cada una de las directivas configuradas en dicho almacén. En esta pantalla ya se muestra la hora de programación de copia de seguridad especificada según la zona horaria local utilizada cuando se creó la directiva. 

## <a name="enhanced-security"></a>Mayor seguridad

Cuando un almacén de Backup se actualiza a un almacén de Recovery Services, la configuración de seguridad para ese almacén se activa automáticamente. Cuando la configuración de seguridad está activada, determinadas operaciones, como la eliminación de copias de seguridad o el cambio de una frase de contraseña, requieren un PIN de [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Para más información sobre la seguridad mejorada, vea el artículo [Características de seguridad para proteger copias de seguridad híbridas mediante Azure Backup](backup-azure-security-feature.md). 

Cuando la seguridad mejorada está activada, los datos se conservan hasta catorce días después de eliminar la información del punto de recuperación del almacén. Se factura a los clientes por el almacenamiento de estos datos de seguridad. La retención de datos de seguridad se aplica a los puntos de recuperación obtenidos para el agente de Azure Backup, Azure Backup Server y System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Recopilación de datos en el almacén

Cuando haya actualizado a un almacén de Recovery Services, configure los informes para Azure Backup (para VM de IaaS y Microsoft Azure Recovery Services (MARS)) y use Power BI para acceder a dichos informes. Para más información sobre la recopilación de datos, vea el artículo [Configuración de informes de Azure Backup](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Afecta el plan de actualización a mis copias de seguridad en curso?**</br>
Nº Las copias de seguridad en curso continúan sin interrupción durante y después de la actualización.

**¿Qué ocurre con mis almacenes si no tengo previsto actualizar pronto?**</br>
Puesto que todas las características nuevas solo se aplican a almacenes de Recovery Services, le instamos a que actualice los almacenes. A partir del 1 de septiembre de 2017, Microsoft comenzará a actualizar automáticamente los almacenes de copia de seguridad para almacenes de Recovery Services. Después del 30 de noviembre de 2017, ya no podrá crear almacenes de Backup con PowerShell. El almacén se puede actualizar automáticamente en cualquier momento hasta esa fecha. Microsoft le recomienda que actualice el almacén tan pronto como sea posible.

**¿Qué supone esta actualización para las herramientas existentes?**</br>
Actualice las herramientas al modelo de implementación de Resource Manager. Los almacenes de Recovery Services se crearon para utilizarse en el modelo de implementación de Resource Manager. La planeación para el modelo de implementación de Resource Manager y la observación de la diferencia en los almacenes son importantes. 

**Durante la actualización, ¿hay mucho tiempo de inactividad?**</br>
Depende del número de recursos que se vayan a actualizar. Para implementaciones pequeñas (de unas decenas de instancias protegidas), toda la actualización debería tardar menos de 20 minutos. Para implementaciones grandes, debe tardar una hora como máximo.

**¿Puedo revertir la actualización?**</br>
Nº No se admite la reversión una vez actualizados correctamente los recursos.

**¿Puedo validar mi suscripción o mis recursos para ver si son aptos para la actualización?**</br>
Sí. El primer paso de la actualización comprueba si los recursos son aptos para la actualización. En caso de que se produzca un error en la validación, recibirá mensajes para todas las razones que no se pudo completar la actualización.

**¿Puedo actualizar mi almacén de Backup basado en CSP?**</br>
Nº Actualmente, no se pueden actualizar los almacenes de Backup basados en CSP. En las próximas versiones, se agregará compatibilidad para actualizar los almacenes de Backup basados en CSP.

**¿Puedo ver mi almacén clásico después de la actualización?**</br>
Nº No se puede ver o administrar su almacén clásico después de la actualización. Solo podrá usar el nuevo Azure Portal para todas las acciones de administración en el almacén.

**Hubo un error de actualización, pero la máquina que mantenía el agente que requiere actualización ya no existe. ¿Qué hago en este caso?**</br>
Si tiene que usar el almacén, las copias de seguridad de esta máquina para una retención a largo plazo, no podrá actualizar el almacén. En versiones futuras se agregará compatibilidad para actualizar tal almacén.
Si ya no necesita almacenar las copias de seguridad de esta máquina, anule el registro de esta máquina del almacén y vuelva a intentar la actualización.

**¿Por qué no puedo ver la información de los trabajos para mis recursos después de la actualización?**</br>
La supervisión de copias de seguridad (agente de MARS e IaaS) es una característica nueva que obtendrá al actualizar el almacén de Backup al almacén de Recovery Services. La información de supervisión tarda hasta 12 horas en sincronizarse con el servicio.

**¿Cómo se informa de un problema?**</br>
Si se produce un error en cualquier parte de la actualización del almacén, tenga en cuenta el identificador de la operación que figura en el error. Soporte técnico de Microsoft trabajará proactivamente para resolver el problema. Puede conectar con el soporte técnico o enviarnos un correo electrónico a rsvaultupgrade@service.microsoft.com con su identificador de suscripción, el nombre del almacén y el identificador de la operación. Intentaremos resolver el problema lo antes posible. No vuelva a intentar la operación a menos que Microsoft se lo indique explícitamente.


## <a name="next-steps"></a>pasos siguientes
Utilice el siguiente artículo para:</br>
[Copia de seguridad de una máquina virtual de IaaS](backup-azure-arm-vms-prepare.md)</br>
[Copia de seguridad de Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Copia de seguridad de Windows Server](backup-configure-vault.md).
