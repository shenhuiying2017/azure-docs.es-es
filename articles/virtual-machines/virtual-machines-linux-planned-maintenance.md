---
title: Mantenimiento planeado de máquinas virtuales Linux | Microsoft Docs
description: Entienda qué es el mantenimiento planeado de Azure y cómo afecta a las máquinas virtuales Linux que se ejecutan en Azure
services: virtual-machines-linux
documentationcenter: ''
author: drewm
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: drewm

---
# Mantenimiento planeado de máquinas virtuales Linux en Azure
Entienda qué es el mantenimiento planeado de Azure y cómo afecta a la disponibilidad de sus máquinas virtuales Linux. Este artículo también está disponible para [máquinas virtuales Windows](virtual-machines-windows-planned-maintenance.md).

Este artículo proporciona un fondo sobre el proceso de mantenimiento planeado de Azure. Si desea saber por qué su máquina virtual se reinicia, puede [leer esta entrada de blog que detalla la visualización de los registros de reinicio de la máquina virtual](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Por qué Azure realiza mantenimiento planeado
Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Muchas de esas actualizaciones se realizan sin que afecten a las máquinas virtuales o a los servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas de estas actualizaciones requieren que las máquinas virtuales se reinicien para aplicar a la infraestructura las actualizaciones requeridas. Las máquinas virtuales se cierran mientras arreglamos la infraestructura y después las máquinas virtuales se reinician.

Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de las máquinas virtuales: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para obtener más información acerca del mantenimiento planeado, consulte [Mantenimiento planeado frente a mantenimiento no planeado](virtual-machines-linux-manage-availability.md).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0525_2016-->