---
title: 'Tutorial: escalado automático de un conjunto de escalado con la CLI de Azure 2.0 | Microsoft Docs'
description: Aprenda a escalar automáticamente un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0 a medida que la demanda de la CPU aumenta y disminuye
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6f184ac0b2af3a66affecd1a3a9c247a96e616f8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Tutorial: escalado automático de un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0
Al crear un conjunto de escalado, puede definir el número de instancias de máquina virtual que quiere ejecutar. A medida que cambia la demanda de las aplicaciones, puede aumentar o reducir automáticamente el número de estas instancias. La posibilidad de realizar el escalado automático le permite satisfacer la demanda del cliente o responder a los cambios de rendimiento de la aplicación a lo largo del ciclo de vida de esta. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar el escalado automático con un conjunto de escalado
> * Crear y usar reglas de escalado automático
> * Realizar pruebas de esfuerzo de instancias de máquina virtual y desencadenar reglas de escalado automático
> * Reducir el escalado automáticamente cuando se reduzca la demanda

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, en este tutorial es preciso que ejecute la CLI de Azure 2.0.29 o versiones posteriores. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Creación de un conjunto de escalado
Para ayudarle con la creación de reglas de escalado automático, defina algunos parámetros para el identificador de suscripción, el grupo de recursos, y el nombre y la ubicación del conjunto de escalado:

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

