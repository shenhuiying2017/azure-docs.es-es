---
title: Descarga de herramientas de Azure Stack desde GitHub | Microsoft Docs
description: Aprenda a descargar las herramientas necesarias para trabajar con Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: a5bc23ee6f986da80630371bafcd8ec80dde3577
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258885"
---
# <a name="download-azure-stack-tools-from-github"></a>Descarga de herramientas de Azure Stack desde GitHub

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

AzureStack-Tools es un repositorio de GitHub que hospeda módulos de PowerShell que puede usar para administrar e implementar recursos en Azure Stack.

## <a name="download-targets"></a>Descarga de destinos

Puede descargar y usar estos módulos de PowerShell para el Kit de desarrollo de Azure Stack, o para un cliente externo basado en Windows que use una conexión VPN.

## <a name="how-to-get-the-tools"></a>Cómo obtener las herramientas

Para obtener estas herramientas, clone el repositorio de GitHub para AzureStack-Tools o descargue la carpeta AzureStack-Tools mediante la ejecución del script siguiente:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funcionalidades proporcionadas por los módulos

El repositorio AzureStack-Tools contiene los módulos de PowerShell que admiten las siguientes funcionalidades para Azure Stack:

| Funcionalidad | DESCRIPCIÓN | ¿Quién puede usar este módulo? |
| --- | --- | --- |
| [Funcionalidades en la nube](azure-stack-validate-templates.md) | Utilice este módulo para obtener las funcionalidades en la nube de una nube. Por ejemplo, con este módulo, puede obtener funcionalidades en la nube como la versión de API, los recursos de Azure Resource Manager, las extensiones de VM, etc. para las nubes de Azure Stack y Azure. | Usuarios y administradores de la nube. |
| [Directiva de Resource Manager para Azure Stack](azure-stack-policy-module.md) | Utilice este módulo para configurar una suscripción de Azure o un grupo de recursos de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack. | Usuarios y administradores de la nube |
| [Conexión a Azure Stack](azure-stack-connect-azure-stack.md) | Utilice este módulo para conectarse a una instancia de Azure Stack a través de PowerShell y configurar la conectividad VPN a Azure Stack. | Usuarios y administradores de la nube |
| [Validador de plantilla](azure-stack-validate-templates.md) | Utilice este módulo para comprobar si una plantilla nueva o existente puede implementarse en Azure Stack. | Usuarios y administradores de la nube |

## <a name="next-steps"></a>Pasos siguientes

* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)
* [Conexión al Kit de desarrollo de Azure Stack a través de una VPN](azure-stack-connect-azure-stack.md)
