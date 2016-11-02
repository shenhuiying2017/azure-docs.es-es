<properties
    pageTitle="Creación de un Centro de IoT mediante la CLI | Microsoft Azure"
    description="Siga las instrucciones de este artículo para crear un Centro de IoT mediante la interfaz de línea de comandos de Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>


# <a name="create-an-iot-hub-using-cli"></a>Creación de un Centro de IoT mediante la CLI

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar la interfaz de línea de comandos de Azure para crear y administrar Centros de IoT de Azure mediante programación. En este artículo se muestra cómo utilizar la CLI de Azure para crear un Centro de IoT.

Para completar este tutorial, necesitará lo siguiente:

- Una cuenta de Azure activa. Puede crear una cuenta de [evaluación gratuita de Azure][lnk-free-trial] en pocos minutos.
- [Azure CLI 0.10.4][lnk-CLI-install] o posterior. Si ya tiene la CLI de Azure, puede validar la versión actual en el símbolo del sistema con el comando siguiente:
```
    azure --version
```

> [AZURE.NOTE] Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). La CLI de Azure debe estar en el modo Azure Resource Manager:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Definición de su cuenta y suscripción de Azure 

1. En el símbolo del sistema, escriba el siguiente comando para iniciar sesión
```
    azure login
```
Utilice el explorador web sugerido y el código de autenticación.

2. Si tiene varias suscripciones de Azure, la conexión a Azure le dará acceso a todas las suscripciones asociadas a sus credenciales. Podrá ver las suscripciones, así como cuál es la predeterminada, mediante el comando
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

> [AZURE.TIP] El artículo [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure][lnk-CLI-arm] ofrece más información acerca de cómo usar la CLI de Azure para administrar recursos de Azure. 


## <a name="create-and-iot-hub"></a>Creación de un Centro de IoT

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

> [AZURE.NOTE] Este comando de la CLI crea un Centro de IoT estándar S1 por el que se le cobrará. Puede eliminar el Centro de IoT **exampleIoTHubName** con el siguiente comando 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Pasos siguientes
Para más información acerca del desarrollo para el Centro de IoT, consulte lo siguiente:
- [SDK de IoT Hub][lnk-sdks]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Administración de IoT Hub a través de Azure Portal][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


