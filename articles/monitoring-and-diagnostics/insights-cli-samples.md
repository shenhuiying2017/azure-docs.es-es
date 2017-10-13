---
title: "Ejemplos de inicio rápido de CLI de Azure Monitor 1.0 | Microsoft Docs"
description: "Comandos de ejemplo de la CLI 1.0 para las características de Azure Monitor. Azure Monitor es un servicio de Microsoft Azure que permite enviar notificaciones de alerta, llamar a direcciones URL web en función de los valores de datos de telemetría configurados y escalar automáticamente Cloud Services, Virtual Machines y Web Apps."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Ejemplos de inicio rápido de la CLI multiplataforma de Azure Monitor 1.0
En este artículo se muestran comandos de la interfaz de la línea de comandos (CLI) de ejemplo que lo ayudarán a acceder a las características de supervisión de Azure Monitor. Azure Monitor permite escalar automáticamente Cloud Services, Virtual Machines y Web Apps para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados.

> [!NOTE]
> Azure Monitor es el nuevo nombre de lo que se conocía como "Azure Insights" hasta el 25 de septiembre de 2016. Sin embargo, los espacios de nombres y, por tanto, los siguientes comandos aún contienen el término "insights".
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Si no ha instalado aún la CLI, consulte [Instalación de la CLI de Azure](../cli-install-nodejs.md). Si no está familiarizado con la CLI de Azure, puede obtener más información en [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

En Windows, instale npm desde el [sitio web de Node.js](https://nodejs.org/). Después de completar la instalación, usando el archivo CMD.exe con los privilegios Ejecutar como administrador, ejecute el siguiente comando desde la carpeta donde se ha instalado npm:

```console
npm install azure-cli --global
```

Después, vaya a cualquier carpeta o ubicación que desee y escriba en la línea de comandos:

```console
azure help
```

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
El primer paso consiste en iniciar sesión en su cuenta de Azure.

```console
azure login
```

Después de ejecutar este comando, tendrá que iniciar sesión siguiendo las instrucciones de la pantalla. Después, podrá ver el id. predeterminado de la suscripción, el id. de inquilino y la cuenta. Todos los comandos funcionan en el contexto de la suscripción predeterminada.

Para mostrar los detalles de la suscripción actual, utilice el siguiente comando.

```console
azure account show
```

Para cambiar el contexto de trabajo a una suscripción diferente, utilice el siguiente comando.

```console
azure account set "subscription ID or subscription name"
```

Para utilizar los comandos de Azure Resource Manager y Azure Monitor, debe estar en el modo ARM.

```console
azure config mode arm
```

Para ver una lista de todos los comandos de Azure Monitor admitidos, realice lo siguiente.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Visualización del registro de actividades para una suscripción
Para ver una lista de eventos del registro de actividades, realice lo siguiente.

```console
azure insights logs list [options]
```

Pruebe el siguiente comando para ver todas las opciones disponibles.

```console
azure insights logs list -help
```

Se trata de un ejemplo para mostrar los registros por grupo de recursos.

```console
azure insights logs list --resourceGroup "myrg1"
```

Ejemplo para mostrar registros por autor de llamada

```console
azure insights logs list --caller "myname@company.com"
```

Ejemplo para mostrar registros por tipo de recurso en una fecha de inicio y de finalización

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Uso de alertas
Puede usar la información de la sección sobre cómo usar las alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obtención de reglas de alerta en un grupo de recursos
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Creación de una regla de alerta de métrica
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>Creación de una regla de alerta de WebTest
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Creación una regla de alerta
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Perfiles de registro
Utilice la información de esta sección para usar perfiles de registro.

### <a name="get-a-log-profile"></a>Obtención de un perfil de registro
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Adición de un perfil de registro sin retención de datos
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Eliminación de un perfil de registro
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Adición de un perfil de registro con retención de datos
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adición de un perfil de registro con retención y EventHub
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnóstico
Utilice la información de esta sección para usar la configuración de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obtención de la configuración de diagnóstico
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Deshabilitación de la configuración de diagnóstico
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Habilitación de la configuración de diagnóstico sin retención
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
Utilice la información de esta sección para usar la configuración de escalado automático. Tendrá que modificar estos ejemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtención de la configuración de escalado automático de un grupo de recursos
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtención de valores de escalado automático por su nombre en un grupo de recursos
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Establecimiento de la configuración de escalado automático
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
