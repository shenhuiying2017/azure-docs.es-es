<properties
   pageTitle="Uso de la configuración de estado deseado con conjuntos de escalado de máquinas virtuales | Microsoft Azure"
   description="Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="zachal"/>

# Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure

Los [conjuntos de escalado de máquinas virtuales (VMSS)](virtual-machines-windows-vmss-powershell-creating.md) pueden utilizarse con el controlador de extensiones de [configuración de estado deseado (DSC) de Azure](virtual-machines-windows-extensions-dsc-overview.md). Los VMSS se emplean para configurar la implementación de máquinas virtuales con el objetivo de cumplir los requisitos de carga. DSC se utiliza para configurar las máquinas virtuales a medida que se conecten, ya que ejecutan el software de producción.

## Diferencias entre implementar máquinas virtuales y VMSS

La estructura subyacente de la plantilla de los VMSS es ligeramente distinta a la de una sola máquina virtual. En concreto, una sola máquina virtual implementa extensiones en el nodo virtualMachines. Hay una entrada del tipo extensions, donde se agrega DSC a la plantilla.

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
                          "script": "dscExtension.ps1",
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

Un nodo de VMSS tiene una sección properties con el atributo extensionProfile de VirtualMachineProfile. DSC se agrega en "extensions".

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.9",
                        "autoUpgradeMinorVersion": true,
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

## Comportamiento de los VMSS

El comportamiento de los VMSS es idéntico al de una sola máquina virtual. Cuando se crea una nueva máquina virtual, se aprovisiona automáticamente con la extensión de DSC. Si la extensión requiere una versión más reciente de WMF, se reinicia la máquina virtual antes de conectarse. Cuando se conecta, descarga el archivo .zip de configuración de DSC y lo aprovisiona en la máquina virtual. Pueden encontrar más información en el artículo de [introducción a la extensión de DSC de Azure](virtual-machines-windows-extensions-dsc-overview.md).

<!---HONumber=AcomDC_0914_2016-->