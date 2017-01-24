---
title: "Extensión de máquina virtual de Azure y OMS para Windows | Microsoft Docs"
description: "Implemente el agente de OMS en la máquina virtual Windows con una extensión de máquina virtual."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: d65505182267bec053fca0ab5045cbcae9381ac7
ms.openlocfilehash: 245e49d36142ef8e927a5e494113e4dd7809fc16


---
# <a name="oms-virtual-machine-extension-for-windows"></a>Extensión de máquina virtual de OMS para Windows

## <a name="overview"></a>Información general

Operations Management Suite (OMS) proporciona funcionalidades de corrección de alertas, supervisión y envío de alertas a todos los recursos locales y en la nube. Microsoft, como editor de la extensión de máquina virtual del agente de OMS para Windows, es quien presta los servicios de soporte técnico para esta solución. La extensión instala al agente de OMS en máquinas virtuales de Azure e inscribe dichas máquinas virtuales en un área de trabajo de OMS existente. En este documento se especifican las plataformas compatibles, configuraciones y opciones de implementación de la extensión de máquina virtual de OMS para Windows.

Para información general sobre las extensiones de máquina virtual de Azure, consulte [Información general de las extensiones de máquina virtual](./virtual-machines-windows-extensions-features.md).

Para más información sobre Operations Management Suite, consulte [Información general de Operations Management Suite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## <a name="prerequisites"></a>Requisitos previos

### <a name="operating-system"></a>Sistema operativo

La extensión del agente de OMS para Windows se puede ejecutar en las versiones 2012, 2012 R2 y 2016 de Windows Server.

### <a name="connectivity"></a>Conectividad

La extensión del agente de OMS para Windows requiere que la máquina virtual de destino esté conectada a Internet. 

## <a name="extension-configuration"></a>Configuración de la extensión

La extensión de máquina virtual del agente de OMS para Windows requiere el identificador y la clave del área de trabajo de OMS de destino. Como la clave del área de trabajo debe tratarse como datos confidenciales, se almacena en una configuración protegida. Los datos de configuración protegida de la extensión de VM de Azure se cifran (y solo se descifran en la máquina virtual de destino). Las configuraciones públicas y privadas se especifican durante la implementación, que se describe detalladamente en las secciones posteriores de este documento.

### <a name="public-configuration"></a>Configuración pública

Esquema de la configuración pública:

- workspaceId (obligatorio; cadena); identificador del área de trabajo de OMS que se incorporará en la máquina virtual.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>Configuración privada

Esquema de la configuración pública:

- workspaceKey (obligatorio; cadena): clave compartida principal y secundaria del área de trabajo.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. Las plantillas resultan ideales al implementar una o varias máquinas virtuales que requieren configurarse tras la implementación, por ejemplo, para incorporarse a OMS. Puede encontrar una plantilla de Resource Manager de ejemplo que incluye la extensión de VM del agente de OMS en la [Galería de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

Este ejemplo puede implementarse a partir de este documento usando este botón:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-windows-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

El JSON que se usa para implementar la extensión de VM del agente de OMS es parecido al siguiente ejemplo de JSON:

```json
{
    "type": "extensions",
    "name": "Microsoft.EnterpriseCloud.Monitoring",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceKey"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceId"
        }
    }
}
```

## <a name="powershell-deployment"></a>Implementación de PowerShell

El comando `Set-AzureRmVMExtension` puede utilizarse para implementar la extensión de máquina virtual del agente de OMS en una máquina virtual existente. Antes de ejecutar el comando, las configuraciones públicas y privadas deben estar almacenadas en una tabla hash de PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>Solución de problemas y soporte técnico

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante la CLI de Azure. Para ver el estado de implementación de las extensiones de una VM determinada, ejecute el comando siguiente con la CLI de Azure.

```azurecli
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

El resultado de la ejecución de las extensiones se registra en los archivos que se encuentran en el siguiente directorio:

`
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
`

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/en-us/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).



<!--HONumber=Dec16_HO1-->


