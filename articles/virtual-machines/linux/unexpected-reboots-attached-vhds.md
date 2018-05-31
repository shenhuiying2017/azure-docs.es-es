---
title: Solución de problemas de reinicio inesperado de máquinas virtuales Linux de Azure con discos duros virtuales conectados | Microsoft Docs
description: Solución de problemas de reinicio inesperado de máquinas virtuales Linux.
keywords: conexión ssh rechazada, error de ssh azure ssh, error de conexión ssh
services: virtual-machines-linux
author: tamram
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: 8ccb6b61c8de1599cd3db011d6401c781cefc31a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779743"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solución de problemas de reinicio inesperado de máquinas virtuales con discos duros virtuales conectados

Si una máquina virtual de Azure (VM) tiene una gran cantidad de discos duros virtuales de la misma cuenta de almacenamiento conectados, es posible que supere los objetivos de escalabilidad de una cuenta de almacenamiento individual, lo que provocaría un reinicio inesperado en la máquina virtual. Compruebe las métricas por minuto de la cuenta de almacenamiento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para ver si hay picos que superen los objetivos de escalabilidad de una cuenta de almacenamiento. Consulte la sección [Las métricas muestran un aumento de PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para recibir asistencia a la hora de averiguar si se produjo alguna limitación en la cuenta de almacenamiento.

En general, cada operación individual de entrada o salida de un VHD desde una máquina virtual se traduce en operaciones **Get Page** o **Put Page** en el blob en páginas subyacente. Por lo tanto, puede usar el IOPS estimado para su entorno con el fin de ajustar la cantidad de VHD que puede tener en una sola cuenta de almacenamiento, en función del comportamiento específico de la aplicación. Microsoft recomienda tener 40 o menos discos en una cuenta de almacenamiento única. Consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../../storage/common/storage-scalability-targets.md) para detalles de los objetivos de escalabilidad para las cuentas de almacenamiento, en particular, el ancho de banda total y la velocidad de solicitudes total para el tipo de cuenta de almacenamiento que usa.

Si supera los objetivos de escalabilidad para la cuenta de almacenamiento, coloque los VHD en varias cuentas de almacenamiento para reducir la actividad de cada cuenta individual.
