<properties
	pageTitle="Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure | Microsoft Azure"
	description="Este artículo le guía por los pasos de migración de recursos compatible con la plataforma desde el modelo de implementación clásica hasta Azure Resource Manager mediante la CLI de Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure

En estos pasos se describe cómo utilizar los comandos de la interfaz de la línea de comandos (CLI) de Azure para migrar recursos de infraestructura como servicio (IaaS) del modelo de implementación clásica al modelo de implementación de Azure Resource Manager. El artículo requiere el inicio de sesión en la [CLI de Azure](../xplat-cli-install.md) (`azure login`).

## Paso 1: Preparación para la migración

Estos son algunos de los procedimientos recomendados a la hora de evaluar la migración de recursos de IaaS desde el modelo clásico a Resource Manager:

- Consulte la [lista de configuraciones o características no admitidas](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones o características no admitidas, se recomienda esperar a que se anuncie dicha compatibilidad. Como alternativa, puede quitar esa característica o salir de esa configuración para habilitar la migración si se ajusta a sus necesidades.
-	Si tiene actualmente scripts automatizados que implementan la infraestructura y las aplicaciones, intente crear una configuración de prueba similar usando esos scripts para la migración. También puede configurar entornos de ejemplo mediante el Portal de Azure.

## Paso 2: Establecimiento de la suscripción y registro para la migración

Para los escenarios de migración, debe configurar el entorno para el modelo clásico y el de Resource Manager. [Instale la CLI de Azure](../xplat-cli-install.md) y [seleccione su suscripción](../xplat-cli-connect.md).

Seleccione la suscripción de Azure con el siguiente comando:

	azure account set "azure-subscription-name"

>[AZURE.NOTE] El registro es un paso que solo se realiza una vez pero que es necesario antes de intentar la migración. Si no se registra, verá el siguiente mensaje de error:

>	*BadRequest : Subscription is not registered for migration.* 

Regístrese con el proveedor de recursos de migración mediante el comando siguiente. Tenga en cuenta que, en algunos casos, se agota el tiempo de espera de este comando. Sin embargo, el registro se realizará correctamente.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Espere cinco minutos a que finalice el registro. Puede comprobar el estado de la aprobación con el siguiente comando. Asegúrese de que RegistrationState sea `Registered` antes de continuar.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Ahora cambie CLI al modo `asm`.

	azure config mode asm

## Paso 3: Ejecución de comandos para migrar los recursos de IaaS

>[AZURE.NOTE] Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. De ese modo, la plataforma intentará de nuevo la acción.

### Migración de máquinas virtuales en un servicio en la nube (no en una red virtual)

Obtenga la lista de servicios en la nube mediante el siguiente comando y seleccione luego el servicio en la nube que quiera migrar. Tenga en cuenta que si las máquinas virtuales del servicio en la nube están en una red virtual o tienen roles web o de trabajo, recibirá un mensaje de error.

	azure service list

Ejecute el siguiente comando para obtener el nombre de la implementación del servicio en la nube desde la salida detallada. En la mayoría de los casos, el nombre de la implementación es el mismo que el nombre del servicio de nube.

	azure service show servicename -vv

Prepare las máquinas virtuales del servicio en la nube para la migración. Tiene dos opciones para elegir.

Si quiere migrar las máquinas virtuales a una red virtual creada en una plataforma, use el siguiente comando.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Si quiere migrar a una red virtual existente en el modelo de implementación de Resource Manager, utilice el siguiente comando.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

Cuando la operación de preparación finalice correctamente, puede consultar la salida detallada para obtener el estado de migración de las máquinas virtuales y asegurarse de que están en estado `Prepared`.

	azure vm show vmName -vv

Compruebe la configuración de los recursos preparados mediante la CLI o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando.

	azure service deployment abort-migration serviceName deploymentName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	azure service deployment commit-migration serviceName deploymentName

### Migración de máquinas virtuales en una red virtual

Seleccione la red virtual que quiere migrar. Tenga en cuenta que si la red virtual contiene roles web o de trabajo, o bien máquinas virtuales con configuraciones no admitidas, recibirá un mensaje de error de validación.

Obtenga todas las redes virtuales de la suscripción con el siguiente comando:

	azure network vnet list

Prepare la red virtual de su elección para la migración mediante el siguiente comando:

	azure network vnet prepare-migration virtualnetworkname

Compruebe la configuración de las máquinas virtuales preparadas mediante la CLI o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando.

	azure network vnet abort-migration virtualnetworkname

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	azure network vnet commit-migration virtualnetworkname

### Migración de una cuenta de almacenamiento

Cuando haya terminado de migrar las máquinas virtuales, se recomienda migrar la cuenta de almacenamiento.

Prepare la cuenta de almacenamiento para la migración mediante el siguiente comando.

	azure storage account prepare-migration storageaccountname

Compruebe la configuración de la cuenta de almacenamiento preparada mediante la CLI o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando.

	azure storage account abort-migration storageaccountname

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	azure storage account commit-migration storageaccountname

## Pasos siguientes

- [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0706_2016-->