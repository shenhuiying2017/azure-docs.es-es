---
title: "Configuración de Key Vault para máquinas virtuales Windows en Azure Resource Manager | Microsoft Docs"
description: "Cómo configurar un Almacén de claves para usarlo con una máquina virtual de Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a5083a5216efbfd76fd912ec48c2f0ec3b30c4a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configuración de un Almacén de claves para máquinas virtuales en Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

En la pila de Azure Resource Manager, los certificados o secretos se modelan como recursos que se proporcionan mediante el proveedor de recursos del Almacén de claves. Para más información sobre el Almacén de claves, consulte [¿Qué es el Almacén de claves de Azure?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Para que un Almacén de claves se utilice con máquinas virtuales de Azure Resource Manager, la propiedad **EnabledForDeployment** del Almacén de claves se debe establecer en true. Puede hacer esto en varios clientes.
> 2. El Almacén de claves debe crearse en la misma ubicación y suscripción que la máquina virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Uso de PowerShell para configurar el Almacén de claves
Para crear un almacén de claves usando PowerShell, consulte [Introducción al Almacén de claves de Azure](../../key-vault/key-vault-get-started.md#vault).

Para almacenes de claves nuevos, puede usar este cmdlet de PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para almacenes de claves existentes, puede usar este cmdlet de PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Uso de la CLI para configurar el Almacén de claves
Para crear un almacén de claves mediante la interfaz de la línea de comandos (CLI), consulte [Administración del Almacén de claves mediante CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para la CLI, primero debe crear el almacén de claves y luego asignar la directiva de implementación. Para ello, puede usar el siguiente comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Uso de plantillas para configurar el Almacén de claves
Al utilizar plantillas, deberá establecer la propiedad `enabledForDeployment` en `true` para el recurso de Almacén de claves.

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
