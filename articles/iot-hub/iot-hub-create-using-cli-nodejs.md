---
title: "Creación de un centro de IoT Hub mediante la CLI de Azure (azure.js) | Microsoft Docs"
description: "Describe cómo crear un centro de IoT Hub de Azure mediante la CLI de Azure entre plataformas (azure.js)."
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: boltean
ms.openlocfilehash: 5e37c6c5e8625ce446ab203f19f9a8b2f1cd5a46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Creación de una instancia de IoT Hub mediante la CLI de Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar la CLI de Azure (azure.js) para crear y administrar los centros de IoT Hub de Azure mediante programación. En este artículo se muestra cómo utilizar la CLI de Azure (azure.js) para crear un centro de IoT Hub.

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* CLI de Azure (azure.js): la CLI de los modelos de implementación de administración de recursos y clásico, tal y como se describe en este artículo.
* [CLI de Azure 2.0 (az.py)](iot-hub-create-using-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [CLI de Azure 0.10.4][lnk-CLI-install] o una versión posterior. Si ya tiene la CLI de Azure instalada, puede validar la versión actual en el símbolo del sistema con el comando siguiente:

```azurecli
azure --version
```

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [Azure Resource Manager y clásico](../azure-resource-manager/resource-manager-deployment-model.md). La CLI de Azure debe estar en el modo Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Definición de su cuenta y suscripción de Azure

1. En el símbolo del sistema, escriba el siguiente comando para iniciar sesión:

   ```azurecli
    azure login
   ```

   Utilice el explorador web sugerido y el código de autenticación.
1. Si tiene varias suscripciones de Azure, la conexión a Azure le concede acceso a todas las suscripciones asociadas a sus credenciales. Puede ver las suscripciones de Azure, y determinar cuál es la predeterminada, mediante el comando:

   ```azurecli
    azure account list
   ```

   Para establecer el contexto de la suscripción en el que desea ejecutar el resto de los comandos, use:

   ```azurecli
    azure account set <subscription name>
   ```

1. Si no tiene un grupo de recursos, puede crear uno denominado **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> El artículo [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure][lnk-CLI-arm] ofrece más información sobre cómo usar la CLI de Azure para administrar recursos de Azure.

## <a name="create-an-iot-hub"></a>Creación de un IoT Hub

Parámetros obligatorios:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**. El nombre del grupo de recursos. El formato no distingue mayúsculas de minúsculas, admite caracteres alfanuméricos, guiones bajos y guiones, y debe tener una longitud de entre 1 y 64 caracteres.
* **nombre**. El nombre del centro de IoT Hub que se va a crear. El formato no distingue mayúsculas de minúsculas, admite caracteres alfanuméricos, guiones bajos y guiones, y debe tener una longitud de entre 3 y 50 caracteres.
* **location**. La ubicación (centro de datos/región de Azure) para aprovisionar el centro de IoT Hub.
* **sku-name**. El nombre de la SKU; uno de los siguientes: [F1, S1, S2 o S3]. Para consultar la lista completa más reciente, diríjase a la página de precios de IoT Hub.
* **units**. El número de unidades aprovisionadas. Intervalo: F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. Las unidades de IoT Hub se basan en el recuento total de mensajes y en el número de dispositivos que desea conectar.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Para ver todos los parámetros de creación disponibles, puede usar el comando de ayuda en el símbolo del sistema:

```azurecli
azure iothub create -h
```

Ejemplo rápido: para crear un centro de IoT Hub denominado **exampleIoTHubName** en el grupo de recursos **exampleResourceGroup**, ejecute el siguiente comando:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Este comando de la CLI de Azure crea un IoT Hub Estándar S1 por el que se le cobrará. Puede eliminar el IoT Hub denominado **exampleIoTHubName** con el siguiente comando:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo desarrollar para IoT Hub, consulte el siguiente artículo:

* [SDK de IoT][lnk-sdks]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Uso de Azure Portal para administrar IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
