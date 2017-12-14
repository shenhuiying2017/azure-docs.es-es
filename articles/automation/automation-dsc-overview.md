---
title: "Información general de DSC de Azure Automation | Microsoft Docs"
description: "Una información general de Configuración de estado deseado (DSC) de Azure Automation, sus condiciones y problemas conocidos"
services: automation
documentationcenter: dev-center-name
author: georgewallace
manager: carmonm
keywords: "powershell dsc, configuración de estado deseado, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: gwallace
ms.openlocfilehash: 7440fa58f9cbd201984d178a7e9a2c12afc84783
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-dsc-overview"></a>Información general de DSC de Azure Automation

DSC de Azure Automation es un servicio de Azure que le permite escribir, administrar y compilar [configuraciones](https://msdn.microsoft.com/powershell/dsc/configurations) de configuración de estado deseado (DSC) de PowerShell, importar [recursos de DSC](https://msdn.microsoft.com/powershell/dsc/resources) y asignar configuraciones a nodos de destino, todo en la nube.

## <a name="why-use-azure-automation-dsc"></a>¿Por qué usar DSC de Azure Automation?

DSC de Azure Automation proporciona varias ventajas con respecto a DSC fuera de Azure.

### <a name="built-in-pull-server"></a>Servidor de extracción integrado

Azure Automation proporciona un [servidor de extracción de DSC](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver) para que los nodos de destino reciban automáticamente las configuraciones, se ajusten al estado deseado y devuelvan la información de cumplimiento.
El servidor de extracción integrado en Azure Automation elimina la necesidad de configurar y mantener su propio servidor de extracción.
Azure Automation puede tener como destino máquinas físicas o virtuales Windows o Linux, en la nube o en un entorno local.

### <a name="management-of-all-your-dsc-artifacts"></a>Administración de todos los artefactos de DSC

DSC de Azure Automation lleva a la [configuración de estado deseado de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) la misma capa de administración que ofrece Azure Automation para el scripting de PowerShell.

Desde Azure Portal o desde PowerShell, puede administrar todas las configuraciones, recursos y nodos de destino de DSC.

![Captura de pantalla de la hoja de Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importación de datos de informes a Log Analytics

Los nodos que se administran con DSC de Azure Automation envían datos de informe de estado detallados al servidor de extracción integrado.
Puede configurar DSC de Azure Automation para que envíe estos datos a su área de trabajo de Log Analytics de Microsoft Operations Management Suite (OMS).
Para obtener información sobre cómo enviar datos de estado de DSC al área de trabajo de Log Analytics, vea [Reenvío de datos de informes de DSC de Azure Automation a Log Analytics de OMS](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Vídeo de presentación

¿Prefiere ver a leer? Eche un vistazo al vídeo siguiente, de mayo de 2015, cuando se anunció DSC de Azure Automation.

>[!NOTE]
>Aunque los conceptos y el ciclo de vida que se tratan en este vídeo son correctos, DSC de Azure Automation ha progresado mucho desde que se grabó este vídeo.
>Ahora está disponible con carácter general, tiene una interfaz de usuario mucho más amplia en el Portal de Azure y admite muchas funciones adicionales.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo incorporar nodos para que se administren con DSC de Azure Automation, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).
* Para empezar a usar DSC de Azure Automation, consulte [Introducción a DSC de Azure Automation](automation-dsc-getting-started.md).
* Para obtener información acerca de la compilación de configuraciones de DSC para que poder asignarlas a los nodos de destino, vea [Compilación de configuraciones en DSC de Azure Automation](automation-dsc-compile.md).
* Para ver la referencia de cmdlets de PowerShell para DSC de Azure Automation, consulte [Cmdlets de DSC de Azure Automation](/powershell/module/azurerm.automation/#automation).
* Para obtener información de precios, consulte [Precios de DSC de Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
* Para ver un ejemplo del uso de DSC de Azure Automation en una canalización de implementación continua, consulte [Implementación continua en máquinas virtuales IaaS con DSC de Automation y Chocolatey](automation-dsc-cd-chocolatey.md)