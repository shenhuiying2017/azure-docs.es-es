<properties
	pageTitle="Preguntas más frecuentes sobre máquinas virtuales Windows | Microsoft Azure"
	description="Proporciona respuestas a algunas de las preguntas frecuentes sobre las máquinas virtuales Windows creadas con el modelo de Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Preguntas más frecuentes sobre máquinas virtuales Windows 


En este artículo se responden algunas preguntas frecuentes que los usuarios plantean sobre las máquinas virtuales Windows creadas en Azure mediante el modelo de implementación de Resource Manager. Para la versión de Linux de este tema, consulte [Preguntas frecuentes sobre las máquinas virtuales de Linux](virtual-machines-linux-faq.md).

## ¿Qué puedo ejecutar en una máquina virtual de Azure?

Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Para obtener información sobre la directiva de soporte de software de servidor de Microsoft ejecutado en Azure, consulte [Microsoft server software support for Azure Virtual Machines](https://support.microsoft.com/kb/2721672) (Soporte de software de servidor de Microsoft para las máquinas virtuales de Azure).

Existen determinadas versiones de Windows 7 y Windows 8.1 para suscriptores de ventajas de MSDN Azure y suscriptores de pago por uso de desarrollo y prueba de MSDN, para tareas de desarrollo y prueba. Para obtener más información, como instrucciones y limitaciones, consulte [Imágenes de cliente de Windows para los suscriptores de MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).


## ¿Cuánto almacenamiento puedo usar con una máquina virtual?

Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).


## ¿Cómo puedo tener acceso a mi máquina virtual?

Debe establecer una conexión remota mediante conexión a Escritorio remoto (RDP) para una máquina virtual Windows. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](virtual-machines-windows-connect-logon.md). Se admite un máximo de 2 conexiones simultáneas, a menos que el servidor esté configurado como un host de sesión de servicios de escritorio remoto.


Si tiene problemas con Escritorio remoto, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Si está familiarizado con Hyper-V, puede que esté buscando una herramienta similar a VMConnect. Azure no ofrece una herramienta similar porque no admite el acceso de la consola a una máquina virtual.

## ¿Puedo usar el disco temporal (la unidad D: de forma predeterminada) para almacenar datos?

No debe utilizar el disco temporal para almacenar datos. Solo proporciona almacenamiento de forma temporal, por lo que se arriesgaría a perder datos que no se pueden recuperar. Esto puede ocurrir cuando se mueve la máquina virtual a un host diferente. Cambiar el tamaño de una máquina virtual, actualizar el host o un error de hardware en el host son algunas de las razones por las que se puede mover una máquina virtual.

Si tiene una aplicación que necesita usar la letra de unidad D:, puede reasignar las letras de unidad para que el disco temporal utilice otra distinta a D:. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).

## ¿Cómo puedo cambiar la letra de la unidad del disco temporal?

Para cambiar la letra de la unidad, mueva el archivo de paginación y reasigne las letras de unidad; sin embargo, deberá asegurarse de realizar los pasos en un orden específico. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).

## ¿Puedo agregar una máquina virtual existente a un conjunto de disponibilidad?

No. Si desea que la máquina virtual forme parte de un conjunto de disponibilidad, debe crearla dentro del conjunto. Actualmente no es posible agregar una máquina virtual a un conjunto de disponibilidad una vez creada.

## ¿Puedo cargar una máquina virtual en Azure?

Sí. Para obtener instrucciones, consulte [Carga de una imagen de VM de Windows en Microsoft Azure para implementaciones del Administrador de recursos](virtual-machines-windows-upload-image.md).

## ¿Puedo cambiar el tamaño del disco del sistema operativo?

Sí. Para obtener instrucciones, consulte [Cómo ampliar la unidad de sistema operativo de una máquina virtual en un grupo de recursos de Azure](virtual-machines-windows-expand-os-disk.md).

## ¿Puedo copiar o clonar una máquina virtual de Azure existente?

Sí. Para obtener más información, consulte, [Creación de una copia de una máquina virtual Windows en el modelo de implementación de Resource Manager](virtual-machines-windows-specialized-image.md).

## ¿Por qué no veo las regiones de Canadá central y Canadá oriental por medio de Azure Resource Manager?

Las dos nuevas áreas Canadá central y Canadá oriental no se registran automáticamente para la creación de máquinas virtuales en las suscripciones de Azure existentes. Este registro se realizará automáticamente cuando se implementa una máquina virtual mediante el Portal de Azure en cualquier otra región usando Azure Resource Manager. Después de implementar una máquina virtual en cualquier otra región de Azure, las áreas nuevas deberán estar disponibles para las máquinas virtuales siguientes.

## ¿Se admiten máquinas virtuales Linux en Azure?

Sí. Para crear rápidamente una máquina virtual Linux de prueba, consulte [Creación de una máquina virtual de Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md).

## ¿Puedo agregar una NIC a mi máquina virtual después de crearla?

No. Solo se puede agregar una NIC en el momento de la creación.

<!---HONumber=AcomDC_0817_2016-->