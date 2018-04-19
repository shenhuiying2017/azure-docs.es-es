---
title: Ejemplos de inicio rápido de CLI de Azure Monitor 2.0. | Microsoft Docs
description: Comandos de ejemplo de la CLI 2.0 para las características de Azure Monitor. Azure Monitor es un servicio de Microsoft Azure que permite enviar notificaciones de alerta, llamar a direcciones URL web en función de los valores de datos de telemetría configurados y escalar automáticamente Cloud Services, Virtual Machines y Web Apps.
author: kamathashwin
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: ashwink
ms.openlocfilehash: e429ba460a97daed4a7bdf71895fe24c1619a645
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Ejemplos de inicio rápido de CLI de Azure Monitor 2.0
En este artículo se muestran comandos de la interfaz de la línea de comandos (CLI) de ejemplo que lo ayudarán a acceder a las características de supervisión de Azure Monitor. Azure Monitor permite escalar automáticamente Cloud Services, Virtual Machines y Web Apps para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados.

## <a name="prerequisites"></a>requisitos previos

Si aún no ha instalado la CLI de Azure, siga las instrucciones para [instalar la CLI de Azure 2.0](/cli/azure/install-azure-cli). También puede usar [Azure Cloud Shell](/azure/cloud-shell) para ejecutar la CLI como una experiencia interactiva en el explorador. 

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure
El primer paso consiste en iniciar sesión en su cuenta de Azure.

```azurecli
az login
```

Después de ejecutar este comando, tendrá que iniciar sesión siguiendo las instrucciones de la pantalla. Todos los comandos funcionan en el contexto de la suscripción predeterminada.

Para mostrar los detalles de la suscripción actual, utilice el siguiente comando.

```azurecli
az account show
```

Para cambiar el contexto de trabajo a una suscripción diferente, utilice el siguiente comando.

```azurecli
az account set -s <Subscription ID or name>
```

Para ver una lista de todos los comandos de Azure Monitor admitidos, realice lo siguiente.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visualización del registro de actividades para una suscripción

Para ver una lista de eventos del registro de actividades, realice lo siguiente.

```azurecli
az monitor activity-log list
```

Pruebe el siguiente comando para ver todas las opciones disponibles.

```azurecli
az monitor activity-log list -h
```

Se trata de un ejemplo para mostrar los registros por grupo de recursos.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Ejemplo para mostrar registros por autor de llamada

```azurecli
az monitor activity-log list --caller myname@company.com
```

Ejemplo para mostrar registros por autor de llamada en un tipo de recurso, dentro de un intervalo de fechas

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Uso de alertas

Puede usar la información de la sección sobre cómo usar las alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obtención de reglas de alerta en un grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-rule"></a>Creación de una regla de alerta de métrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-rule"></a>Creación una regla de alerta

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfiles de registro

Utilice la información de esta sección para usar perfiles de registro.

### <a name="get-a-log-profile"></a>Obtención de un perfil de registro

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adición de un perfil de registro con retención de datos

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adición de un perfil de registro con retención y EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Eliminación de un perfil de registro

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnóstico

Utilice la información de esta sección para usar la configuración de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obtención de la configuración de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Creación de la configuración del registro de diagnóstico 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Eliminación de la configuración de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Escalado automático

Utilice la información de esta sección para usar la configuración de escalado automático. Tendrá que modificar estos ejemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtención de la configuración de escalado automático de un grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtención de valores de escalado automático por su nombre en un grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-auotoscale-settings"></a>Establecimiento de la configuración de escalado automático

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
