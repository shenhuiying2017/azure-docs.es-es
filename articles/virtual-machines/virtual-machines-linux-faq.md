<properties
	pageTitle="Preguntas frecuentes sobre las máquinas virtuales de Linux | Microsoft Azure"
	description="Proporciona respuestas a algunas de las preguntas frecuentes sobre las máquinas virtuales de Linux creadas con el modelo de Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Preguntas frecuentes sobre las máquinas virtuales de Linux 


En este artículo se responden algunas preguntas comunes que los usuarios plantean sobre las máquinas virtuales Linux creadas en Azure mediante el modelo de implementación de Resource Manager. Para ver la versión de Windows de este tema, consulte [Preguntas más frecuentes sobre máquinas virtuales Windows](virtual-machines-windows-faq.md).

## ¿Qué puedo ejecutar en una máquina virtual de Azure?

Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Para obtener información más detallada, consulte [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md).


## ¿Cuánto almacenamiento puedo usar con una máquina virtual?

Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).



## ¿Cómo puedo tener acceso a mi máquina virtual?

Debe establecer una conexión remota para iniciar sesión en la máquina virtual mediante Secure Shell (SSH). Consulte las instrucciones sobre cómo conectarse [desde Windows](virtual-machines-linux-ssh-from-windows.md) o [desde Linux y Mac](virtual-machines-linux-mac-create-ssh-keys.md). De forma predeterminada, SSH permite un máximo de 10 conexiones simultáneas. Puede aumentar este número editando el archivo de configuración.


Si tiene problemas, consulte [Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-linux-troubleshoot-ssh-connection.md).

## ¿Puedo usar el disco temporal (/dev/sdb1) para almacenar datos?

No utilice el disco temporal (/dev/sdb1) para almacenar datos. Solamente proporciona almacenamiento de forma temporal, por lo que corre el riesgo de perder datos irrecuperables.

## ¿Puedo copiar o clonar una máquina virtual de Azure existente?

Sí. Para obtener instrucciones, consulte [Creación de una copia de una máquina virtual Linux en el modelo de implementación de Resource Manager](virtual-machines-linux-copy-vm.md).

## ¿Por qué no veo las regiones de Canadá central y Canadá oriental por medio de Azure Resource Manager?

Las dos nuevas áreas Canadá central y Canadá oriental no se registran automáticamente para la creación de máquinas virtuales en las suscripciones de Azure existentes. Este registro se realizará automáticamente cuando se implementa una máquina virtual mediante el Portal de Azure en cualquier otra región usando Azure Resource Manager. Después de implementar una máquina virtual en cualquier otra región de Azure, las áreas nuevas deberán estar disponibles para las máquinas virtuales siguientes.

## ¿Puedo agregar una NIC a mi máquina virtual después de crearla?

No. Solo se puede agregar una NIC en el momento de la creación.

<!---HONumber=AcomDC_0824_2016-->