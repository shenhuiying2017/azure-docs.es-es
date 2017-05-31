---
title: "Actualización de un almacén de Backup a un almacén de Recovery Services (versión preliminar) | Microsoft Docs"
description: "Comandos e información complementaria para actualizar un almacén de Azure Backup a un almacén de Recovery Services."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/10/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 00744d70a7644d80cfd99acbcf96966574f4512e
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Actualización de un almacén de Backup a un almacén de Recovery Services

En este artículo se explica cómo actualizar un almacén de Backup a un almacén de Recovery Services. El proceso de actualización no afecta a los trabajos de copia de seguridad que se están ejecutando y no se perderá ningún dato de la copia de seguridad. Las principales razones para actualizar un almacén de Backup a un almacén de Recovery Services:
- Todas las características de un almacén de Backup se conservan en un almacén de Recovery Services.
- Los almacenes de Recovery Services tienen más características que los de Backup, incluida una mejor seguridad, supervisión integrada, restauraciones más rápidas y restauraciones de nivel de elemento, y administran elementos de copia de seguridad desde un portal simplificado y mejorado.
-  Solo se publican nuevas características para los almacenes de Recovery Services.

## <a name="impact-to-operations-during-upgrade"></a>Impacto en las operaciones durante la actualización

Cuando se actualiza un almacén de Backup a un almacén de Recovery Services, no hay ningún impacto en las operaciones del plan de datos. Todos los trabajos de copia de seguridad continúan con normalidad, y los trabajos de restauración activos continúan sin interrupción. Durante la actualización, las operaciones de administración sufren un corto período de inactividad, y no pueden proteger los elementos nuevos o crear trabajos de copias de seguridad de ad-hoc. Los trabajos de restauración para las máquinas virtuales IaaS no se ejecutan durante la actualización. <MG>: ¿Cuál es la diferencia entre los trabajos de restauración activos que dejarán de tener el estado normal y los trabajos de restauración de la máquina virtual IaaS?
La actualización del almacén tarda menos de una hora en completarse. Una vez finalizado, un almacén de Recovery Services reemplaza al almacén de Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Cambios en la automatización y la herramienta después de actualizar

