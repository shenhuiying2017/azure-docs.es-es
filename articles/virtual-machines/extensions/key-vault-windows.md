---
title: Extensión de máquina virtual de Key Vault para Windows | Microsoft Docs
description: Implemente un agente que realice la actualización automática de secretos de Key Vault en máquinas virtuales mediante una extensión de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944757"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensión de máquina virtual de Key Vault para Windows

## <a name="overview"></a>Información general

La extensión de máquina virtual de Key Vault proporciona la actualización automática de secretos almacenados en un almacén de claves de Azure. En concreto, la extensión supervisa una lista de certificados observados almacenados en almacenes de claves y, cuando detecta un cambio, recupera e instala los certificados correspondientes. Microsoft publica la extensión de máquina virtual de Key Vault y es compatible con esta, actualmente en máquinas virtuales de Windows. En este documento se especifican las plataformas compatibles, configuraciones y opciones de implementación de la extensión de máquina virtual de Key Vault para Windows. 

## <a name="prerequisites"></a>requisitos previos

La extensión de máquina virtual de Key Vault depende de la extensión de máquina virtual de Managed Service Identity para autenticarse en el servicio de Key Vault. Consulte la documentación de la extensión de máquina virtual de MSI para obtener más información.

### <a name="operating-system"></a>Sistema operativo

La extensión de máquina virtual de Key Vault admite actualmente todas las versiones de Windows.

| Distribución | Versión |
|---|---|
| Windows | Núcleo |

### <a name="internet-connectivity"></a>Conectividad de Internet

La extensión de máquina virtual de Key Vault para Windows requiere que la máquina virtual de destino esté conectada a Internet. 

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema para la extensión de máquina virtual de Key Vault. La extensión no requiere configuración protegida, ya que todas sus opciones se consideran información sin riesgo de seguridad. La extensión requiere una lista de secretos supervisados, frecuencia de sondeo y el almacén de certificados de destino. Concretamente:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Valores de propiedad

| NOMBRE | Valor / ejemplo | Tipo de datos |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | fecha |
| publisher | Microsoft.Azure.KeyVault.Edp | string |
| Tipo | KeyVaultForWindows | string |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | string |
| certificateStoreLocation  | LOCAL_MACHINE | string |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | matriz de cadenas


## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. Las plantillas son ideales al implementar una o varias máquinas virtuales que requieren la actualización de los certificados tras la implementación. La extensión se puede implementar en máquinas virtuales individuales o en conjuntos de escalado de máquinas virtuales. El esquema y la configuración son comunes para ambos tipos de plantilla. 

La configuración de JSON para una extensión de máquina virtual debe estar anidada dentro del fragmento de recursos de máquina virtual de la plantilla, en concreto el objeto `"resources": []` de la máquina virtual.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Implementación de Azure PowerShell

Azure PowerShell puede usarse para implementar la extensión de máquina virtual de Key Vault en una máquina virtual o un conjunto de escalado de máquinas virtuales. 

* Para implementar la extensión en una máquina virtual:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implementar la extensión en un conjunto de escalado de máquinas virtuales, por ejemplo, como parte de un clúster de Service Fabric:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Tenga en cuenta las restricciones y los requisitos siguientes:
- La implementación debe realizarse en la región Centro-sur de EE. UU.
- Restricciones de Key Vault:
    - Debe existir en el momento de la implementación. 
    - Debe encontrarse en la misma región y grupo de recursos que la implementación.
    - Debe estar habilitado para la implementación y la implementación de plantillas.

## <a name="azure-cli-deployment"></a>Implementación de la CLI de Azure

Pronto habrá disponibles ejemplos para la implementación de la extensión de máquina virtual de Key Vault en la CLI de Azure. 

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante Azure PowerShell. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente con Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

El resultado de la ejecución de las extensiones se registra en el archivo siguiente:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).
