---
title: Mantenimiento planeado de VM Windows | Microsoft Docs
description: "Entienda qué es el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales Windows que se ejecutan en Azure."
services: virtual-machines-windows
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: f81d4a407e738aa8133e91a24f06feba5af75e63


---
# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Mantenimiento planeado de máquinas virtuales Linux en Azure
Entienda qué es el mantenimiento planeado de Azure y cómo afecta a la disponibilidad de sus máquinas virtuales Windows. Este artículo también está disponible para [máquinas virtuales Linux](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Este artículo proporciona un fondo sobre el proceso de mantenimiento planeado de Azure. Si desea saber por qué su máquina virtual se reinicia, puede [leer esta entrada de blog que detalla la visualización de los registros de reinicio de la máquina virtual](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Por qué Azure realiza mantenimiento planeado
Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Muchas de esas actualizaciones se realizan sin que afecten a las máquinas virtuales o a los servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas de estas actualizaciones requieren que las máquinas virtuales se reinicien para aplicar a la infraestructura las actualizaciones requeridas. Las máquinas virtuales se cierran mientras arreglamos la infraestructura y después las máquinas virtuales se reinician.

Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de las máquinas virtuales: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para más información sobre el mantenimiento planeado, consulte [Mantenimiento planeado frente a mantenimiento no planeado](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]




<!--HONumber=Nov16_HO3-->


