<properties
	pageTitle="Solución de problemas de errores de asignación de una VM de Windows | Microsoft Azure"
	description="Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una VM de Windows en Azure"
	services="virtual-machines-windows, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de las VM de Windows en Azure

Cuando se crea o se cambia el tamaño de una VM (máquina virtual), o bien se reinician las detenidas (desasignadas), Microsoft Azure asigna recursos de proceso a la suscripción. En ocasiones, es posible que reciba errores al realizar estas operaciones incluso antes de llegar a los límites de la suscripción de Azure. En este artículo se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones. La información también puede ser útil si tiene pensado realizar la implementación de sus servicios.

En la sección "Pasos generales para solucionar problemas" se describen los pasos necesarios para resolver problemas habituales. En la sección “Pasos de la solución de problemas detallada” se ofrecen los pasos de resolución por mensaje de error específico. Antes de empezar, le ofrecemos información de contexto que explica cómo funciona la asignación y por qué se producen errores de asignación. También puede [solucionar los errores de asignación al crear, reiniciar o cambiar de tamaño máquinas virtuales Linux en Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0330_2016-->