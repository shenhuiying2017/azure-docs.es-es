---
title: 'Guía de inicio rápido de Azure: Creación de una instancia de Key Vault mediante PowerShell | Microsoft Docs'
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: e7aa675e849fb8c4dac0bfb5a1ae44dd16ca25e3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068303"
---
# <a name="quickstart-create-an-azure-key-vault-using-powershell"></a>Guía de inicio rápido: Creación de una instancia de Azure Key Vault mediante PowerShell

Azure Key Vault es un servicio en la nube que funciona como un almacén de secretos seguro. Puede almacenar de forma segura claves, contraseñas, certificados y otros secretos. Para más información sobre Key Vault, puede consultar esta [introducción](key-vault-overview.md). En esta guía de inicio rápido, va a usar PowerShell para crear un almacén de claves. Posteriormente, va a almacenar un secreto en el almacén recién creado.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, en este tutorial necesitará la versión 5.1.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

```azurepowershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

```azurepowershell
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Creación de un almacén de claves

A continuación, puede crear una instancia de Key Vault. Cuando se realiza este paso, se necesita cierta información:

Aunque aquí hemos usado "Contoso KeyVault2" como el nombre de la instancia de Key Vault a lo largo de esta guía de inicio rápido, usted debe utilizar un nombre único.

- **Nombre del almacén**: Contoso-Vault2.
- **Nombre del grupo de recursos**: ContosoResourceGroup.
- **Ubicación**: Este de EE. UU.

```azurepowershell
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

La salida de este cmdlet muestra las propiedades del almacén de claves que acaba de crear. Tome nota de las dos propiedades siguientes:

* **Nombre del almacén**: en este ejemplo es **Contoso-Vault2**. Utilizará este nombre para otros cmdlets de Key Vault.
* **URI de almacén**: en este ejemplo, es https://contosokeyvault.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Después de la creación del almacén, la cuenta de Azure es la única cuenta a la que se permite realizar cualquier acción en este nuevo almacén.

![Salida tras completarse el comando de creación de Key Vault](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Incorporación de un secreto a Key Vault

Para agregar un secreto al almacén, solo tiene que realizar un par de pasos. En este caso, ha agregado una contraseña que una aplicación podría usar. La contraseña se denomina **ExamplePassword** y se almacena el valor ""**Pa$$w0rd**"" en ella.

Convierta en primer lugar el valor de Pa$$w0rd en una cadena segura escribiendo:

```azurepowershell
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

A continuación, escriba los siguientes comandos de PowerShell para crear un secreto en Key Vault denominado **ExamplePassword** con el valor **Pa$$w0rd**:

```azurepowershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Para ver el valor contenido en el secreto como texto sin formato:

```azurepowershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Ya ha creado una instancia de Key Vault, ha almacenado un secreto y, posteriormente, lo ha recuperado.

## <a name="clean-up-resources"></a>Limpieza de recursos

 Otras guías de inicio rápido y tutoriales de esta colección se basan en los valores de esta. Si tiene pensado seguir trabajando en otras guías de inicio rápido y tutoriales, considere la posibilidad de dejar estos recursos activos.

Cuando ya no los necesite, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la instancia de Key Vault y todos los recursos relacionados.

```azurepowershell
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Key Vault y almacenado un secreto de software en ella. Para más información sobre Key Vault y cómo lo puede utilizar con sus aplicaciones, siga con el tutorial sobre aplicaciones web que funcionan con Key Vault.

> [!div class="nextstepaction"]
> Para obtener más información sobre cómo leer un secreto desde Key Vault mediante una aplicación web con una identidad de servicio administrada, consulte el siguiente tutorial: [Configure an Azure web application to read a secret from Key vault](tutorial-web-application-keyvault.md) (Configurar una aplicación web de Azure para que lea un secreto desde el almacén de claves).
