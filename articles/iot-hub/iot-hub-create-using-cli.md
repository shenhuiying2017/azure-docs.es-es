---
title: "Creación de un centro de IoT mediante la CLI de Azure | Microsoft Docs"
description: "Siga las instrucciones de este artículo para crear un IoT Hub mediante la interfaz de la línea de comandos de Azure."
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
ms.date: 09/21/2016
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 637cdc4763cd0caa9f75e25015c0c12e9db629cb


---
# <a name="create-an-iot-hub-using-azure-cli"></a>Creación de un IoT Hub mediante la CLI de Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción
Puede usar la interfaz de la línea de comandos de Azure para crear y administrar IoT Hub de Azure mediante programación. En este artículo se muestra cómo utilizar la CLI de Azure para crear un Centro de IoT.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [CLI de Azure 0.10.4][lnk-CLI-install] o una versión posterior. Si ya tiene la CLI de Azure, puede validar la versión actual en el símbolo del sistema con el comando siguiente:
  ```
    azure --version
  ```

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [Azure Resource Manager y clásico](../azure-resource-manager/resource-manager-deployment-model.md). La CLI de Azure debe estar en el modo Azure Resource Manager:
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Definición de su cuenta y suscripción de Azure
1. En el símbolo del sistema, escriba el siguiente comando para iniciar sesión
   
   ```
    azure login
   ```
   Utilice el explorador web sugerido y el código de autenticación.
2. Si tiene varias suscripciones de Azure, la conexión a Azure le dará acceso a todas las suscripciones de Azure asociadas a sus credenciales. Podrá ver las suscripciones de Azure, así como cuál es la predeterminada, mediante el comando.
   
   ```
    azure account list 
   ```

   Para establecer el contexto de la suscripción en el que desea ejecutar el resto de los comandos, use

   ```
    azure account set <subscription name>
   ```

3. Si no tiene un grupo de recursos, puede crear uno denominado **exampleResourceGroup** 
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> El artículo [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure][lnk-CLI-arm] ofrece más información sobre cómo usar la CLI de Azure para administrar recursos de Azure. 
> 
> 

## <a name="create-an-iot-hub"></a>Creación de un IoT Hub
Parámetros obligatorios:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Para ver todos los parámetros de creación disponibles, puede usar el comando de ayuda en el símbolo del sistema

```
    azure iothub create -h 
```
Ejemplo rápido:

 Para crear un Centro de IoT denominado **exampleIoTHubName** en el grupo de recursos **exampleResourceGroup** simplemente ejecute el siguiente comando

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Este comando de la CLI de Azure crea un IoT Hub Estándar S1 por el que se le cobrará. Puede eliminar el Centro de IoT **exampleIoTHubName** con el siguiente comando 
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca del desarrollo para el Centro de IoT, consulte lo siguiente:

* [SDK de IoT][lnk-sdks]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Uso de Azure Portal para administrar IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


