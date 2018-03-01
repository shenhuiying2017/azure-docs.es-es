---
title: "Administración de Azure API Management mediante Azure Automation"
description: "Obtenga información acerca de cómo puede usarse el servicio de Azure Automation para administrar Azure API Management."
services: api-management, automation
documentationcenter: 
author: vladvino
manager: eamono
editor: 
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 8bd251fa05e8e144471cd577cec607d91e5ab84c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Administración de Azure API Management mediante Azure Automation
Esta guía presenta el servicio Azure Automation y cómo se puede usar para simplificar la administración de Azure API Management.

## <a name="what-is-azure-automation"></a>¿Qué es Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Azure Automation, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Azure Automation proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que se adapta para satisfacer sus necesidades. En Azure Automation, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>¿Cómo puede ayudar Azure Automation a administrar Azure API Management?
API Management se puede administrar en Azure Automation mediante el uso de [cmdlets de Windows PowerShell para la API de Azure API Management](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/). En Azure Automation, puede escribir scripts de flujo de trabajo de PowerShell para llevar a cabo muchas de las tareas de API Management con los cmdlets. También puede emparejar estos cmdlets en Azure Automation con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Estos son algunos ejemplos del uso de API Management con Automation:

* [Azure API Management – Using PowerShell for backup and restore](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Azure Automation y cómo se puede usar para administrar Azure API Management, siga estos vínculos para obtener más información.

* Consulte el [tutorial de introducción](../automation/automation-first-runbook-graphical.md) de Azure Automation.

