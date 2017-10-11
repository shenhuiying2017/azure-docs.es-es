---
title: "Administración de Azure Key Vault con Azure Automation | Microsoft Docs"
description: "Obtenga información acerca de cómo puede usarse el servicio de Automatización de Azure para administrar el Almacén de claves de Azure."
services: Key-Vault, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
ms.openlocfilehash: dee39662472fe54776b591977f2b1ecb39d15b00
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Administración del Almacén de claves de Azure mediante Automatización de Azure
Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de claves y secretos en el Almacén de claves de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es Automatización de Azure?
[Automatización de Azure](../automation/automation-intro.md) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos y la configuración de estado deseado. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>¿Cómo puede ayudar Automatización de Azure a administrar el Almacén de claves de Azure?
Key Vault se puede administrar en Azure Automation mediante los [cmdlets AzureRM de Key Vault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) y los [cmdlets de Azure Key Vault clásico](https://msdn.microsoft.com/library/azure/dn868052.aspx). El módulo de Azure para administrar el Almacén de claves clásico está disponible automáticamente en Automatización de Azure, mientras que el [módulo AzureRM-KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) se puede importar a Automatización de Azure para poder realizar muchas de las tareas de administración de Almacén de claves dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Con los cmdlets del Almacén de claves de Azure puede realizar estas tareas entre otras: 

* Crear y configurar un almacén de claves
* Crear o importar una clave
* Crear o actualizar un secreto
* Actualizar los atributos de una clave
* Obtener una clave o un secreto
* Eliminar una clave o un secreto

Estos son algunos ejemplos de cómo usar PowerShell para administrar el Almacén de claves:  

* [Azure Key Vault - Step by Step (Almacén de claves de Azure: paso a paso)](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Setting Up and Configuring an Azure Key Vault (Instalar y configurar un Almacén de claves de Azure)](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar el Almacén de claves de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

* Consulte el [Tutorial de introducción](../automation/automation-first-runbook-graphical.md)de Automatización de Azure.
* Consulte los [scripts de PowerShell del Almacén de claves de Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

