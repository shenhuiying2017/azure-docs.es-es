---
title: "Diferentes formas de crear una máquina virtual Windows | Microsoft Docs"
description: "Enumera las diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a5e0ba0128b7f20d6307f5dea784de007e71321c


---
# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos
Azure ofrece varias formas de crear una máquina virtual porque las máquinas virtuales son adecuadas para distintos usuarios y objetivos. Esto significa que tendrá tomar algunas decisiones sobre la máquina virtual y cómo crearla. Este artículo ofrece un resumen de estas opciones y vínculos a instrucciones.

## <a name="azure-portal"></a>Portal de Azure
El portal de Azure es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure. 

[Creación de una máquina virtual que ejecuta Windows en el portal](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>Plantilla
Las máquinas virtuales requieren una combinación de recursos (por ejemplo, conjuntos de disponibilidad y cuentas de almacenamiento). En lugar de implementar y administrar cada recurso por separado, puede crear una plantilla de Azure Resource Manager que implementa y aprovisiona todos los recursos en una operación única y coordinada.

* [Creación de una máquina virtual Windows con una plantilla del Administrador de recursos](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
Si prefiere trabajar en un shell de comandos, puede usar PowerShell de Azure.

* [Creación de una máquina virtual Windows con PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
Utilice Visual Studio para crear, administrar e implementar máquinas virtuales con Azure Tools para Visual Studio y el SDK de Azure.

[Herramientas de Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)




<!--HONumber=Nov16_HO3-->


