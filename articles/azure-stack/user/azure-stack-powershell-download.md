---
title: Descarga de herramientas de Azure Stack desde GitHub | Microsoft Docs
description: Aprenda a descargar las herramientas necesarias para trabajar con Azure Stack.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 69ca030d1b7601df424fa9446e1d194a3f6bd50a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Descarga de herramientas de Azure Stack desde GitHub

AzureStack-Tools es un repositorio de GitHub que hospeda módulos de PowerShell que puede usar para administrar e implementar recursos en Azure Stack. Puede descargar y usar estos módulos de PowerShell para Azure Stack Development Kit, o para un cliente externo basado en Windows, si va a establecer la conectividad VPN. Para obtener estas herramientas, clone el repositorio de GitHub o descargue la carpeta AzureStack-Tools. 

Para clonar el repositorio, descargue [Git](https://git-scm.com/download/win) para Windows, abra una ventana del símbolo del sistema y ejecute el siguiente script:

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

Para descargar la carpeta de herramientas, ejecute el script siguiente:

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

| Funcionalidad | Descripción | ¿Quién puede usar este módulo? |
| --- | --- | --- |
| [Funcionalidades en la nube](azure-stack-validate-templates.md) | Utilice este módulo para obtener las funcionalidades en la nube de una nube. Por ejemplo, con este módulo, puede obtener funcionalidades en la nube como la versión de API, los recursos de Azure Resource Manager, las extensiones de VM, etc. para las nubes de Azure Stack y Azure. | Usuarios y administradores de la nube. |
| [Directiva de Resource Manager para Azure Stack](azure-stack-policy-module.md) | Utilice este módulo para configurar una suscripción de Azure o un grupo de recursos de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack. | Usuarios y administradores de la nube |
| [Conexión a Azure Stack](azure-stack-connect-azure-stack.md) | Utilice este módulo para conectarse a una instancia de Azure Stack a través de PowerShell y configurar la conectividad VPN a Azure Stack. | Usuarios y administradores de la nube |
| [Validador de plantilla](azure-stack-validate-templates.md) | Utilice este módulo para comprobar si una plantilla nueva o existente puede implementarse en Azure Stack. | Usuarios y administradores de la nube |


## <a name="next-steps"></a>Pasos siguientes
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)   
* [Conexión a Azure Stack Development Kit a través de una VPN](azure-stack-connect-azure-stack.md)  
