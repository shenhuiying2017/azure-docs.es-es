---
title: "Guía de inicio rápido de Azure: Creación de una cuenta de almacenamiento con la CLI de Azure | Microsoft Docs"
description: "Aprenda rápidamente a crear una nueva cuenta de almacenamiento con la CLI de Azure."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: 7186c5e2ce94d06b21d95a557e960b82e268cdce
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Creación de una cuenta de almacenamiento con la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Esta guía de inicio rápido detalla el uso de la CLI de Azure para crear una cuenta de Azure Storage.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con el comando [az group create](/cli/azure/group#create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En este ejemplo, se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Si no está seguro de qué región especificar para el parámetro `--location`, puede recuperar una lista de regiones admitidas para la suscripción con el comando [az account list-locations](/cli/azure/account#list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Creación de una cuenta de almacenamiento estándar de uso general

Existen varios tipos de cuentas de almacenamiento adecuadas para distintos escenarios de uso, cada uno de los cuales admite uno o más de los servicios de almacenamiento (blobs, archivos, tablas o colas). La siguiente tabla describe los tipos de cuenta de almacenamiento disponibles.

|**Tipo de cuenta de almacenamiento**|**Uso general estándar**|**Uso general premium**|**Niveles de acceso frecuente y esporádico de Blob Storage**|
|-----|-----|-----|-----|
|**Servicios admitidos**| Blob service, File service, Table service y Queue service | Blob service | Blob service|
|**Tipos de blobs compatibles**|Blobs en bloques, en páginas y en anexos | Blobs en páginas | Blobs en bloques y blobs en anexos|

Cree una cuenta de almacenamiento estándar de uso general con el comando [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida la cuenta de almacenamiento que creó en esta guía de inicio rápido, elimine el grupo de recursos con el comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado un grupo de recursos y una cuenta de almacenamiento estándar de uso general. Para obtener información sobre cómo transferir datos desde la cuenta de almacenamiento y hasta ella, continúe con la guía de inicio rápido de Blob Storage.

> [!div class="nextstepaction"]
> [Transferencia de objetos a Azure Blob Storage y desde Azure Blob Storage mediante la CLI de Azure](../blobs/storage-quickstart-blobs-cli.md)