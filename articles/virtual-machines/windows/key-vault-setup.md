---
title: Configuración de Key Vault para máquinas virtuales Windows en Azure Resource Manager | Microsoft Docs
description: Cómo configurar Key Vault para usarlo con una máquina virtual de Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: f8f094bfb0f304123cbdf719bec22185431aca5a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918394"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configuración de Key Vault para máquinas virtuales en Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

En la pila de Azure Resource Manager, los certificados o secretos se modelan como recursos que se proporcionan mediante el proveedor de recursos de Key Vault. Para más información sobre Key Vault, consulte [¿Qué es Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Para poder usar Key Vault con máquinas virtuales de Azure Resource Manager, la propiedad **EnabledForDeployment** de Key Vault se debe establecer en true. Puede hacer esto en varios clientes.
> 2. El almacén de claves debe crearse en la misma ubicación y suscripción que la máquina virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Uso de PowerShell para configurar Key Vault
Para crear un almacén de claves usando PowerShell, consulte [Introducción a Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Para almacenes de claves nuevos, puede usar este cmdlet de PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para almacenes de claves existentes, puede usar este cmdlet de PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Uso de la CLI para configurar Key Vault
Para crear un almacén de claves mediante la interfaz de la línea de comandos (CLI), consulte [Administración de Key Vault mediante la CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para la CLI, primero debe crear el almacén de claves y luego asignar la directiva de implementación. Para ello, puede usar el siguiente comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Uso de plantillas para configurar Key Vault
Al utilizar plantillas, deberá establecer la propiedad `enabledForDeployment` en `true` para el recurso de Key Vault.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para otras opciones que puede configurar al crear un almacén de claves mediante plantillas, consulte [Create a key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)(Creación de un almacén de claves).
