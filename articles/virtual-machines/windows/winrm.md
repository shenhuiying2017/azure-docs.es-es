---
title: "Configuración del acceso a WinRM para una máquina virtual de Azure | Microsoft Docs"
description: "Configure el acceso a WinRM para usarlo con una máquina virtual Azure creada con el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 2d6533462400bc1d93d0d3b0227769784e2658a9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configuración de acceso a WinRM para máquinas virtuales en Azure Resource Manager
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM en administración de servicios de Azure frente a Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

* Para ver información general sobre Azure Resource Manager, consulte este [artículo](../../azure-resource-manager/resource-group-overview.md)
* Para conocer las diferencias entre la administración de servicios de Azure y Azure Resource Manager, consulte este [artículo](../../resource-manager-deployment-model.md)

La diferencia clave en la configuración de WinRM entre las dos pilas es el modo en que se instala el certificado en la máquina virtual. En la pila de Azure Resource Manager, los certificados se modelan como recursos que se administran mediante el proveedor de recursos del Almacén de claves. Por lo tanto, el usuario debe proporcionar su propio certificado y cargarlo en un Almacén de claves antes de usar una máquina virtual.

Estos son los pasos que debe seguir para configurar una máquina virtual con conectividad de WinRM.

1. Creación de un Almacén de claves
2. Creación de un certificado autofirmado
3. Carga del certificado autofirmado en el Almacén de claves
4. Obtención de la dirección URL para el certificado autofirmado en el Almacén de claves
5. Referencia a la dirección URL de los certificados autofirmados durante la creación de una máquina virtual

## <a name="step-1-create-a-key-vault"></a>Paso 1: Creación de un Almacén de claves
Puede utilizar el siguiente comando para crear el Almacén de claves:

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Paso 2: Creación de un certificado autofirmado
Puede crear un certificado autofirmado mediante este script de PowerShell:

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Paso 3: Carga del certificado autofirmado en el Almacén de claves
Antes de cargar el certificado en el Almacén de claves que creó en el paso 1, debe convertirlo a un formato que entienda el proveedor de recursos Microsoft.Compute. El siguiente script de PowerShell le permitirá hacer eso:

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Paso 4: Obtención de la dirección URL del certificado autofirmado en el Almacén de claves
El proveedor de recursos Microsoft.Compute necesita una dirección URL al secreto en el Almacén de claves durante el aprovisionamiento de la máquina virtual. De esta manera, dicho proveedor podrá descargar el secreto y crear el certificado equivalente en la máquina virtual.

> [!NOTE]
> La dirección URL del secreto debe incluir también la versión. Este es un ejemplo de una dirección URL de este tipo https://contosovault.vault.azure.net:443/secretos/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Plantillas
Puede obtener el vínculo a la dirección URL en la plantilla mediante el siguiente código:

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Puede obtener esta dirección URL mediante el siguiente comando de PowerShell:

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Paso 5: Referencia a la dirección URL de los certificados autofirmados durante la creación de una máquina virtual
#### <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure
Al crear una máquina virtual mediante plantillas, se hace referencia al certificado en la sección de secretos y en la sección de WinRM de la manera siguiente:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Una plantilla de ejemplo para el caso mencionado se puede encontrar aquí en [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

El código fuente de esta plantilla está disponible en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Paso 6: Conexión a la máquina virtual
Antes de poder conectarse a la máquina virtual, debe asegurarse de que el equipo esté configurado para la administración remota de WinRM. Inicie PowerShell como administrador y ejecute el siguiente comando para asegurarse de que se encuentra en el programa de instalación.

    Enable-PSRemoting -Force

> [!NOTE]
> Si la solución anterior no funciona, debe asegurarse de que el servicio WinRM esté en ejecución. Para ello, utilice `Get-Service WinRM`
> 
> 

Cuando haya finalizado la instalación, puede conectarse a la máquina virtual mediante el comando siguiente:

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
