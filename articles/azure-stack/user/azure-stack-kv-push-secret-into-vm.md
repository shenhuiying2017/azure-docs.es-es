---
title: "Implementación de una máquina virtual con un certificado almacenado de forma segura en Azure Stack | Microsoft Docs"
description: "Aprenda a implementar una máquina virtual e inserte en ella un certificado utilizando un almacén de claves en Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Creación de una máquina virtual e inclusión de un certificado recuperado de un almacén de claves

En este artículo le ayuda a crear una máquina virtual en Azure Stack y a insertar certificados en ella. 

## <a name="prerequisites"></a>Requisitos previos

* Debe suscribirse a una oferta que incluya el servicio Key Vault. 
* [Instale PowerShell para Azure Stack.](azure-stack-powershell-install.md)  
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)

Un almacén de claves en Azure Stack se utiliza para almacenar certificados. Los certificados son útiles en varios escenarios diferentes. Por ejemplo, considere un escenario en el que haya una máquina virtual en Azure Stack que está ejecutando una aplicación que necesita un certificado. Este certificado se puede utilizar para cifrar, para la autenticación en Active Directory, o para SSL en un sitio web. Tener el certificado guardado en un almacén de claves, le ayuda a cerciorarse de que está seguro.

En este artículo, se le guiará por los pasos necesarios para insertar un certificado en una máquina virtual Windows en Azure Stack. Puede seguir los pasos descritos aquí ya sea desde el kit de desarrollo de Azure Stack o desde un cliente externo basado en Windows, si se conecta a través de VPN.

Los pasos siguientes describen el proceso necesario para insertar un certificado en la máquina virtual:

1. Cree un secreto de almacén de claves.
2. Actualice el archivo azuredeploy.parameters.json.
3. Implementación de la plantilla

## <a name="create-a-key-vault-secret"></a>Creación de un secreto de almacén de claves

El script siguiente crea un certificado con el formato .pfx, crea un almacén de claves y almacena el certificado en el almacén de claves como un secreto. Use el parámetro `-EnabledForDeployment` al crear el almacén de claves. Este parámetro se asegura de que se puede hacer referencia al almacén de claves desde las plantillas de Azure Resource Manager.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

Cuando se ejecuta el script anterior, la salida incluye el identificador URI del secreto. Anote este URI. Tendrá que hacer referencia a él en la [inserción de certificado en la plantilla de Resource Manager de Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Descargue la carpeta de la [plantilla vm-push-certificate-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) en el equipo de desarrollo. Esta carpeta contiene los archivos `azuredeploy.json` y `azuredeploy.parameters.json` que necesitará en los pasos siguientes.

Modificar el archivo `azuredeploy.parameters.json` según los valores del entorno. Los parámetros de especial interés son el nombre del almacén, el grupo de recursos del almacén y el identificador URI del secreto (que se generó en el script anterior). El archivo siguiente es un ejemplo de un archivo de parámetros:

## <a name="update-the-azuredeployparametersjson-file"></a>Actualice el archivo azuredeploy.parameters.json

Actualice el archivo azuredeploy.parameters.json con los valores de vaultName (nombre del almacén), URI del secreto, VmName (nombre de la máquina virtual) y otros valores según el entorno. El siguiente archivo JSON muestra un ejemplo del archivo de parámetros de plantilla: 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Implementación de la plantilla

Ahora implemente la plantilla con el siguiente script de PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Cuando la plantilla se ha implementado correctamente, se producen en la siguiente salida:

![Salida de la implementación](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Cuando se implementa esta máquina virtual, Azure Stack inserta el certificado en la máquina virtual. En Windows, el certificado se agrega a la ubicación de certificados LocalMachine, con el almacén de certificados que el usuario proporcionó. En Linux, el certificado se coloca en el directorio /var/lib/waagent, con el nombre de archivo &lt;UppercaseThumbprint&gt;.crt para el archivo de certificado X509 y &lt;UppercaseThumbprint&gt;.prv para la clave privada.

## <a name="retire-certificates"></a>Retirada de certificados

En la sección anterior, se muestra cómo insertar un nuevo certificado en una máquina virtual. El certificado antiguo todavía está en la máquina virtual y no se puede quitar. Sin embargo, puede deshabilitar la versión anterior del secreto mediante el cmdlet `Set-AzureKeyVaultSecretAttribute`. A continuación, verá un ejemplo de cómo usar este cmdlet. Asegúrese de reemplazar el nombre del almacén, nombre de secreto y valores de versión de acuerdo con su entorno:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una máquina virtual con una contraseña de Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Permitir el acceso de una aplicación a Key Vault](azure-stack-kv-sample-app.md)


