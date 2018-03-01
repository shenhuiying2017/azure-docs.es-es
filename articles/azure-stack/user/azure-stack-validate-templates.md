---
title: Uso del validador de plantilla para buscar plantillas para Azure Stack | Microsoft Docs
description: "Comprobación de plantillas para la implementación en Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6a77efb3ef4236048ff08b14346175b592493982
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Compruebe las plantillas para Azure Stack con el validador de plantilla

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la herramienta de validación de plantillas para comprobar si las [plantillas](azure-stack-arm-templates.md) de Azure Resource Manager están listas para Azure Stack. La herramienta de validación de plantillas está disponible como parte de las herramientas de Azure Stack. Descargue las herramientas de Azure Stack mediante los pasos descritos en el artículo [Descargar herramientas de GitHub](azure-stack-powershell-download.md). 

Para validar plantillas, use los siguientes módulos de PowerShell que se encuentran en las carpetas **TemplateValidator** y **CloudCapabilities**: 

 - AzureRM.CloudCapabilities.psm1 crea un archivo JSON de funcionalidades de la nube que representa los servicios y las versiones en una nube como Azure Stack.
 - AzureRM.TemplateValidator.psm1 utiliza un archivo JSON de funcionalidades de la nube para probar plantillas para la implementación en Azure Stack.
 
En este artículo, generará un archivo de funcionalidades de la nube y, a continuación, ejecutará la herramienta de validación.

## <a name="build-cloud-capabilities-file"></a>Genere el archivo de funcionalidades de la nube
Para poder usar el validador de plantillas, ejecute el módulo de AzureRM.CloudCapabilities PowerShell para generar un archivo JSON. Si actualiza el sistema integrado o agrega los nuevos servicios o extensiones de VM, también debe volver a ejecutar ese módulo.

1.  Asegúrese de que dispone de conectividad a Azure Stack. Estos pasos se pueden realizar desde el host del Kit de desarrollo de Azure Stack, o puede usar una [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para conectarse desde su estación de trabajo. 
2.  Importe el módulo de PowerShell AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Use el cmdlet Get-CloudCapabilities para recuperar las versiones de servicio y crear un archivo JSON de funcionalidades de la nube. Si no se especifica -OutputPath, se crea el archivo AzureCloudCapabilities.Json en el directorio actual. Utilice la ubicación real:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```             

## <a name="validate-templates"></a>Validar plantillas
En estos pasos, valide las plantillas mediante el módulo de PowerShell AzureRM.TemplateValidator. Puede usar sus propias plantillas o validar las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Importe el módulo de PowerShell AzureRM.TemplateValidator.psm1:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Ejecute el validador de plantillas:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Cualquier error o advertencia de validación de plantillas se registra en la consola de PowerShell y en un archivo HTML en el directorio de origen. A continuación, se muestra un ejemplo del informe de validación:

![informe de validación de ejemplo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parámetros

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
En este ejemplo se validan todas las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) descargadas de forma local y también las extensiones y los tamaños de VM con las funcionalidades del Kit de desarrollo de Azure Stack.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```


## <a name="next-steps"></a>pasos siguientes
 - [Implementar plantillas en Azure Stack](azure-stack-arm-templates.md)
 - [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)

