---
title: Implementar la plantilla de Azure con el token de SAS y la CLI de Azure | Microsoft Docs
description: "Use Azure Resource Manager y la CLI de Azure para implementar recursos en Azure desde una plantilla que está protegida con el token de SAS."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 22387aadd8f53a65efb76a29a9403c46a2c25954
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Implementar la plantilla de Resource Manager privada con el token de SAS y la CLI de Azure

Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación. En este tema se explica cómo usar Azure PowerShell con plantillas de Resource Manager para proporcionar un token de SAS durante la implementación. 

## <a name="add-private-template-to-storage-account"></a>Adición de plantilla privada a la cuenta de almacenamiento

Puede agregar las plantillas a una cuenta de almacenamiento y establecer vínculos a ellas durante la implementación con un token de SAS.

> [!IMPORTANT]
> Siguiendo estos pasos, el blob que contiene la plantilla solo es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SAS para el blob, el blob es accesible para cualquier persona con ese URI. Si otro usuario intercepta el URI, ese usuario podrá tener acceso a la plantilla. El uso de un token de SAS supone un buen método para limitar el acceso a las plantillas, pero no debe incluir información confidencial como contraseñas directamente en estas.
> 
> 

El siguiente ejemplo configura un contenedor de una cuenta de almacenamiento privado y carga una plantilla:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Provisión del token de SAS durante la implementación
Para implementar una plantilla privada en una cuenta de almacenamiento, genere un token de SAS e inclúyalo en el identificador URI de la plantilla. Establezca el tiempo de expiración con un margen suficiente para completar la implementación.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Para obtener un ejemplo del uso de un token de SAS con plantillas vinculadas, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una introducción a la implementación de plantillas, vea [Implementación de recursos con plantillas de Resource Manager y Azure PowerShell](resource-group-template-deploy-cli.md).
* Para obtener un script de ejemplo completo que implementa una plantilla, vea [Deploy Resource Manager template script](resource-manager-samples-cli-deploy.md) (Implementar script de plantilla de Resource Manager).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
