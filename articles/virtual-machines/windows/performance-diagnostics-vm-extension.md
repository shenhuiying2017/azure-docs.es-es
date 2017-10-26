---
title: "Extensión de máquina virtual de diagnóstico de rendimiento de Azure para Windows | Microsoft Docs"
description: "Presenta la extensión de máquina virtual de diagnóstico de rendimiento de Azure para Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 85d4764534c77ea0e4d999e249abe456d0234d75
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensión de máquina virtual de diagnóstico de rendimiento de Azure para Windows

## <a name="summary"></a>Resumen
La extensión de máquina virtual de diagnóstico de rendimiento de Azure ayuda a recopilar los datos de diagnóstico de rendimiento desde máquinas virtuales de Windows, realiza el análisis y proporciona un informe de conclusiones y recomendaciones para identificar y resolver los problemas de rendimiento en la máquina virtual. Esta extensión instala una herramienta de solución de problemas denominada [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Requisitos previos
### <a name="operating-systems"></a>Operating Systems
Esta extensión puede instalarse en sistemas operativos Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 y Windows 10.

## <a name="extension-schema"></a>Esquema de extensión
El siguiente JSON muestra el esquema para la extensión de diagnóstico de rendimiento de Azure. Esta extensión requiere el nombre y la clave para una cuenta de almacenamiento para almacenar el resultado de diagnóstico y el informe. Estos valores son confidenciales y deben almacenarse en la configuración protegida. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino. Tenga en cuenta que storageAccountName y storageAccountKey distinguen mayúsculas de minúsculas. Aparecen otros parámetros necesarios en la sección siguiente.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Valores de propiedad

|   **Name**   |**Valor/Ejemplo**|       **Descripción**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versión de la API
|publisher|Microsoft.Azure.Performance.Diagnostics|Espacio de nombres del publicador para la extensión
|type|AzurePerformanceDiagnostics|Tipo de extensión de máquina virtual
|typeHandlerVersion|1.0|Versión del controlador de extensiones
|performanceScenario|basic|Escenario de rendimiento para capturar los datos. Los valores válidos son: **basic**, **vmslow**, **azurefiles** y **custom**.
|traceDurationInSeconds|300|Duración de los seguimientos si se ha seleccionado alguna de las opciones de seguimiento.
|DiagnosticsTrace|d|Opción para habilitar el seguimiento de diagnóstico. Los valores válidos son **d** o un valor vacío. Si no desea capturar este seguimiento, deje el valor vacío.
|perfCounterTrace|p|Opción para habilitar el seguimiento del contador de rendimiento. Los valores válidos son **p** o un valor vacío. Si no desea capturar este seguimiento, deje el valor vacío.
|networkTrace|n|Opción para habilitar el seguimiento de Netmon. Los valores válidos son **n** o un valor vacío. Si no desea capturar este seguimiento, deje el valor vacío.
|xperfTrace|x|Opción para habilitar el seguimiento de XPerf. Los valores válidos son **x** o un valor vacío. Si no desea capturar este seguimiento, deje el valor vacío.
|storPortTrace|s|Opción para habilitar el seguimiento de StorPort. Los valores válidos son s o un valor vacío. Si no desea capturar este seguimiento, deje el valor vacío.
|srNumber|123452016365929|El número de incidencia de soporte técnico si está disponible. Déjelo vacío si no dispone de él.
|requestTimeUtc|9/2/2017 11:06:00 p.m.|Hora de fecha actual en UTC. No tiene que proporcionar este valor si usa el portal para instalar esta extensión.
|storageAccountName|mystorageaccount|Nombre de la cuenta de almacenamiento para almacenar los registros de diagnóstico y resultados.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Clave para la cuenta de almacenamiento.

## <a name="install-the-extension"></a>Instalación de la extensión

Siga estos pasos para instalar la extensión de máquina virtual en máquinas virtuales de Windows:

1. Inicie sesión en [Azure Portal](http://portal.azure.com).
2. Seleccione la máquina virtual en la que desee instalar esta extensión.

    ![Seleccione la máquina virtual](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Seleccione la hoja **Extensiones** y haga clic en el botón **Agregar**.

    ![Seleccione extensiones](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Seleccione la extensión de diagnóstico de rendimiento de Azure, revise los términos y condiciones y haga clic en el botón **Crear**.

    ![Creación de la extensión de diagnóstico de rendimiento de Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Proporcione los valores de parámetros para la instalación y haga clic en **Aceptar** para instalar la extensión. Puede encontrar más información sobre los escenarios de solución de problemas compatibles [aquí](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Instalación de la extensión](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Una vez que la instalación es correcta, verá un mensaje que indica que el aprovisionamiento se ha realizado con éxito.

    ![Mensaje de aprovisionamiento correcto](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > La ejecución de la extensión se iniciará una vez que el aprovisionamiento sea correcto y tardará un par de minutos o menos en completar la ejecución de un escenario básico. Para otros escenarios, se ejecutará a través de la duración especificada durante la instalación.

## <a name="remove-the-extension"></a>Eliminación de la extensión
Para quitar la extensión desde una máquina virtual, siga estos pasos:

1. Inicie sesión en [Azure Portal](http://portal.azure.com) y seleccione la máquina virtual en la que desee quitar esta extensión y, a continuación, seleccione la hoja Extensiones. 
2. Haga clic en (**…**) para la entrada de extensión de diagnóstico de rendimiento de la lista y seleccione Desinstalar.

    ![Desinstalación de la extensión](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > También puede seleccionar la entrada de extensión y seleccionar la opción Desinstalar.

## <a name="template-deployment"></a>Implementación de plantilla
Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON detallado en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión de diagnóstico de rendimiento de Azure durante la implementación de una plantilla de Azure Resource Manager. Aquí tiene una plantilla de ejemplo que puede usarse con Template Deployment:

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Implementación de PowerShell
El comando `Set-AzureRmVMExtension` puede utilizarse para implementar la extensión de máquina virtual del diagnóstico de rendimiento de Azure en una máquina virtual existente. Antes de ejecutar el comando, las configuraciones públicas y privadas deben estar almacenadas en una tabla hash de PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario" = "basic"; "traceDurationInSeconds" = 300; "diagnosticsTrace" = "d"; "perfCounterTrace" = "p"; "networkTrace" = ""; "xperfTrace" = ""; "storPortTrace" = ""; "srNumber" = ""; "requestTimeUtc" = "2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName" = "mystorageaccount" ; "storageAccountKey" = "mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Información sobre los datos capturados
La herramienta de PerfInsights recopila distintos registros, configuraciones, datos de diagnóstico, etc. según el escenario seleccionado. Para obtener más información sobre los datos recopilados por escenario, visite la [documentación de PerfInsights](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visualización y uso compartido de resultados

El resultado de la extensión se almacena dentro de una carpeta con el nombre log_collection en la unidad temporal (normalmente, D:\log_collection) de forma predeterminada. En esta carpeta podrá ver los archivos ZIP que contienen los registros de diagnóstico y un informe con resultados y recomendaciones.

El archivo ZIP creado también se carga en la cuenta de almacenamiento proporcionada durante la instalación y se comparte durante 30 días mediante [Firmas de acceso compartido (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). También se crea un archivo de texto con el nombre *zipfilename*_saslink.txt en la carpeta log_collection. Este archivo contiene el vínculo de SAS creado para descargar el archivo ZIP. Cualquier persona que tenga este vínculo podrá descargar el archivo ZIP.

Microsoft puede usar este vínculo SAS para descargar los datos de diagnóstico para que el ingeniero de soporte técnico que trabaja en su incidencia de soporte técnico realice una investigación más exhaustiva.

Para ver el informe, extraiga el archivo ZIP y abra el archivo **PerfInsights Report.html**.

También puede descargar el archivo ZIP directamente del portal seleccionando la extensión.

![Visualización del estado detallado](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> El vínculo SAS mostrado en el portal puede no funcionar a veces debido a una dirección URL formada incorrectamente (provocada por caracteres especiales) durante la operación de codificación y descodificación. La solución alternativa es obtener el vínculo directamente desde el archivo *_saslink.txt file de la máquina virtual.

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia
### <a name="troubleshoot"></a>Solución de problemas

- El estado de la implementación de la extensión (en el área de notificación) puede mostrar "Implementación en curso" a pesar de que se haya realizado un aprovisionamiento correcto de la extensión.

    Este problema puede omitirse de forma segura siempre que el estado de la extensión indique que la extensión se ha aprovisionado correctamente.
- Se pueden solucionar algunos problemas durante la instalación con los registros de extensión. El resultado de la ejecución de las extensiones se registra en los archivos que se encuentran en el siguiente directorio:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).
