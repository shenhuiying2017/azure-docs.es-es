---
title: "Exportación de grupos de recursos de Azure que contienen extensiones de VM | Microsoft Docs"
description: "Exportar plantillas de Resource Manager que incluyen extensiones de máquina virtual."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: danis
ms.openlocfilehash: 739ae7995fca41fe8abb7cd54ccb72ff3bc43854
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportación de grupos de recursos que contienen extensiones de VM

Los grupos de recursos de Azure se pueden exportar a una nueva plantilla de Resource Manager que, después, se puede volver a implementar. El proceso de exportación interpreta los recursos existentes y crea una plantilla de Resource Manager que, cuando se implementa, da como resultado un grupo de recursos similar. Cuando se usa la opción de exportación del grupo de recursos en un grupo de recursos que contiene extensiones de máquina virtual, se deben tener en cuenta varios elementos, como la compatibilidad de extensión y la configuración protegida.

En este documento se detalla cómo funciona el proceso de exportación del grupo de recursos con respecto a las extensiones de máquina virtual, incluida una lista de extensiones admitidas y detalles sobre el control de los datos seguros.

## <a name="supported-virtual-machine-extensions"></a>Extensiones de máquina virtual admitidas

Hay muchas extensiones de máquina virtual disponibles. No todas las extensiones se pueden exportar a una plantilla de Resource Manager mediante la característica de “Script de Automation”. Si no se admite una extensión de máquina virtual, debe volverse a colocar manualmente en la plantilla exportada.

Las siguientes extensiones se pueden exportar con la característica de script de automatización.

| Extensión ||||
|---|---|---|---|
| Acronis Backup | Datadog Windows Agent | OS Patching For Linux | VM Snapshot Linux
| Acronis Backup Linux | Extensión de Docker | Puppet Agent |
| Bg Info | Extensión DSC | Site 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Site 24x7 Linux Server |
| BMC CTM Agent Windows | Dynatrace Windows | Site 24x7 Windows Server |
| Chef Client | HPE Security Application Defender | Trend Micro DSA |
| Script personalizado | IaaS Antimalware | Trend Micro DSA Linux |
| Extensión Custom Script | IaaS Diagnostics | VM Access For Linux |
| Script personalizado para Linux | Linux Chef Client | VM Access For Linux |
| Datadog Linux Agent | Linux Diagnostic | VM Snapshot |

## <a name="export-the-resource-group"></a>Exportación del grupo de recursos

Para exportar un grupo de recursos a una plantilla reutilizable, complete los pasos siguientes:

1. Inicie sesión en el Portal de Azure.
2. En el menú Concentrador, haga clic en Grupos de recursos.
3. Seleccione el grupo de recursos de destino de la lista.
4. En la hoja Grupo de recursos, haga clic en Script de automatización.

![Exportación de plantilla](./media/extensions-export-templates/template-export.png)

El script de automatización de Azure Resource Manager genera una plantilla de Resource Manager, un archivo de parámetros y varios scripts de implementación de ejemplo, como PowerShell y la CLI de Azure. En este momento, la plantilla exportada se puede descargar utilizando el botón de descarga, agregar como una nueva plantilla a la biblioteca de plantillas o volver a implementar mediante el botón de implementación.

## <a name="configure-protected-settings"></a>Definición de la configuración protegida

Muchas extensiones de máquina virtual de Azure incluyen una configuración protegida, que cifra los datos confidenciales, como credenciales y cadenas de configuración. La configuración protegida no se exporta con el script de automatización. Si procede, la configuración protegida necesita volverse a insertar en la plantilla exportada.

### <a name="step-1---remove-template-parameter"></a>Paso 1: Quitar parámetro de plantilla

Cuando el grupo de recursos se exporta, se crea un parámetro de plantilla único para proporcionar un valor para la configuración protegida exportada. Este parámetro se puede quitar. Para quitar el parámetro, examine la lista de parámetros y elimine el parámetro que tiene un aspecto similar a este ejemplo JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Paso 2: Obtener propiedades de configuración protegida

Dado que cada configuración protegida tiene un conjunto de propiedades obligatorias, es necesario recopilar una lista de estas propiedades. Cada parámetro de la configuración protegida se encuentra en el [esquema de Azure Resource Manager en GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Este esquema solo incluye los conjuntos de parámetros para las extensiones que aparecen en la sección de información general de este documento. 

Desde dentro del repositorio de esquema, busque la extensión que desee, para este ejemplo `IaaSDiagnostics`. Una vez que el objeto `protectedSettings` de las extensiones se ha localizado, tome nota de cada parámetro. En el ejemplo de la extensión `IaasDiagnostic`, los parámetros requeridos son `storageAccountName`, `storageAccountKey` y `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Paso 3: Volver a crear la configuración protegida

En la plantilla exportada, busque `protectedSettings` y reemplace el objeto de configuración protegida exportado por uno nuevo que incluya los parámetros de extensión necesarios y un valor para cada uno de ellos.

En el ejemplo de la extensión `IaasDiagnostic`, la nueva configuración protegida sería similar al ejemplo siguiente:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

El recurso de extensión final tiene un aspecto similar al siguiente ejemplo JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Si utiliza parámetros de plantilla para proporcionar valores de propiedad, estos deben crearse. Al crear parámetros de plantilla para los valores de configuración protegida, asegúrese de usar el tipo de parámetro `SecureString` para que se protejan los valores confidenciales. Para más información sobre el uso de parámetros, consulte [Authoring Azure Resource Manager templates](../../resource-group-authoring-templates.md) (Creación de plantillas de Azure Resource Manager).

En el ejemplo de la extensión `IaasDiagnostic`, se crearán los siguientes parámetros en la sección de parámetros de la plantilla de Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

En este momento, la plantilla se puede implementar mediante cualquier método de implementación de plantilla.
