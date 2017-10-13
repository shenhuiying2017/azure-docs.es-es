---
title: "Uso de la configuración de estado deseado con conjuntos de escalado de máquinas virtuales | Microsoft Docs"
description: "Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: b61b0acf3072569ab733a13defb465c921d26187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure
Los [conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md) pueden usarse con el controlador de extensiones de [Configuración de estado deseado (DSC) de Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Los conjuntos de escalado de máquinas virtuales proporcionan una manera de implementar y administrar un gran número de máquinas virtuales, y se pueden escalar y reducir horizontalmente en respuesta a la carga. DSC se utiliza para configurar las máquinas virtuales a medida que se conecten, ya que ejecutan el software de producción.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferencias entre implementar máquinas virtuales y conjuntos de escalado de máquinas virtuales
La estructura subyacente de la plantilla para un conjunto de escalado de máquinas virtuales es ligeramente distinta a la de una sola máquina virtual. En concreto, una sola máquina virtual implementa extensiones en el nodo virtualMachines. Hay una entrada del tipo extensions, donde se agrega DSC a la plantilla.

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Un nodo del conjunto de escalado de máquinas virtuales tiene una sección "properties" con el atributo "extensionProfile" de "VirtualMachineProfile". DSC se agrega en "extensions".

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Comportamiento de un conjunto de escalado de máquinas virtuales
El comportamiento del conjunto de escalado de máquinas virtuales es idéntico al de una sola máquina virtual. Cuando se crea una nueva máquina virtual, se aprovisiona automáticamente con la extensión de DSC. Si la extensión requiere una versión más reciente de WMF, se reinicia la máquina virtual antes de conectarse. Cuando se conecta, descarga el archivo .zip de configuración de DSC y lo aprovisiona en la máquina virtual. Pueden encontrar más información en el artículo de [introducción a la extensión de DSC de Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes
Examine la [plantilla de Azure Resource Manager para la extensión de DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Obtenga información sobre cómo la [extensión de DSC administra de forma segura las credenciales](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para más información sobre el controlador de extensiones DSC de Azure, consulte [Introducción al controlador de extensiones de configuración de estado deseado de Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para más información sobre DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