Cree un grupo de recursos con [az group create](/cli/azure/group#create), como sigue:

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

Ahora, cree un conjunto de escalado de máquinas virtuales con [az vmss create](/cli/azure/vmss#create). En el ejemplo siguiente se crea un conjunto de escalado con *2* instancias y se generan claves SSH (si aún no existen):

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>Definición de un perfil de escalado automático
Las reglas de escalado automático se implementan como JSON (notación de objetos JavaScript) con la CLI de Azure 2.0. Veamos las partes de este perfil de escalado automático y creemos un ejemplo integral.

El inicio del perfil de escalado automático define la capacidad mínima, máxima y predeterminada del conjunto de escalado. En el ejemplo siguiente se establece la capacidad predeterminada y la mínima para *2* instancias de máquina virtual y un máximo de *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-autoscale-out"></a>Creación de una regla de escalado automático horizontal
Si aumenta la demanda de la aplicación, la carga de las instancias de máquina virtual del conjunto de escalado aumenta. Si este aumento de la carga es continuado, en lugar de ser algo puntual, puede configurar reglas de escalado automático para aumentar el número de instancias de máquina virtual en el conjunto de escalado. Cuando se crean estas instancias de máquina virtual y se implementan las aplicaciones, el conjunto de escalado empieza a distribuir el tráfico entre ellas mediante el equilibrador de carga. Puede controlar qué métricas se deben supervisar como, por ejemplo, la CPU o el disco, cuánto tiempo debe cumplir la carga de la aplicación un límite determinado y cuántas instancias de máquina virtual se deben agregar al conjunto de escalado.

Vamos a crear una regla que aumente el número de instancias de máquina virtual de un conjunto de escalado cuando la carga promedio de la CPU sea superior al 70 % durante un período de más de 5 minutos. Cuando se desencadena la regla, aumenta el número de instancias de máquina virtual en tres.

Los siguientes parámetros se utilizan para esta regla:

| .         | Explicación                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | La métrica de rendimiento del conjunto de escalado sobre el que realizar las acciones de supervisión y aplicación.                                                   | Porcentaje de CPU  |
| *timeGrain*       | Frecuencia de recopilación de las métricas para el análisis.                                                                   | 1 minuto        |
| *timeAggregation* | Define cómo se deben agregar las métricas recopiladas para el análisis.                                                | Media         |
| *timeWindow*      | El periodo durante el que se realiza la supervisión antes de que se comparen los valores de métricas y umbrales.                                   | 5 minutos       |
| *operator*        | El operador que se utiliza para comparar los datos de las métricas con los umbrales.                                                     | Mayor que    |
| *threshold*       | El valor que hace que la regla de escalado automático desencadene una acción.                                                      | 70%             |
| *dirección*       | Define si el conjunto de escalado debe escalarse o reducirse horizontalmente al aplicar la regla.                                              | Aumento        |
| *type*            | Indica que el número de instancias de máquina virtual debe cambiarse por un valor especificado.                                    | Nº de cambios    |
| *value*           | Número de instancias de máquina virtual que se debe escalar o reducir horizontalmente al aplicar la regla.                                             | 3               |
| *cooldown*        | El periodo que hay que esperar hasta que la regla se vuelva a aplicar, para que las acciones de escalado automático tengan tiempo de surtir efecto. | 5 minutos       |

En el ejemplo siguiente se define la regla para escalar horizontalmente el número de instancias de máquina virtual. *metricResourceUri* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "ChangeCount",
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>Creación de una regla de escalado automático horizontal de reducción
La demanda de la aplicación puede reducirse por las tardes o durante los fines de semana. Si esta reducción es constante a lo largo de un período, puede configurar reglas de escalado automático para reducir el número de instancias de máquina virtual del conjunto de escalado. Esta acción de reducción horizontal permite rebajar el costo de ejecutar el conjunto de escalado ya que solo se ejecuta el número de instancias necesario para satisfacer la demanda actual.

Cree otra regla que reduzca el número de instancias de máquina virtual de un conjunto de escalado cuando la carga promedio de la CPU sea inferior al 30 % durante un período de más de 5 minutos. En el ejemplo siguiente se define la regla para reducir horizontalmente el número de instancias de máquina virtual en una unidad. *metricResourceUri* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicación de reglas de escalado automático a un conjunto de escalado
El último paso consiste en aplicar el perfil y reglas de escalado automático al conjunto de escalado. Posteriormente, el conjunto de escalado se podrá escalar o reducir horizontalmente en función de la demanda de la aplicación. Aplique el perfil de escalado automático con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) como se indica a continuación. El siguiente JSON completo utiliza el perfil y las reglas que se han indicado en las secciones anteriores:

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "3",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>Generación de carga de CPU en el conjunto de escalado
Para probar las reglas de escalado automático, genere cargas de CPU en las instancias de máquina virtual del conjunto de escalado. Esta carga de CPU simulada hace que el escalado automático escale y reduzca horizontalmente el número de instancias de máquina virtual. Como la carga simulada de la CPU se reduce, las reglas de escalado automático reducen horizontalmente el número de instancias de máquina virtual.

Primero, enumere las direcciones y los puertos para conectarse a las instancias de máquina virtual en un conjunto de escalado con [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

La salida del ejemplo siguiente muestra el nombre de instancia, la dirección IP pública del equilibrador de carga y el número de puerto al que las reglas de traducción de direcciones de red (NAT) reenvían el tráfico:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Conéctese mediante SSH a la primera instancia de máquina virtual. Especifique su propia dirección IP pública y el número de puerto con el parámetro `-p`, tal como se muestra en el comando anterior:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Una vez iniciada la sesión, instale la utilidad **stress**. Inicie *10* trabajos de **stress** que generen carga de CPU. Estos trabajos se ejecutan durante *420* segundos, lo suficiente para que las reglas de escalado automático implementen la acción deseada.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Cuando **stress** muestre resultados similares a *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, presione la tecla *Entrar* para volver al símbolo del sistema.

Para confirmar que **stress** genera carga de CPU, examine la carga del sistema activa con la utilidad **top**:

```azuecli-interactive
top
```

Salga de la utilidad **top** y cierre la conexión a la instancia de máquina virtual. **Stress** continuará ejecutándose en la instancia de máquina virtual.

```azurecli-interactive
Ctrl-c
exit
```

Conéctese a la segunda instancia de máquina virtual con el número de puerto de la lista [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) anterior:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Instale y ejecute la utilidad **stress** e inicie diez trabajos en esta segunda instancia de máquina virtual.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

De nuevo, cuando **stress** muestre resultados similares a *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, presione la tecla *Entrar* para volver al símbolo del sistema.

Cierre la conexión a esta instancia de máquina virtual. **Stress** continuará ejecutándose en la instancia de máquina virtual.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Supervisión de las reglas de escalado automático activas
Para supervisar el número de instancias de máquina virtual del conjunto de escalado, use la utilidad **watch**. Las reglas de escalado automático tardan 5 minutos en empezar el proceso de escalado horizontal en respuesta a la carga de CPU generada por **stress** en cada instancia de máquina virtual:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

Cuando se alcanza el umbral de la CPU, las reglas de escalado automático aumentan el número de instancias de máquina virtual en el conjunto de escalado. La siguiente salida muestra tres máquinas virtuales que se crean durante el escalado horizontal automático:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Cuando se detiene **stress** en las instancias de máquina virtual iniciales, la carga media de CPU vuelve al estado normal. Después de otros 5 minutos, las reglas de escalado automático reducen horizontalmente el número de instancias de máquina virtual. Con las acciones de reducción horizontal se eliminan instancias de máquina virtual en orden, empezando por los identificadores mayores. Cuando un conjunto de escalado usa conjuntos de disponibilidad o zonas de disponibilidad, las acciones de reducción horizontal se distribuyen por igual entre esas instancias de máquina virtual. En la salida de ejemplo siguiente se muestra la eliminación de una instancia de máquina virtual con la reducción horizontal del conjunto de escalado:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Salga de la utilidad *watch* con `Ctrl-c`. El conjunto de escalado continúa la reducción horizontal cada 5 minutos y quita una instancia de máquina virtual hasta que se alcanza el mínimo de dos instancias.


## <a name="clean-up-resources"></a>Limpieza de recursos
Para quitar el conjunto de escalado y los recursos adicionales, elimine el grupo de recursos y todos sus recursos con [az group delete](/cli/azure/group#az_group_delete). El parámetro `--no-wait` devuelve el control a la petición de confirmación sin esperar a que finalice la operación. El parámetro `--yes` confirma que desea eliminar los recursos sin pedir confirmación adicional.

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a escalar y reducir horizontalmente un conjunto de escalado de forma automática con la CLI de Azure 2.0:

> [!div class="checklist"]
> * Usar el escalado automático con un conjunto de escalado
> * Crear y usar reglas de escalado automático
> * Realizar pruebas de esfuerzo de instancias de máquina virtual y desencadenar reglas de escalado automático
> * Reducir el escalado automáticamente cuando se reduzca la demanda

Para más ejemplos de conjuntos de escalado de máquinas virtuales en acción, vea los siguientes scripts de ejemplo de la CLI de Azure 2.0:

> [!div class="nextstepaction"]
> [Ejemplos de scripts de conjuntos de escalado para la CLI de Azure 2.0](cli-samples.md)