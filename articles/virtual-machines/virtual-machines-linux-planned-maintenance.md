---
title: "Mantenimiento planeado de máquinas virtuales Linux | Microsoft Docs"
description: "Entienda qué es el mantenimiento planeado de Azure y cómo afecta a las máquinas virtuales Linux que se ejecutan en Azure"
services: virtual-machines-linux
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e65e614c-e969-40dc-a271-fe074069daf1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d0e7d00dd8e6ab53897340e13a3170519cbdb135
ms.lasthandoff: 11/17/2016


---
# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Mantenimiento planeado de máquinas virtuales Linux en Azure
Entienda qué es el mantenimiento planeado de Azure y cómo afecta a la disponibilidad de sus máquinas virtuales Linux. Este artículo también está disponible para [máquinas virtuales Windows](virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Este artículo proporciona un fondo sobre el proceso de mantenimiento planeado de Azure. Si desea saber por qué su máquina virtual se reinicia, puede [leer esta entrada de blog que detalla la visualización de los registros de reinicio de la máquina virtual](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Por qué Azure realiza mantenimiento planeado
Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Muchas de esas actualizaciones se realizan sin que afecten a las máquinas virtuales o a los servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas de estas actualizaciones requieren que las máquinas virtuales se reinicien para aplicar a la infraestructura las actualizaciones requeridas. Las máquinas virtuales se cierran mientras arreglamos la infraestructura y después las máquinas virtuales se reinician.

Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de las máquinas virtuales: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para más información sobre el mantenimiento planeado, consulte [Mantenimiento planeado frente a mantenimiento no planeado](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

