---
title: Usar una herramienta de validación de plantillas para comprobar plantillas de Azure Stack | Microsoft Docs
description: Comprobación de plantillas para la implementación en Azure Stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 88fac41ce2c9fa0c5569beae02ab90a507c89a34
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358649"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Comprobar las plantillas de Azure Stack con la herramienta de validación de plantillas

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la herramienta de validación de plantillas para comprobar si las [plantillas](azure-stack-arm-templates.md) de Azure Resource Manager están listas para la implementación en Azure Stack. La herramienta de validación de plantillas está disponible como parte de las herramientas de Azure Stack. Descargue las herramientas de Azure Stack mediante los pasos descritos en el artículo [Descargar herramientas de GitHub](azure-stack-powershell-download.md).

## <a name="overview"></a>Información general

Para validar una plantilla, primero tiene que crear un archivo con funcionalidades de la nube y, a continuación, ejecutar la herramienta de validación. Use los siguientes módulos de PowerShell desde las herramientas de Azure Stack:

- En la carpeta **TemplateValidator**:<br>         AzureRM.CloudCapabilities.psm1 crea un archivo JSON de funcionalidades de la nube que representa los servicios y las versiones en una nube de Azure Stack.
- En la carpeta **CloudCapabilities**:<br>
AzureRM.TemplateValidator.psm1 utiliza un archivo JSON de funcionalidades de la nube para probar plantillas para la implementación en Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Genere el archivo de funcionalidades de la nube

Para poder usar el validador de plantillas, ejecute el módulo de AzureRM.CloudCapabilities PowerShell para generar un archivo JSON.

>[!NOTE]
>Si actualiza el sistema integrado o agrega los nuevos servicios o extensiones virtuales, también debe volver a ejecutar ese módulo.

1. Asegúrese de que dispone de conectividad a Azure Stack. Estos pasos se pueden realizar desde el host del Kit de desarrollo de Azure Stack, o puede usar una [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para conectarse desde su estación de trabajo.
2. Importe el módulo de PowerShell AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Use el cmdlet Get-CloudCapabilities para recuperar las versiones de servicio y crear un archivo JSON de funcionalidades de la nube. Si no se especifica **-OutputPath**, se crea el archivo AzureCloudCapabilities.Json en el directorio actual. Utilice la ubicación real:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Validar plantillas

Siga estos pasos para validar las plantillas mediante el módulo de PowerShell AzureRM.TemplateValidator. Puede usar sus propias plantillas o validar las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importe el módulo de PowerShell AzureRM.TemplateValidator.psm1:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Ejecute el validador de plantillas:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Los errores o advertencias de validación de plantillas se registran en la consola de PowerShell y en un archivo HTML en el directorio de origen. La captura de pantalla siguiente muestra un ejemplo de un informe de comprobación:

![Informe de comprobación de plantilla](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parámetros

El validador de plantillas admite los siguientes parámetros.

| . | DESCRIPCIÓN | Obligatorio |
| ----- | -----| ----- |
| TemplatePath | Especifica la ruta de acceso para buscar de forma recursiva las plantillas de Azure Resource Manager | Sí | 
| TemplatePattern | Especifica el nombre de los archivos de plantilla para que coincida. | Sin  |
| CapabilitiesPath | Especifica la ruta de acceso al archivo JSON de funcionalidades de la nube | Sí | 
| IncludeComputeCapabilities | Incluye la evaluación de recursos de IaaS como tamaños de máquina virtual y extensiones de máquina virtual | Sin  |
| IncludeStorageCapabilities | Incluye la evaluación de recursos de almacenamiento como los tipos SKU | Sin  |
| Informe | Especifica el nombre del informe HTML generado | Sin  |
| Detallado | Registra los errores y advertencias en la consola | Sin |

### <a name="examples"></a>Ejemplos

En este ejemplo, se validan todas las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) descargadas en el almacenamiento local. Además, en el ejemplo validan las extensiones y los tamaños de máquina virtual frente a las funcionalidades del Kit de desarrollo de Azure Stack.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>Pasos siguientes

- [Implementar plantillas en Azure Stack](azure-stack-arm-templates.md)
- [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)
