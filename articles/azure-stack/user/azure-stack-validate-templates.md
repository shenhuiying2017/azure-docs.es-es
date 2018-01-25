---
title: Uso del validador de plantilla para buscar plantillas para Azure Stack | Microsoft Docs
description: "Comprobación de plantillas para la implementación en Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c30b0a78cf3421554cf8f7c887c7973c7b9f4b9c
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Compruebe las plantillas para Azure Stack con el validador de plantilla

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Puede usar la herramienta de validación de plantillas para comprobar si las [plantillas](azure-stack-arm-templates.md) de Azure Resource Manager están listas para Azure Stack. La herramienta de validación de plantillas está disponible como parte de las herramientas de Azure Stack. Descargue las herramientas de Azure Stack mediante los pasos descritos en el artículo [Descargar herramientas de GitHub](azure-stack-powershell-download.md). 

Para validar plantillas, use los siguientes módulos de PowerShell y el archivo JSON que se encuentra en las carpetas **TemplateValidator** y **CloudCapabilities**: 

 - AzureRM.CloudCapabilities.psm1 crea un archivo JSON de funcionalidades de la nube que representa los servicios y las versiones en una nube como Azure Stack.
 - AzureRM.TemplateValidator.psm1 utiliza un archivo JSON de funcionalidades de la nube para probar plantillas para la implementación en Azure Stack.
 - AzureStackCloudCapabilities_with_AddOns_20170627.json es un archivo predeterminado de funcionalidades de la nube.  Puede crear el suyo propio o utilizar este archivo para empezar a trabajar. 

En este tema, ejecute la validación en las plantillas y, opcionalmente, genere un archivo de funcionalidades de la nube.

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

Cualquier error o advertencia de validación de plantillas se registra en la consola de PowerShell y también en un archivo HTML en el directorio de origen. Un ejemplo de la salida del informe de validación tiene el siguiente aspecto:

![informe de validación de ejemplo](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parámetros

| . | DESCRIPCIÓN | Obligatorio |
| ----- | -----| ----- |
| TemplatePath | Especifica la ruta de acceso para buscar de forma recursiva las plantillas del Resource Manager | Sí | 
| TemplatePattern | Especifica el nombre de los archivos de plantilla para que coincida. | Sin  |
| CapabilitiesPath | Especifica la ruta de acceso al archivo JSON de funcionalidades de la nube | Sí | 
| IncludeComputeCapabilities | Incluye la evaluación de recursos de IaaS como tamaños de máquina virtual y extensiones de máquina virtual | Sin  |
| IncludeStorageCapabilities | Incluye la evaluación de recursos de almacenamiento como los tipos SKU | Sin  |
| Informe | Especifica el nombre del informe HTML generado | Sin  |
| Detallado | Registra los errores y advertencias en la consola | Sin |


### <a name="examples"></a>Ejemplos
En este ejemplo se validan todas las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) descargadas de forma local y también las extensiones y los tamaños de VM con las funcionalidades de Azure Stack Development Kit.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Genere el archivo de funcionalidades de la nube
Los archivos descargados incluyen un archivo predeterminado *AzureStackCloudCapabilities_with_AddOns_20170627.json*, que describe las versiones de servicio disponibles en Azure Stack Development Kit con los servicios de PaaS instalados.  Si instala proveedores de recursos adicionales, puede usar el módulo de PowerShell AzureRM.CloudCapabilities para generar un archivo JSON que incluya servicios nuevos.  

1.  Asegúrese de que dispone de conectividad a Azure Stack.  Estos pasos se pueden realizar desde el host de Azure Stack Development Kit, o puede usar una [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para conectarse desde su estación de trabajo. 
2.  Importe el módulo de PowerShell AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Use el cmdlet Get-CloudCapabilities para recuperar las versiones de servicio y crear un archivo JSON de funcionalidades de la nube:

    ```PowerShell
    Get-AzureRMCloudCapability -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>pasos siguientes
 - [Implementar plantillas en Azure Stack](azure-stack-arm-templates.md)
 - [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)

