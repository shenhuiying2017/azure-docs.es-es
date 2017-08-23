---
title: "Administración de Azure Web Apps mediante Azure Automation | Microsoft Docs"
description: "Obtenga información acerca de cómo puede usarse el servicio para administrar Aplicaciones web de Azure."
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 4edd2696c9a5709ded6e2a3e352090775335f0d2
ms.openlocfilehash: 4fcfa2e7ec2e8257407026ed4cca0e15fd0b5bb6
ms.contentlocale: es-es
ms.lasthandoff: 12/06/2016

---
# <a name="managing-azure-web-app-using-azure-automation"></a>Administración de aplicaciones web de Azure mediante Automatización de Azure
Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de Aplicaciones web de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es Automatización de Azure?
[Automatización de Azure](../automation/automation-intro.md) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>¿Cómo puede ayudar Automatización de Azure a administrar Aplicaciones web de Azure?
Las aplicaciones web se pueden administrar en Automatización de Azure mediante los cmdlets de PowerShell que están disponibles en los [módulos de Azure PowerShell](/powershell/azureps-cmdlets-docs). Puede [instalar estos cmdlets de Aplicaciones web de PowerShell en Automatización de Azure](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/)para que pueda realizar todas las tareas de administración de Aplicaciones web dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Estos son algunos ejemplos de administración de Servicios de aplicaciones con Automatización:

* [Scripts para administrar Aplicaciones web](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar Aplicaciones web de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

* Consulte el [Tutorial de introducción](../automation/automation-first-runbook-graphical.md)


