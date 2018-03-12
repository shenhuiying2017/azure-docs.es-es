---
title: "Configuración del aprovisionamiento de dispositivos mediante la CLI de Azure | Microsoft Docs"
description: "Guía de inicio rápido de Azure: configuración del servicio Azure IoT Hub Device Provisioning mediante la CLI de Azure"
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 09b212a5abe2ebb9c123f3adcbdfa59390d16c10
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Configuración del servicio Azure IoT Hub Device Provisioning con la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía de inicio rápido detalla cómo usar la CLI de Azure para crear una instancia de IoT Hub y una instancia del servicio IoT Hub Device Provisioning y para vincular os dos servicios entre sí. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!IMPORTANT]
> Tanto IoT Hub como el servicio de aprovisionamiento que se crean en esta guía de inicio rápido se podrán detectar públicamente como puntos de conexión de DNS. Asegúrese de evitar cualquier información confidencial si decide cambiar que se usan para estos recursos.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos llamado *my-sample-resource-group* en la ubicación *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> El ejemplo crea el grupo de recursos en la ubicación de oeste de EE. UU. Puede ejecutar el comando `az account list-locations -o table` para ver una lista de las ubicaciones disponibles.
>
>

## <a name="create-an-iot-hub"></a>Creación de un centro de IoT

Para crear un centro de IoT, use el comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create). 

En el ejemplo siguiente, se crea un centro de IoT llamado *my-sample-hub* en la ubicación *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Creación de un servicio de aprovisionamiento

Para crear un servicio de aprovisionamiento, use el comando [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create). 

En el ejemplo siguiente, se crea un servicio de aprovisionamiento llamado *my-sample-dps* en la ubicación *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> El ejemplo crea el servicio de aprovisionamiento en la ubicación de oeste de EE. UU. Para ver una lista de las ubicaciones disponibles, ejecute el comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` o vaya a la página [Estado de Azure](https://azure.microsoft.com/status/) página y busque "Servicio Device Provisioning". En los comandos, las ubicaciones se pueden especificar en formato de una palabra o de varias; por ejemplo, westus, West US, WEST US, etc. El valor no distingue mayúsculas de minúsculas. Si utiliza el formato de varias palabras para especificar la ubicación, escriba el valor entre comillas; por ejemplo, `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Obtención de la cadena de conexión del centro de IoT

La cadena de conexión de un centro de IoT se necesita para vincularlo el servicio de aprovisionamiento de dispositivos. Utilice el comando [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) para obtener la cadena de conexión y use su resultado para establecer una variable que utilizará al vincular los dos recursos. 

En el siguiente ejemplo se establece la variable *hubConnectionString* en el valor de la cadena de conexión de la clave principal de la directiva *iothubowner* del centro. Puede especificar otra directiva con el parámetro `--policy-name`. El comando usa las opciones [query](/cli/azure/query-azure-cli) y [output](/cli/azure/format-output-azure-cli#tsv-output-format) de la CLI de Azure para extraer la cadena de conexión de la salida del comando.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Puede usar el comando `echo` para ver la cadena de conexión.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Estos dos comandos son válidos para un host que funcione en Bash. Si usa un shell CMD o Windows local, o un host de PowerShell, tendrá que modificar los comandos para utilizar la sintaxis correcta de dicho entorno.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Vinculación del centro de IoT y el servicio de aprovisionamiento

El centro de IoT y el servicio de aprovisionamiento se vinculan con el comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

En el ejemplo siguiente se vincula un centro de IoT llamado *my-sample-hub* de la ubicación *westus* y servicio de aprovisionamiento de dispositivos llamado *my-sample-dps*. Utiliza la cadena de conexión para el centro *my-sample-hub* almacenado en la variable *hubConnectionString* en el paso anterior.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Comprobación del servicio de aprovisionamiento

Para obtener los detalles del servicio de aprovisionamiento, use el comando [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

El ejemplo siguiente obtiene los detalles de un servicio de aprovisionamiento llamado *my-sample-dps*. El centro de IoT vinculado se muestra en la colección *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido o tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no planea continuar, puede utilizar los siguientes comandos para eliminar el servicio de aprovisionamiento, el centro de IoT o el grupo de recursos y todos sus recursos.

Para eliminar el servicio de aprovisionamiento, ejecute el comando [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Para eliminar el centro de IoT, ejecute el comando [az group delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Para eliminar un grupo de recursos y todos sus recursos, use el comando [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un centro de IoT y una instancia del servicio Device Provisioning, y ha vinculado los dos recursos. Para aprender a usar esta configuración para aprovisionar un dispositivo simulado, siga la guía de inicio rápido que permite crear un dispositivo simulado.

> [!div class="nextstepaction"]
> [Guía de inicio rápido para crear el dispositivo simulado](./quick-create-simulated-device.md)

