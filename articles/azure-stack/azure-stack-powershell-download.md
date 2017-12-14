---
title: Descarga de herramientas de Azure Stack desde GitHub | Microsoft Docs
description: "Obtenga información sobre cómo descargar las herramientas necesarias para trabajar con Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E4DF77FA-F468-42B5-B44F-F10ED8049171
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: e730df58c54f7804d77b052a699d827bb82e5204
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Descarga de herramientas de Azure Stack desde GitHub

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

**AzureStack-Tools** es un repositorio de GitHub que hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack. Si planea establecer conectividad VPN, puede descargar estos módulos de PowerShell en Azure Stack Development Kit o en un cliente externo basado en Windows. Para obtener estas herramientas, clone el repositorio de GitHub o descargue la carpeta **AzureStack-Tools** mediante la ejecución del script siguiente:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funcionalidad proporcionada por los módulos

El repositorio **AzureStack-Tools** contiene los módulos de PowerShell que admiten las funcionalidades siguientes para Azure Stack:  

| Funcionalidad | Descripción | ¿Quién puede usar este módulo? |
| --- | --- | --- |
| [Funcionalidades en la nube](user/azure-stack-validate-templates.md) | Utilice este módulo para obtener las funcionalidades en la nube de una nube. Por ejemplo, con este módulo puede obtener las funcionalidades en la nube como la versión de API y los recursos de Azure Resource Manager. También puede obtener las extensiones de VM para Azure Stack y las nubes de Azure con este módulo. | Operadores de nube y usuarios |
| [Administración de procesos de Azure Stack](azure-stack-add-vm-image.md) | Utilice este módulo para agregar o quitar una imagen de máquina virtual desde el marketplace de Azure Stack. | Operadores de nube |
| [Administración de la infraestructura de Azure Stack](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | Use este módulo para administrar las actualizaciones, las alertas, las máquinas virtuales y mucho más de la infraestructura de Azure Stack. |  Operadores de nube|
| [Directiva de Resource Manager para Azure Stack](user/azure-stack-policy-module.md) | Utilice este módulo para configurar una suscripción de Azure o un grupo de recursos de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack. | Operadores de nube y usuarios |
| [Registro con Azure](azure-stack-register.md) | Utilice este módulo para registrar la instancia de Azure Stack Development Kit. Después del registro, puede descargar los elementos de marketplace de Azure y usarlos en Azure Stack. | Operadores de nube |
| [Implementación de Azure Stack](azure-stack-run-powershell-script.md) | Use este módulo para preparar el equipo host de Azure Stack para implementar y volver a implementar con la imagen de disco duro virtual (VHD) de Azure Stack. | Operadores de nube|
| [Conexión a Azure Stack](azure-stack-connect-powershell.md) | Utilice este módulo para conectarse a una instancia de Azure Stack a través de PowerShell y configurar la conectividad VPN a Azure Stack. | Operadores de nube y usuarios |
| [Administración de servicios de Azure Stack](azure-stack-create-offer.md) | Use este módulo para crear una oferta de inquilino predeterminada con una cuota ilimitada a través de los servicios de Key Vault, red, Azure Storage y proceso.   | Operadores de nube|
| [Validador de plantilla](user/azure-stack-validate-templates.md) | Utilice este módulo para comprobar si una plantilla nueva o existente puede implementarse en Azure Stack. | Operadores de nube y usuarios|


## <a name="next-steps"></a>Pasos siguientes
* [Configuración del entorno de PowerShell del usuario de Azure Stack](user/azure-stack-powershell-configure-user.md)   
* [Conexión a Azure Stack Development Kit a través de una VPN](azure-stack-connect-azure-stack.md)  
