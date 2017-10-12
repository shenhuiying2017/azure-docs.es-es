---
title: Conectarse a Azure Stack con CLI | Microsoft Docs
description: "Obtenga información sobre cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: bd731c32d32063b54d5899db3b3a13a911ca79be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Instalar y configurar la CLI para su uso con Azure Stack

En este documento, le guiaremos en el proceso de uso de la interfaz de la línea de comandos (CLI) de Azure para administrar los recursos del kit de desarrollo de Azure Stack de las plataformas de cliente de Linux y Mac. 

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportación del certificado raíz de CA de Azure Stack

Si usa la CLI desde una máquina virtual que se ejecuta en el entorno del kit de desarrollo de Azure Stack, el certificado raíz de Azure Stack ya está instalado en la máquina virtual para que pueda recuperar directamente. En cambio, si usa la CLI desde una estación de trabajo fuera del kit de desarrollo, debe exportar el certificado raíz de la entidad de certificación de Azure Stack desde el kit de desarrollo y agregarlo al almacén de certificados de Python de la estación de trabajo de desarrollo (plataforma externa de Linux o Mac). 

Inicie sesión en el kit de desarrollo y ejecute el script siguiente para exportar el certificado raíz de Azure Stack en formato PEM:

```powershell
   $label = "AzureStackSelfSignedRootCert"
   Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
   $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
   if (-not $root)
   {
       Log-Error "Cerficate with subject CN=$label not found"
       return
   }

   Write-Host "Exporting certificate"
   Export-Certificate -Type CERT -FilePath root.cer -Cert $root

   Write-Host "Converting certificate to PEM format"
   certutil -encode root.cer root.pem
```

## <a name="install-cli"></a>Instalar la CLI

A continuación, debe iniciar sesión en la estación de trabajo de desarrollo e instalar la CLI. Azure Stack requiere la versión 2.0 de la CLI de Azure, que se puede instalar siguiendo los pasos que se describen en el artículo [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Para comprobar si la instalación se realizó correctamente, abra un terminal o una ventana del símbolo del sistema y ejecute el siguiente comando:

```azurecli
az --version
```

Debería ver la versión de la CLI de Azure y otras bibliotecas dependientes instaladas en su equipo.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar en el certificado de raíz de CA de Azure Stack

Para confiar en el certificado de raíz de CA de Azure Stack, debe anexarlo al certificado existente de Python. Si está ejecutando la CLI desde una máquina de Linux creada dentro del entorno de Azure Stack, ejecute el siguiente comando de bash:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Si está ejecutando la CLI desde una máquina fuera del entorno de Azure Sack, primero debe configurar la [conectividad VPN a Azure Stack](azure-stack-connect-azure-stack.md). Ahora copie el certificado PEM que ha exportado previamente en la estación de trabajo de desarrollo y ejecute los comandos siguientes según el sistema operativo de la estación de trabajo de desarrollo:

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash=(Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash=(Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash=(Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry  = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Para que los usuarios puedan crear máquinas virtuales mediante la CLI, el administrador de la nube debe establecer un punto de conexión accesible públicamente que contenga los alias de imagen de la máquina virtual y registrar dicho punto de conexión con la nube. El parámetro `endpoint-vm-image-alias-doc` del comando `az cloud register` se usa para este propósito. Los administradores de la nube deben descargar la imagen en Marketplace de Azure Stack antes de agregarla al punto de conexión de los alias de imagen.
   
Por ejemplo, el contenido de Azure usa el URI siguiente: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. El administrador de la nube debe configurar un punto de conexión similar para Azure Stack con las imágenes que están disponibles en Marketplace de Azure Stack.

## <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

Use los pasos siguientes para conectarse a Azure Stack:

1. Registre el entorno de Azure Stack. Para ello, ejecute el comando az cloud register.
   
   a. Para registrar el entorno **administrativo de nube**, use:

   ```azurecli
   az cloud register \ 
     -n AzureStackAdmin \ 
     --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
     --suffix-storage-endpoint "local.azurestack.external" \ 
     --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
     --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
     --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
   ```

   b. Para registrar el entorno de **usuario**, use:

   ```azurecli
   az cloud register \ 
     -n AzureStackUser \ 
     --endpoint-resource-manager "https://management.local.azurestack.external" \ 
     --suffix-storage-endpoint "local.azurestack.external" \ 
     --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
     --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
     --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
   ```

2. Establezca el entorno activo mediante los comandos siguientes:

   a. Para el entorno **administrativo de nube**, use:

   ```azurecli
   az cloud set \
     -n AzureStackAdmin
   ```

   b. Para el entorno de **usuario**, use:

   ```azurecli
   az cloud set \
     -n AzureStackUser
   ```

3. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack. Para actualizar la configuración, ejecute el comando siguiente:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Inicie sesión en el entorno de Azure Stack mediante el comando **az login**. Puede iniciar sesión en el entorno de Azure Stack como un usuario o una [entidad de servicio](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects). 

   * Iniciar sesión como un **usuario**: puede especificar el nombre de usuario y la contraseña directamente en el comando az login o autenticarse mediante un explorador. Deberá hacer esto último, si la cuenta tiene habilitada la autenticación multifactor.

   ```azurecli
   az login \
     -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
     --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
   ```

   > [!NOTE]
   > Si su cuenta de usuario tiene la autenticación multifactor habilitada, puede usar el comando az login sin proporcionar el parámetro -u. Al ejecutar el comando, se le proporciona una URL y un código que debe usar para la autenticación.
   
   * Iniciar sesión como una **entidad de servicio**: antes de iniciar sesión, [cree una entidad de servicio a través de Azure Portal](azure-stack-create-service-principals.md) o la CLI y asígnele un rol. Ahora, inicie sesión con el siguiente comando:

   ```azurecli
   az login \
     --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
     --service-principal \
     -u <Application Id of the Service Principal> \
     -p <Key generated for the Service Principal>
   ```

## <a name="test-the-connectivity"></a>Probar la conectividad

Ahora que todo está configurado, vamos a usar la CLI para crear recursos en Azure Stack. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="next-steps"></a>Pasos siguientes

[Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)

[Administración de permisos de usuario](azure-stack-manage-permissions.md)

