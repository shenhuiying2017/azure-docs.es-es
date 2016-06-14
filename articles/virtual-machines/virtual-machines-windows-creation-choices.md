<properties
	pageTitle="Diferentes formas de crear una máquina virtual Windows | Microsoft Azure"
	description="Enumera las diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="03/11/2016"
	ms.author="cynthn"/>

# Diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos

Azure ofrece varias formas de crear una máquina virtual porque las máquinas virtuales son adecuadas para distintos usuarios y objetivos. Esto significa que tendrá tomar algunas decisiones sobre la máquina virtual y cómo crearla. Este artículo ofrece un resumen de estas opciones y vínculos a instrucciones.

## Portal de Azure

El portal de Azure es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure.

[Creación de una máquina virtual que ejecuta Windows en el portal](virtual-machines-windows-hero-tutorial.md)

## Azure PowerShell

Si prefiere trabajar en un shell de comandos, puede usar PowerShell de Azure.

- [Creación de una máquina virtual Windows con PowerShell](virtual-machines-windows-ps-create.md)
- [Creación de una máquina virtual Windows con una plantilla del Administrador de recursos](virtual-machines-windows-ps-template.md)

## Plantilla

Las máquinas virtuales requieren una combinación de recursos (por ejemplo, conjuntos de disponibilidad y cuentas de almacenamiento). En lugar de implementar y administrar cada recurso por separado, puede crear una plantilla de Azure Resource Manager que implementa y aprovisiona todos los recursos en una operación única y coordinada.

- [Creación de una máquina virtual Windows con una plantilla del Administrador de recursos](virtual-machines-windows-ps-template.md)

## Visual Studio

[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento](virtual-machines-windows-csharp.md)

<!---HONumber=AcomDC_0601_2016-->