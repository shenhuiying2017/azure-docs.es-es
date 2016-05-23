<properties
	pageTitle="Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure"
	description="Este artículo lo guía por las funcionalidades del servicio de migración compatible con la plataforma. Service Management para Azure Resource Manager con la CLI de Azure."
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

Estos pasos le muestran cómo utilizar los comandos de CLI de Azure para migrar recursos de IaaS del modelo clásico a Resource Manager. El artículo requiere haber iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) (`azure login`).

## Paso 1: Preparación para la migración

Estas son algunas prácticas recomendadas a la hora de evaluar la migración de recursos de IaaS desde el modelo clásico a Resource Manager

- Lea la lista de configuraciones o características no admitidas [aquí](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones o características no admitidas, se recomienda esperar a que se anuncie dicha compatibilidad. Como alternativa, puede quitar esa característica o salir de esa configuración para habilitar la migración si se ajusta a sus necesidades.
-	Si ha automatizado los scripts que implementan la infraestructura y las aplicaciones hoy. Intente crear un programa de instalación de prueba similar con esos scripts para la migración. Si lo prefiere, también puede configurar entornos de ejemplo mediante el Portal de Azure.
- Puesto que el servicio está en una versión preliminar pública, asegúrese de que el entorno de prueba para la migración está aislado de su entorno de producción. No mezcle cuentas de almacenamiento, redes virtuales u otros recursos entre los entornos de prueba y producción.

## Paso 2: Establecimiento de la suscripción para la versión preliminar pública de la migración

Para los escenarios de migración, debe configurar el entorno para el modelo clásico y Resource Manager. [Instale la CLI de Azure](../xplat-cli-install.md) y [seleccione su suscripción](../xplat-cli-connect.md).

Seleccione la suscripción de Azure con el siguiente comando:

	azure account set "azure-subscription-name"

Regístrese para la versión preliminar pública con el siguiente comando. Tenga en cuenta que en algunos casos se excederá el tiempo de espera de este comando; sin embargo, el registro tendrá éxito. Continúe con el paso siguiente para comprobar el estado del registro.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Espere cinco minutos a que se complete el registro. Puede comprobar el estado de aprobación con el siguiente comando. Asegúrese de que el estado de RegistrationState es 'Registrado' antes de continuar.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Ahora cambie CLI al modo asm.

	azure config mode asm

## Paso 3: Comandos para migrar los recursos de IaaS

>[AZURE.NOTE] Tenga en cuenta que todas las operaciones que se describen a continuación son idempotentes. Si surge cualquier problema, aparte de un error de configuración o característica no admitida, se recomienda repetir la operación de preparación, anulación o confirmación y la plataforma volverá a intentar la acción.

### Migración de máquinas virtuales en un servicio en la nube (no en una red virtual)

Obtenga la lista de Servicios en la nube mediante el siguiente comando y seleccione el servicio en la nube que desea migrar. Tenga en cuenta que si las máquinas virtuales en el servicio en la nube están en una red virtual o tienen roles web o de trabajo, recibirá un mensaje de error.

	azure service list

Ejecute el siguiente comando para obtener el nombre de la implementación del servicio en la nube desde la salida detallada. En la mayoría de los casos, el nombre de la implementación es el mismo que el nombre del servicio de nube.

	azure service show servicename -vv

Prepare las máquinas virtuales del servicio en la nube para la migración. Tiene dos opciones para elegir.

Si desea migrar las máquinas virtuales a una red virtual creada en una plataforma, use el siguiente comando.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Si desea migrar a una red virtual existente en el modelo de implementación de Resource Manager, utilice el siguiente comando.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

Una vez que la operación de preparación finaliza correctamente, puede consultar la salida detallada para obtener el estado de migración de la máquina virtual y asegurarse de que las máquinas virtuales están en estado 'Preparado'.

	azure vm show vmName -vv

Compruebe la configuración de los recursos preparados mediante CLI o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

	azure service deployment abort-migration serviceName deploymentName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	azure service deployment commit-migration serviceName deploymentName

### Migración de máquinas virtuales en una red virtual

Seleccione la red virtual que desea migrar. Tenga en cuenta que si la red virtual contiene roles web o de trabajo, o bien máquinas virtuales con configuraciones no admitidas, recibirá un mensaje de error de validación.

Obtenga todas las redes virtuales de la suscripción con el siguiente comando:

	azure network vnet list

Prepare la red virtual para la migración mediante el siguiente comando:

	azure network vnet prepare-migration virtualnetworkname

Compruebe la configuración de las máquinas virtuales preparadas mediante CLI o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

	azure network vnet abort-migration virtualnetworkname

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	azure network vnet commit-migration virtualnetworkname

## Referencias

- [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0511_2016-->