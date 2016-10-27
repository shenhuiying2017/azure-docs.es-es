<properties
   pageTitle="Uso de la configuración de estado deseado con conjuntos de escalado de máquinas virtuales | Microsoft Azure"
   description="Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>


# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Uso de conjuntos de escalado de máquinas virtuales con la extensión de DSC de Azure

Los [conjuntos de escalado de máquinas virtuales (VMSS)](virtual-machine-scale-sets-overview.md) pueden usarse con el controlador de extensiones de [configuración de estado deseado (DSC) de Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). VMSS proporciona una manera de implementar y administrar un gran número de máquinas virtuales, y se puede escalar y reducir horizontalmente en respuesta a la carga. DSC se utiliza para configurar las máquinas virtuales a medida que se conecten, ya que ejecutan el software de producción.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Diferencias entre implementar máquinas virtuales y VMSS

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

Un nodo de VMSS tiene una sección properties con el atributo extensionProfile de VirtualMachineProfile. DSC se agrega en "extensions".

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

## <a name="behavior-for-vmss"></a>Comportamiento de los VMSS

El comportamiento de los VMSS es idéntico al de una sola máquina virtual. Cuando se crea una nueva máquina virtual, se aprovisiona automáticamente con la extensión de DSC. Si la extensión requiere una versión más reciente de WMF, se reinicia la máquina virtual antes de conectarse. Cuando se conecta, descarga el archivo .zip de configuración de DSC y lo aprovisiona en la máquina virtual. Pueden encontrar más información en el artículo de [introducción a la extensión de DSC de Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Pasos siguientes ##
Examine la [plantilla de Azure Resource Manager para la extensión de DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Obtenga información sobre cómo la [extensión de DSC administra de forma segura las credenciales](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Para más información sobre el controlador de extensiones DSC de Azure, consulte [Introducción al controlador de extensiones de configuración de estado deseado de Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Para más información sobre DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 





<!--HONumber=Oct16_HO2-->


