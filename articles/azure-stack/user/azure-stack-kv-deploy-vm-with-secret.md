---
title: "Implementación de una máquina virtual con una contraseña almacenada de forma segura en Azure Stack | Microsoft Docs"
description: "Aprenda a implementar una máquina virtual usando una contraseña almacenada en el almacén de claves de Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: sngun
ms.openlocfilehash: 3292a2dfefc17e5034c66122a3eab24d6c03e694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Creación de una máquina virtual mediante la recuperación de la contraseña almacenada en un almacén de claves

Si necesita pasar un valor seguro, como una contraseña, durante la implementación, puede almacenar ese valor como un secreto en un almacén de claves de Azure Stack y hacer referencia al secreto en las plantillas de Azure Resource Manager. No tiene que escribir manualmente el secreto cada vez que implemente los recursos, solo tiene que especificar qué usuarios o entidades de servicio pueden tener acceso al secreto. 

En este artículo, se le guiará por los pasos necesarios para implementar una máquina virtual Windows en Azure Stack mediante la recuperación de la contraseña que está guardada en un almacén de claves. Por lo tanto, la contraseña nunca se coloca en texto sin formato en el archivo de parámetros de plantilla. Puede seguir los pasos descritos aquí ya sea desde Azure Stack Development Kit o desde un cliente externo, si se conecta a través de VPN.

## <a name="prerequisites"></a>Requisitos previos
 
* Debe suscribirse a una oferta que incluya el servicio Key Vault.  
* [Instale PowerShell para Azure Stack.](azure-stack-powershell-install.md)  
* [Configuración del entorno de PowerShell del usuario de Azure Stack.](azure-stack-powershell-configure-user.md)

Los pasos siguientes describen el proceso necesario para crear una máquina virtual mediante la recuperación de la contraseña almacenada en un almacén de claves:

1. Cree un secreto de almacén de claves.
2. Actualice el archivo azuredeploy.parameters.json.
3. Implemente la plantilla.

## <a name="create-a-key-vault-secret"></a>Creación de un secreto de almacén de claves

El script siguiente crea un almacén de claves y almacena en él una contraseña como un secreto. Use el parámetro `-EnabledForDeployment` al crear el almacén de claves. Este parámetro se asegura de que se puede hacer referencia al almacén de claves desde las plantillas de Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Cuando se ejecuta el script anterior, la salida incluye el identificador URI del secreto. Anote este URI. Tendrá que hacer referencia a él durante la [implementación de máquina virtual Windows con contraseña en la plantilla de almacén de claves](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Descargue la carpeta [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) en el equipo de desarrollo. Esta carpeta contiene los archivos `azuredeploy.json` y `azuredeploy.parameters.json` que necesitará en los pasos siguientes.

Modificar el archivo `azuredeploy.parameters.json` según los valores del entorno. Los parámetros de especial interés son el nombre del almacén, el grupo de recursos del almacén y el identificador URI del secreto (que se generó en el script anterior). El archivo siguiente es un ejemplo de un archivo de parámetros:

## <a name="update-the-azuredeployparametersjson-file"></a>Actualice el archivo azuredeploy.parameters.json

Actualice el archivo azuredeploy.parameters.json con los valores de KeyVault URI, secretName y adminUsername de la máquina virtual correspondientes a su entorno. El siguiente archivo JSON muestra un ejemplo del archivo de parámetros de plantilla: 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Implementación de plantilla

Ahora implemente la plantilla con el siguiente script de PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
Cuando la plantilla se ha implementado correctamente, se producen en la siguiente salida:

![Salida de la implementación](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>Pasos siguientes
[Implementación de una aplicación de ejemplo con Key Vault](azure-stack-kv-sample-app.md)

[Implementación de una máquina virtual con un certificado de Key Vault](azure-stack-kv-push-secret-into-vm.md)

