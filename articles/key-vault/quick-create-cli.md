---
title: 'Guía de inicio rápido de Azure: Creación de una instancia de Key Vault mediante la CLI | Microsoft Docs'
description: Guía de inicio rápido que muestra cómo crear una instancia de Azure Key Vault mediante la CLI
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/16/2018
ms.author: barclayn
ms.openlocfilehash: aaf8b93a41399b7754fb458d7d1d278a64f82139
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-an-azure-key-vault-using-the-cli"></a>Guía de inicio rápido: Creación de una instancia de Azure Key Vault mediante la CLI

Azure Key Vault es un servicio en la nube que funciona como un almacén de secretos seguro. Puede almacenar de forma segura claves, contraseñas, certificados y otros secretos. Para más información sobre Key Vault, puede consultar esta [introducción](key-vault-overview.md). La CLI de Azure se usa para crear y administrar recursos de Azure mediante comandos o scripts. En este artículo, va a crear una instancia de Key Vault. En esta guía de inicio rápido, va a crear un almacén de claves. Una vez que haya terminado, almacenará un secreto.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI en un entorno local, para esta guía de inicio rápido se necesita la versión 2.0.4 de la CLI de Azure o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

Para iniciar sesión en Azure mediante la CLI puede escribir:

```azurecli
az login
```

Para más información sobre las opciones de inicio de sesión mediante la CLI, consulte [Inicio de sesión con la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *ContosoResourceGroup* en la ubicación *eastus*.

```azurecli
az group create --name 'ContosoResourceGroup' --location eastus
```

## <a name="create-a-key-vault"></a>Creación de un almacén de claves

A continuación, creará una instancia de Key Vault en el grupo de recursos creado en el paso anterior. Tendrá que proporcionar algo de información:

- En esta guía de inicio rápido se usará **Contoso-vault2**. Debe proporcionar un nombre exclusivo en las pruebas.
- Nombre del grupo de recursos: **ContosoResourceGroup**.
- Ubicación: **Este de EE. UU**.

```azurecli
az keyvault create --name 'Contoso-Vault2' --resource-group 'ContosoResourceGroup' --location eastus
```

La salida de este cmdlet muestra las propiedades del almacén de claves que acaba de crear. Tome nota de las dos propiedades siguientes:

- **Nombre de almacén**: en este ejemplo es **Contoso-Vault2**. Utilizará este nombre para otros comandos de Key Vault.
- **URI de almacén**: en el ejemplo, es https://contoso-vault2.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

En este momento, su cuenta de Azure es la única autorizada para realizar operaciones en este nuevo almacén.

## <a name="add-a-secret-to-key-vault"></a>Incorporación de un secreto a Key Vault

Para agregar un secreto al almacén, simplemente debe realizar un par de pasos adicionales. Esta contraseña la podría usar una aplicación. La contraseña se denominará **ExamplePassword** y se almacenará el valor **Pa$$w0rd** en ella.

Escriba los siguientes comandos para crear un secreto en Key Vault denominado **ExamplePassword** que almacenará el valor **Pa$$w0rd**:

```azurecli
az keyvault secret set --vault-name 'Contoso-Vault2' --name 'ExamplePassword' --value 'Pa$$w0rd'
```

Ahora puede hacer referencia a esta clave que agregó a Azure Key Vault utilizando su URI. Use **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** para obtener la versión actual. 

Para ver el valor contenido en el secreto como texto sin formato:

```azurecli
az keyvault secret show --name 'ExamplePassword' --vault-name 'Contoso-Vault2'
```

Ya ha creado una instancia de Key Vault, ha almacenado un secreto y, posteriormente, lo ha recuperado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido y tutoriales de esta colección se basan en los valores de esta. Si tiene pensado seguir trabajando en otras guías de inicio rápido y tutoriales, considere la posibilidad de dejar estos recursos activos.
Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos y todos los recursos relacionados. Puede eliminar los recursos como se indica a continuación:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Key Vault y almacenado un secreto en ella. Para más información sobre Key Vault y cómo lo puede utilizar con sus aplicaciones, siga con el tutorial sobre aplicaciones web que funcionan con Key Vault.

> [!div class="nextstepaction"]
> Consulte [Uso de Azure Key Vault desde una aplicación web](key-vault-use-from-web-application.md) para más información sobre cómo leer un secreto desde Key Vault mediante una aplicación web con una [identidad de servicio administrada](/active-directory/managed-service-identity/overview.md), continúe con el siguiente tutorial: [Configuración de una aplicación web de Azure para que lea un secreto desde el almacén de claves](tutorial-web-application-keyvault.md)
