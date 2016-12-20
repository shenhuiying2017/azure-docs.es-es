---
title: "Administración de Azure Service Bus con Azure Automation | Microsoft Docs"
description: "Obtenga información sobre cómo usar el servicio de Automatización de Azure para administrar Bus de servicio de Azure."
services: service-bus-messaging, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 2a06e94b-92ef-4b5d-a2b7-fe827063df82
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1912275b05948525486e4e30e2065bd7f63630cb


---
# <a name="managing-azure-service-bus-using-azure-automation"></a>Administración de Bus de servicio de Azure con Automatización de Azure
Esta guía presenta el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de Bus de servicio de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es Automatización de Azure?
[Automatización de Azure](../automation/automation-intro.md) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos y la configuración de estado deseado. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>¿Cómo puede ayudar Automatización de Azure a administrar Bus de servicio de Azure?
Puede administrar el Bus de servicio con la Automatización de Azure mediante el uso de la [API de REST de Bus de servicio](https://msdn.microsoft.com/library/azure/mt639375.aspx). En Automatización de Azure, puede ejecutar scripts de PowerShell para realizar muchas de las tareas del Bus de servicio con las API de REST. También puede emparejar estas llamadas de API de REST en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas en los servicios de Azure y en sistemas de terceros.

Los siguientes son algunos ejemplos de cómo usar PowerShell para administrar Bus de servicio de Azure:

* [Cmdlets personalizados de PowerShell para administrar colas de Bus de servicio de Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Creación de espacios de nombres de Bus de servicio de Azure con PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

El módulo de PowerShell que se utiliza con el Bus de servicio de Azure en cuadernos de automatización puede descargarse en la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar Bus de servicio de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

* Consulte el [Tutorial de introducción](../automation/automation-first-runbook-graphical.md)
* Consulte cómo [administrar Bus de servicio con PowerShell](service-bus-powershell-how-to-provision.md)




<!--HONumber=Nov16_HO3-->


