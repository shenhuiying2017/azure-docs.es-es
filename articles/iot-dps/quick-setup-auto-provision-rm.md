---
title: "Configuración de Device Provisioning con una plantilla de Azure Resource Manager | Microsoft Docs"
description: "Guía de inicio rápido de Azure: configuración del servicio Azure IoT Hub Device Provisioning con una plantilla"
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
ms.openlocfilehash: 827be2be6915b0f0c9892e73b8f0a293a9659b6e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Configuración del servicio IoT Hub Device Provisioning con una plantilla de Azure Resource Manager

Puede utilizar [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) para configurar mediante programación los recursos de nube de Azure necesarios para aprovisionar los dispositivos. Estos pasos muestran cómo crear una instancia de IoT Hub, una nueva instancia del servicio IoT Hub Device Provisioning y vincular los dos servicios mediante una plantilla de Azure Resource Manager. Esta guía de inicio rápido utiliza la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli) para realizar los pasos de programación necesarios para crear un grupo de recursos e implementar la plantilla, pero puede utilizar fácilmente [Azure Portal](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby u otros lenguajes de programación para realizar estos pasos e implementar la plantilla. 


## <a name="prerequisites"></a>requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Esta guía de inicio rápido requiere que se ejecute localmente la CLI de Azure. Debe tener instalada la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Inicio de sesión en Azure y creación de un grupo de recursos

Inicie sesión en la cuenta de Azure y seleccione su suscripción.

1. En el símbolo del sistema, ejecute el [comando de inicio de sesión][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga las instrucciones para realizar la autenticación mediante el código e inicie sesión en la cuenta de Azure a través de un explorador web.

2. Si tiene varias suscripciones de Azure, iniciar sesión en Azure le concede acceso a todas las cuentas de Azure asociadas con las credenciales. Use el siguiente [comando para mostrar las cuentas de Azure][lnk-az-account-command] que tiene disponibles para su uso:
    
    ```azurecli
    az account list 
    ```

    Use el siguiente comando para seleccionar la suscripción que desea usar para ejecutar los comandos que crearán la instancia de IoT Hub. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Cuando se crean recursos de nube de Azure como centros de IoT y servicios de aprovisionamiento, se crean en un grupo de recursos. Use un grupo de recursos existente o ejecute el comando [siguiente para crear un grupo de recursos][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > El ejemplo anterior crea el grupo de recursos en la ubicación del oeste de EE. UU. Puede ejecutar el comando `az account list-locations -o table` para ver una lista de las ubicaciones disponibles.
    >
    >

## <a name="create-a-resource-manager-template"></a>Creación de una plantilla de Resource Manager

Utilice una plantilla JSON para crear un servicio de aprovisionamiento y un centro de IoT vinculado en su grupo de recursos. También puede usar una plantilla de Azure Resource Manager para realizar cambios en un servicio de aprovisionamiento o en un centro de IoT existente.

1. Use un editor de texto para crear una plantilla de Azure Resource Manager llamada **template.json**, con el siguiente contenido de esquema. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Reemplace la sección **parameters** por el contenido siguiente:. La sección parameters especifica los parámetros que se pueden pasar desde otro archivo. En esta sección se especifica el nombre del centro de IoT y del servicio de aprovisionamiento que se van a crear. También especifica la ubicación tanto para el centro de IoT como para el servicio de aprovisionamiento. Los valores están restringidos a las regiones de Azure que admiten centros de IoT y servicios de aprovisionamiento. Para obtener una lista de ubicaciones compatibles para el servicio Device Provisioning, puede ejecutar el siguiente comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` o visitar la página [Estado de Azure](https://azure.microsoft.com/status/) y busque "Servicio Device Provisioning".

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Reemplace la sección **variables** por el contenido siguiente. En esta sección se definen los valores que se utilizan posteriormente para construir la cadena de conexión del centro de IoT, necesaria para vincular el servicio de aprovisionamiento y el centro de IoT. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Para crear un centro de IoT, agregue las líneas siguientes a la colección **resources**. El JSON especifica las propiedades mínimas necesarias para crear un centro de IoT. Las propiedades **name** y **location** se pasan como parámetros. Para más información sobre las propiedades que se pueden especificar para un centro de IoT en una plantilla, consulte [Microsoft.Devices/IotHubs template reference](https://docs.microsoft.com/en-us/azure/templates/microsoft.devices/iothubs) (Referencia de la plantilla Microsoft.Devices/IotHubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Para crear el servicio de aprovisionamiento, agregue las siguientes líneas después de la especificación del centro de IoT en la colección **Resources**. Las propiedades **name** y **location** del servicio de aprovisionamiento se pasan en los parámetros. Especifique los centros de IoT que se van a vincular al servicio de aprovisionamiento en la colección **iotHubs**. Como mínimo, debe especificar las propiedades **connectionString** y **location** para cada centro de IoT vinculado. También puede establecer propiedades como **allocationWeight** y **applyAllocationPolicy** en cada centro de IoT, así como propiedades, como **allocationPolicy** y **authorizationPolicies** en el mismo servicio de aprovisionamiento. Para más información, consulte [Microsoft.Devices/provisioningServices template reference](https://docs.microsoft.com/en-us/azure/templates/microsoft.devices/provisioningservices) (Referencia de plantilla Microsoft.Devices/provisioningServices).

   La propiedad **dependsOn** se utiliza para asegurarse de que Resource Manager crea el centro de IoT antes de crear el servicio de aprovisionamiento. La plantilla requiere la cadena de conexión del centro de IoT para especificar su vinculación con el servicio de aprovisionamiento, por lo que primero debe crearse el centro y sus claves. La plantilla utiliza funciones como **concat** y **listKeys** para crear la cadena de conexión. Para más información, consulte [Funciones de la plantilla de Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Guarde el archivo de plantilla. La plantilla terminada debe ser similar a la siguiente:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Creación de un archivo de parámetros de Resource Manager

La plantilla que ha definido en el último paso utiliza parámetros para especificar el nombre del centro de IoT, el nombre del servicio de aprovisionamiento y la ubicación (región de Azure) para crearlos. Estos parámetros se pasan en un archivo independiente. De este modo, puede volver a usar la misma plantilla para varias implementaciones. Para crear el archivo de parámetros, siga estos pasos:

1. Use un editor de texto para crear un archivo de parámetros de Azure Resource Manager llama **parameters.json** con el siguiente contenido de esquema: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Agregue el valor **iotHubName** a la sección de parámetro. Si cambia el nombre, asegúrese de que sigue las convenciones de nomenclatura adecuadas para un centro de IoT. Debe tener una longitud de entre 3 y 50 caracteres, y puede contener solo caracteres alfanuméricos en mayúscula y minúscula y guiones ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Agregue el valor **provisioningServiceName** a la sección de parámetro. Si cambia el nombre, asegúrese de que sigue las convenciones de nomenclatura adecuadas para un servicio IoT Hub Device Provisioning. Debe tener una longitud de entre 3 y 64 caracteres, y puede contener solo caracteres alfanuméricos en mayúscula y minúscula y guiones ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Agregue el valor **hubLocation** a la sección de parámetro. Este valor especifica la ubicación del centro de IoT y del servicio de aprovisionamiento. El valor debe corresponder a una de las ubicaciones especificadas en la colección **allowedValues** de la definición del parámetro en el archivo de plantilla. Esta colección restringe los valores a las ubicaciones de Azure que admiten los centros de IoT y los servicios de aprovisionamiento. Para obtener una lista de ubicaciones compatibles para el servicio Device Provisioning, puede ejecutar el siguiente comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` o visitar la página [Estado de Azure](https://azure.microsoft.com/status/) y busque "Servicio Device Provisioning".

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Guarde el archivo. 


> [!IMPORTANT]
> Tanto el centro de IoT como el servicio de aprovisionamiento pueden detectarse públicamente como puntos de conexión DNS; por tanto, procure evitar cualquier información confidencial al darles el nombre.
>

## <a name="deploy-the-template"></a>Implementación de la plantilla

Utilice los siguientes comandos de la CLI de Azure para implementar las plantillas y comprobar la implementación.

1. Para implementar la plantilla, ejecute el siguiente [comando para iniciar una implementación](https://docs.microsoft.com/en-us/cli/azure/group/deployment?view=azure-cli-latest#az_group_deployment_create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Busque la propiedad **provisioningState** establecida en "Correcto" en la salida. 

   ![Aprovisionamiento de salida](./media/quick-setup-auto-provision-rm/output.png) 


2. Para comprobar la implementación, ejecute el siguiente [comando para enumerar los recursos](https://docs.microsoft.com/en-us/cli/azure/resource?view=azure-cli-latest#az_resource_list) y busque el nuevo servicio de aprovisionamiento y el centro de IoT en la salida:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido o tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no piensa continuar, puede usar la CLI de Azure para [eliminar un recurso individual][lnk-az-resource-command], como una instancia de IoT Hub o un servicio de aprovisionamiento, o para eliminar un grupo de recursos y todos sus recursos.

Para eliminar el servicio de aprovisionamiento, ejecute el siguiente comando:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Ejecute el comando siguiente para eliminar una instancia de IoT Hub:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Ejecute el comando siguiente para eliminar un grupo de recursos y todos sus recursos:

```azurecli
az group delete --name {your resource group name}
```

También puede eliminar grupos de recursos y recursos individuales mediante Azure Portal, PowerShell, las API REST o con los SDK admitidos por la plataforma publicados para Azure Resource Manager o IoT Hub, y el servicio Device Provisioning.

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, ha implementado un centro de IoT y una instancia del servicio Device Provisioning, y ha vinculado los dos recursos. Para aprender a usar esta configuración para aprovisionar un dispositivo simulado, siga la guía de inicio rápido que permite crear un dispositivo simulado.

> [!div class="nextstepaction"]
> [Guía de inicio rápido para crear el dispositivo simulado](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
