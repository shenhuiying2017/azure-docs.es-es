---
title: "Extensión de máquina virtual del agente de Azure Network Watcher para Windows | Microsoft Docs"
description: "Implemente el agente de Network Watcher en la máquina virtual Windows mediante una extensión de máquina virtual."
services: virtual-machines-windows
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 68855e0070916dc672914fbc8ca3587a5d3c25f6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensión de máquina virtual del agente de Network Watcher para Windows

## <a name="overview"></a>Información general

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) es un servicio de supervisión, diagnóstico y análisis del rendimiento de red que permite supervisar redes de Azure. La extensión de máquina virtual del agente de Network Watcher es un requisito para capturar el tráfico de red a petición y otra funcionalidad avanzada en máquinas virtuales de Azure.


En este documento se especifican las plataformas compatibles y las opciones de implementación de la extensión de máquina virtual del agente de Network Watcher para Windows.

## <a name="prerequisites"></a>requisitos previos

### <a name="operating-system"></a>Sistema operativo

La extensión del agente de Network Watcher para Windows se puede ejecutar en las versiones Windows Server 2008 R2, 2012, 2012 R2 y 2016. Nano Server no se admite.

### <a name="internet-connectivity"></a>Conectividad de Internet

Parte de la funcionalidad del agente de Network Watcher requiere que la máquina virtual de destino esté conectada a Internet. Si no se pueden establecer conexiones salientes, el agente de Network Watcher no podrá cargar capturas de paquetes en la cuenta de almacenamiento. Para más detalles, consulte la [documentación de Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema para la extensión del agente de Network Watcher. La extensión no requiere ni admite ninguna configuración proporcionada por el usuario y se basa en su propia configuración predeterminada.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valores de propiedad

| NOMBRE | Valor / ejemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| Tipo | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implementación de plantilla

Puede implementar las extensiones de VM de Azure con plantillas de Azure Resource Manager. Puede usar el esquema JSON detallado en la sección anterior en una plantilla de Azure Resource Manager para ejecutar la extensión del agente de Network Watcher durante la implementación de dicha plantilla.

## <a name="powershell-deployment"></a>Implementación de PowerShell

Use el comando `Set-AzureRmVMExtension` para implementar la extensión de máquina virtual del agente de Network Watcher en una máquina virtual existente:

```powershell
Set-AzureRmVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Solución de problemas y soporte técnico

### <a name="troubleshooting"></a>solución de problemas

Puede recuperar los datos sobre el estado de las implementaciones de extensiones desde Azure Portal y mediante PowerShell. Para ver el estado de implementación de las extensiones de una VM determinada, ejecute el comando siguiente con el módulo de Azure PowerShell:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

El resultado de la ejecución de las extensiones se registra en los archivos que se encuentran en el siguiente directorio:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede consultar la documentación de la guía del usuario de Network Watcher o ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/en-us/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