Al preparar la infraestructura de la actualización del almacén, debe actualizar la automatización existente o las herramientas para asegurarse de que sigue funcionando después de la actualización.
Consulte las guías de referencia de los cmdlets de PowerShell para más información sobre el [modelo de implementación de Service Manager](backup-client-automation-classic.md) y el [modelo de implementación de Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Antes de la actualización

Compruebe lo siguiente antes de actualizar los almacenes de Backup a almacenes de Recovery Service.

- **Versión de agente mínimo**: para actualizar el almacén, asegúrese de que el agente de Microsoft Azure Recovery Services (MARS) es al menos la versión 2.0.9070.0. Si el agente de MARS es anterior a 2.0.9070.0, actualícelo antes de iniciar el proceso de actualización.
- **Modelo de facturación basado en instancias**: los almacenes de Recovery Service solo admiten el modelo de facturación basado en instancias. Si tiene un almacén de Backup que utiliza el anterior modelo de facturación basado en almacenamiento, convierta el modelo durante la actualización.
- **Ninguna operación de configuración de copia de seguridad en curso**: durante la actualización, se restringe el acceso al plan de administración. Complete todas las acciones del plan de administración e inicie la actualización.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Uso de scripts de PowerShell para actualizar los almacenes

Puede utilizar scripts de PowerShell para actualizar los almacenes de Backup a Recovery Services. Compruebe que tiene los componentes necesarios de PowerShell para desencadenar la actualización del almacén. Los scripts de PowerShell para almacenes de Backup no funcionan en los almacenes de Recovery Services. Preparación del entorno para actualizar los almacenes:

1. Instale o actualice [Windows Management Framework (WMF) a la versión 5](https://www.microsoft.com/download/details.aspx?id=50395) o superior.
2. Descargue e [instale Azure PowerShell 3.7.0](https://github.com/Azure/azure-powershell/releases/download/v3.7.0-March2017/azure-powershell.3.7.0.msi).
3. Actualización de los módulos de PowerShell:
  - Para Windows Server:
    - Install-Module -Name Azure -RequiredVersion 3.8.0
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0
  - Para Windows 10
    - Install-Module -Name Azure -RequiredVersion 3.8.0 -AllowClobber
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0 -AllowClobber
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0 -AllowClobber
4. Descargue el [script de PowerShell](http://download.microsoft.com/download/1/c/6/1c6ed72e-38f9-4675-9cf9-9b8a06da7cd3/recoveryservicesvaultupgrade.ps1) para actualizar los almacenes.

### <a name="run-the-powershell-script"></a>Ejecución del script de PowerShell

Use el siguiente script para actualizar los almacenes. El siguiente script de ejemplo ofrece explicaciones de los parámetros.

RecoveryServicesVaultUpgrade.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`


SubscriptionID: el número de Id. de suscripción del almacén de credenciales que se está actualizando.
VaultName: el nombre del almacén de Backup que se está actualizando.
Location: ubicación del almacén que se va a actualizar.
ResourceType: use BackupVault.
TargetResourceGroupName: puesto que va a actualizar el almacén a una implementación basada en Resource Manager, especifique un grupo de recursos. Puede usar un grupo de recursos existente o crear uno proporcionando un nuevo nombre. Si se equivoca al escribir el nombre de un grupo de recursos, puede crear uno nuevo. Para más información sobre los grupos de recursos, lea la [introducción a Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Los nombres de grupos de recursos tienen restricciones. Asegúrese de seguir las instrucciones; si no lo hace, la actualización podría generar un error.
>
>

El siguiente fragmento de código es un ejemplo de cómo debería ser el comando de PowerShell:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

También puede ejecutar la secuencia de comandos sin ningún parámetro, y se le pedirá que proporcione valores para todos los parámetros necesarios.

El script de PowerShell le pide que escriba sus credenciales. Escriba sus credenciales dos veces: una vez para la cuenta de Service Manager y una segunda vez para la cuenta de Resource Manager.

### <a name="pre-requisites-checking"></a>Comprobación de los requisitos previos
Una vez que haya escrito sus credenciales de Azure, Azure comprobará que el entorno cumpla los requisitos previos siguientes:

- **Versión mínima del agente**: la actualización de los almacenes de Backup a almacenes de Recovery Services requiere que la versión del agente de MARS sea como mínimo la 2.0.9070. Si tiene elementos registrados en un almacén de Backup con un agente anterior a la versión 2.0.9070, se producirá un error en la comprobación de los requisitos previos. En ese caso, actualice al agente y vuelva a intentar la actualización. Puede descargar la versión más reciente del agente en [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Trabajos de configuración en curso**: si alguien está configurando un trabajo para un almacén de Backup que se va a actualizar, o registrando un elemento, la comprobación de requisitos previos generará un error. Complete la configuración o termine de registrar el elemento y, a continuación, inicie el proceso de actualización del almacén.
- **Modelo de facturación basado en almacenamiento**: los almacenes de Recovery Services admiten el modelo de facturación basado en instancias. Si ejecuta la actualización de un almacén de Backup que usa el modelo de facturación basado en almacenamiento, se le pedirá que actualice el modelo junto con el almacén. Si lo prefiere, puede actualizar primero el modelo de facturación y, después, actualizar el almacén.
- Identifique un grupo de recursos para el almacén de Recovery Services. Para aprovechar las características de implementación de Resource Manager, debe colocar un almacén de Recovery Services en un grupo de recursos. Si no sabe qué grupo de recursos usar, proporcione un nombre y el proceso de actualización creará el grupo de recursos automáticamente. El proceso de actualización también asocia el almacén al nuevo grupo de recursos.

Una vez que el proceso de actualización finalice la comprobación de los requisitos previos, el proceso le pedirá que inicie la actualización del almacén. Después de confirmarlo, el proceso de actualización suele tarda unos 15-20 minutos en completarse, según el tamaño de su almacén. Si tiene un almacén de gran tamaño, la actualización puede tardar hasta 90 minutos.

## <a name="managing-your-recovery-services-vaults"></a>Administración de almacenes de Recovery Services

En las pantallas siguientes se muestra un nuevo almacén de Recovery Services actualizado desde el almacén de Backup, en Azure Portal. En la primera pantalla se muestra el panel del almacén con las principales entidades para el almacén.

![ejemplo de almacén de Recovery Services actualizado desde un almacén de Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

En la segunda pantalla se muestran los vínculos de ayuda para empezar a usar el almacén de Recovery Services.

![vínculos de ayuda en la hoja Inicio rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Afecta el plan de actualización a mis copias de seguridad en curso?**</br>
No. Las copias de seguridad en curso continúan sin interrupción durante y después de la actualización.

**¿Qué ocurre con mi almacenes si no tengo previsto actualizar pronto?**</br>
Dado que todas las características nuevas están planificadas para el almacén de Recovery Services y uso del portal clásico dejará de utilizarse, Microsoft dejará de usar los almacenes de Backup y el modelo de Service Manager. En el futuro, Microsoft desencadenará una actualización automática de todos los almacenes al almacén de Recovery Services. Hasta entonces, los clientes controlan cuándo desean actualizar sus almacenes.

**¿Qué supone este plan de actualización para las herramientas existentes?**</br>  
La actualización de la herramientas para el modelo de implementación de Resource Manager en el que se basan los almacenes de Recovery Services es uno de los cambios más importantes que es necesario tener en cuenta en los planes de actualización.

**¿Cuánto durará el tiempo de inactividad?**</br>
Depende del número de recursos que se vayan a actualizar. Para implementaciones pequeñas (de unas decenas de instancias protegidas), toda la actualización debería tardar menos de 20 minutos. Para implementaciones grandes, debe tardar una hora como máximo.

**¿Puedo revertir la actualización?**</br>
No. No se admite la reversión una vez actualizados correctamente los recursos.

**¿Puedo validar mi suscripción o mis recursos para ver si son aptos para la actualización?**</br>
Sí. El primer paso de la actualización comprueba si los recursos son aptos para la actualización. En caso de que se produzca un error en la validación, recibirá mensajes para todas las razones que no se pudo completar la actualización.

**¿Qué permisos debo tener que desencadenar la actualización del almacén?**</br>
Para realizar la actualización, debe ser agregado como coadministrador de la suscripción en el Portal de Azure clásico. Esto es necesario incluso aunque ya esté agregado como propietario en Azure Portal. Trate de agregar un coadministrador de la suscripción en el Portal de Azure clásico para averiguar si es coadministrador de la suscripción. Si no puede agregar un coadministrador, póngase en contacto con un administrador del servicio o coadministrador de la suscripción para que le agreguen.

**¿Puedo actualizar mi almacén de Backup basado en CSP?**</br>
No. Actualmente, no se pueden actualizar los almacenes de Backup basados en CSP. Iremos agregando compatibilidad para ello en las próximas ediciones.

**¿Puedo ver mi almacén clásico después de la actualización?**</br>
No. No se puede ver o administrar su almacén clásico después de la actualización. Solo podrá usar el nuevo Azure Portal para todas las acciones de administración en el almacén.

**Mi actualización ha generado un error que indica que necesito actualizar el agente en un equipo que ya no existe. ¿Qué hago en este caso?**</br>
Si tiene que usar el almacén de las copias de seguridad de este equipo para una retención a largo plazo, no podrá actualizar el almacén. Iremos agregando compatibilidad para habilitar la actualización de dicho almacén en futuras versiones.
Si ya no necesita almacenar las copias de seguridad de esta máquina, anule el registro de esta máquina del almacén y vuelva a intentar la actualización.

**¿Por qué no puedo ver la información de los trabajos para mis recursos locales después de la actualización**</br>
La supervisión de copias de seguridad locales (agente de MARS, DPM y MABS) es una característica nueva que obtendrá al actualizar el almacén de Backup al almacén de Recovery Services. La información de supervisión tarda hasta 12 horas para permanecer sincronizado con el servicio.

**¿Cómo se informa de un problema?**</br>
En caso de que se produzcan errores durante la actualización, tenga en cuenta el identificador OperationId indicado en el error. Soporte técnico de Microsoft funcionará proactivamente para resolver el problema. Puede conectar con el soporte técnico o enviarnos un correo electrónico a rsvaultupgrade@service.microsoft.com con su Id. de suscripción, el nombre del almacén y el identificador de la operación. Trabajaremos para resolver el problema lo antes posible. No vuelva a intentar la operación a menos que Microsoft se lo indique explícitamente.


## <a name="next-steps"></a>Pasos siguientes
Utilice el siguiente artículo para:</br>
[Copia de seguridad de una máquina virtual de IaaS](backup-azure-arm-vms-prepare.md)</br>
[Copia de seguridad de Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Hacer una copia de seguridad de Windows Server](backup-configure-vault.md)

