---
title: Extensión de Azure Diagnostics para Windows | Microsoft Docs
description: Supervisión de máquinas virtuales mediante la extensión de Azure Diagnostics para Windows
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: a9621f6f93d8e14e53cfe05ca4a714e88b9d8572
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928698"
---
# <a name="windows-azure-diagnostics-extension"></a>Extensión de Microsoft Azure Diagnostics

## <a name="overview"></a>Información general

La extensión de máquinas virtuales de Azure Diagnostics para Windows le permite recopilar datos de supervisión como, por ejemplo, contadores de rendimiento y registros de eventos, de la máquina virtual Windows. Puede especificar de forma granular qué datos desea recopilar y dónde desea que estos vayan como, por ejemplo, a una cuenta de Azure Storage o a una instancia de Azure Event Hubs. También puede usar estos datos para generar gráficos en Azure Portal o crear alertas de métricas.

## <a name="prerequisites"></a>requisitos previos

### <a name="operating-system"></a>Sistema operativo

La extensión de Azure Diagnostics para Windows se puede ejecutar en el cliente de Windows 10, Windows Server 2008 R2, 2012, 2012 R2 y 2016.

### <a name="internet-connectivity"></a>Conectividad de Internet

La extensión de Azure Diagnostics para Windows requiere que la máquina virtual de destino esté conectada a Internet. 

## <a name="extension-schema"></a>Esquema de extensión

[En este documento se describe el esquema y los valores de propiedad de la extensión de Azure Diagnostics para Windows.](../../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md)

## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON detallado en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión de Azure Diagnostics para Windows durante la implementación de una plantilla de Azure Resource Manager. Consulte [Uso de la supervisión y el diagnóstico con una máquina virtual Windows y plantillas de Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implementación de la CLI de Azure

La CLI de Azure puede utilizarse para implementar la extensión de Azure Diagnostics para Windows en una máquina virtual existente. Reemplace los valores protegidos y las propiedades de configuración por un JSON válido desde el esquema de extensión anterior. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Implementación de PowerShell

Se puede utilizar el comando `Set-AzureRmVMDiagnosticsExtension` para agregar la extensión de Azure Diagnostics para Windows a una máquina virtual existente. Consulte también [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante la CLI de Azure. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente con la CLI de Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Consulte este artículo](../../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) para obtener una guía de solución de problemas más completa de la extensión de Azure Diagnostics para Windows.

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Pasos siguientes
* [Más información acerca de la extensión de Azure Diagnostics para Windows](../../monitoring-and-diagnostics/azure-diagnostics.md)
* [Revise las versiones y el esquema de la extensión](../../monitoring-and-diagnostics/azure-diagnostics-schema.md)
