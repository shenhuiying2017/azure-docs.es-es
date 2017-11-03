---
title: "Opciones de depuración de servicios en la nube de Azure | Microsoft Docs"
description: "Depuración de servicios en la nube de Azure"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 196a39a75f8a3151c277f0b2a7944e6886192171
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Más información sobre las diversas maneras de depurar un servicio en la nube de Azure
Este artículo contiene vínculos a las diversas maneras de depurar un servicio en la nube de Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Depuración de un servicio en la nube de Azure en Visual Studio
Puede ahorrar tiempo y dinero si utiliza el emulador de proceso de Azure para depurar su servicio en la nube en un equipo local. La depuración de un servicio localmente antes de su implementación puede mejorar la fiabilidad y el rendimiento sin pagar por tiempo de proceso. No obstante, podrían producirse algunos errores solo al ejecutar un servicio en la nube en Azure. Los errores que se producen solo al ejecutar un servicio en la nube de Azure pueden depurarse habilitando la depuración remota cuando se publique el servicio. Luego, se debe asociar el depurador a una instancia de rol. Para obtener más información, consulte [Depuración del servicio en la nube en el equipo local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-azure-diagnostics"></a>Uso de Azure Diagnostics 
Puede usar Diagnósticos de Azure para registrar información detallada del código que se ejecuta en los roles, bien sea en el entorno de desarrollo o en Azure. Para más información, consulte [Habilitación de diagnósticos de Azure en servicios en la nube de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

## <a name="using-intellitrace"></a>Uso de IntelliTrace 
Si usa Visual Studio Enterprise para escribir roles destinados a .NET Framework 4.5, puede habilitar IntelliTrace en el momento de implementar un servicio en la nube de Azure desde Visual Studio. IntelliTrace proporciona un registro que se puede usar con Visual Studio para depurar la aplicación como si se estuviera ejecutando en Azure. Para obtener más información, vea [Depurar con IntelliTrace y Visual Studio un servicio en la nube](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Depuración remota 
Puede habilitar la depuración remota en los servicios en la nube cuando implementa el servicio en la nube desde Visual Studio. Si elige habilitar la depuración remota para una implementación, los servicios de depuración remota se instalan en las máquinas virtuales que ejecutan cada instancia de rol. Estos servicios, como `msvsmon.exe`, no afectan al rendimiento ni al resultado en cuanto a costos adicionales. Para obtener más información, consulte [Depuración de un servicio en la nube en Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Pasos siguientes
- [Depurar una máquina virtual o un servicio en la nube de Azure en Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md): Obtenga información sobre cómo depurar los servicios en la nube de Azure.