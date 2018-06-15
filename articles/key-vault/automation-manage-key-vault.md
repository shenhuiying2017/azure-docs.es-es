---
title: Administración de Azure Key Vault con Azure Automation | Microsoft Docs
description: Obtenga información acerca de cómo puede usarse el servicio Azure Automationpara administrar Azure Key Vault.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.openlocfilehash: b5f8a1b826717d51729b0bb621bf26e35d4bdd36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406881"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Administración de Azure Key Vault mediante Azure Automation
Esta guía le ofrece el servicio Azure Automation y cómo se puede usar para simplificar la administración de claves y secretos en Azure Key Vault.

## <a name="what-is-azure-automation"></a>¿Qué es Azure Automation?
[Azure Automation](../automation/automation-intro.md) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos y la configuración de estado deseado. Mediante Azure Automation, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Azure Automation proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza el escalamiento para satisfacer sus necesidades. En Azure Automation, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>¿Cómo puede ayudar Azure Automation a administrar Azure Key Vault?
Key Vault se puede administrar en Azure Automation mediante los [cmdlets AzureRM de Key Vault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) y los [cmdlets de Azure Key Vault clásico](https://msdn.microsoft.com/library/azure/dn868052.aspx). El módulo de Azure para administrar la versión clásica de Key Vault está disponible automáticamente en Azure Automation, mientras que el [módulo AzureRM-KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) se puede importar a Azure Automation para poder realizar muchas de las tareas de administración de Key Vault dentro del servicio. También puede emparejar estos cmdlets en Azure Automation con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Con los cmdlets de Azure Key Vault puede realizar estas tareas entre otras: 

* Crear y configurar un almacén de claves
* Crear o importar una clave
* Crear o actualizar un secreto
* Actualizar los atributos de una clave
* Obtener una clave o un secreto
* Eliminar una clave o un secreto

Estos son algunos ejemplos de cómo usar PowerShell para administrar Key Vault:  

* [Azure Key Vault - Step by Step (Almacén de claves de Azure: paso a paso)](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Setting Up and Configuring an Azure Key Vault (Instalar y configurar un Almacén de claves de Azure)](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Azure Automation y cómo se puede usar para administrar Azure Key Vault, siga estos vínculos para obtener más información acerca de Azure Automation.

* Consulte el [tutorial de introducción](../automation/automation-first-runbook-graphical.md) de Azure Automation.
* Consulte los [scripts de PowerShell de Azure Key Vault](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

