<properties
	pageTitle="Azure Insights: ejemplos de inicio rápido de CLI multiplataforma de Azure Insights | Microsoft Azure"
	description="Los comandos de CLI de ejemplo pueden ayudarle a acceder a las características de supervisión de Azure Insights. Azure Insights es un servicio de Microsoft Azure que permite escalar automáticamente aplicaciones web, máquinas virtuales y servicios en la nube para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="ashwink"/>

# Ejemplos de inicio rápido de CLI multiplataforma de Azure Insights

En este artículo se muestran comandos de la interfaz de la línea de comandos (CLI) de ejemplo que lo ayudarán a acceder a las características de supervisión de Azure Insights. Azure Insights permite escalar automáticamente aplicaciones web, máquinas virtuales y servicios en la nube para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados.


## Requisitos previos

Si no ha instalado aún la CLI, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md). Si no está familiarizado con la CLI de Azure, puede obtener más información en [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


En Windows, instale npm desde el [sitio web de Node.js](https://nodejs.org/). Después de completar la instalación, usando el archivo CMD.exe con los privilegios Ejecutar como administrador, ejecute el siguiente comando desde la carpeta donde se ha instalado npm:

```
npm install azure-cli --global
```

Después, vaya a cualquier carpeta o ubicación que desee y escriba en la línea de comandos:

```
azure help
```

## Inicie sesión en Azure.

El primer paso consiste en iniciar sesión en su cuenta de Azure.

```
azure login
```

Después de ejecutar este comando, tendrá que iniciar sesión siguiendo las instrucciones de la pantalla. Después, podrá ver el id. predeterminado de la suscripción, el id. de inquilino y la cuenta. Todos los comandos funcionan en el contexto de la suscripción predeterminada.

Para mostrar los detalles de la suscripción actual, utilice el siguiente comando.

```
azure account show
```

Para cambiar el contexto de trabajo a una suscripción diferente, utilice el siguiente comando.

```
azure account set "subscription ID or subscription name"
```

Para utilizar los comandos de Azure Resource Manager y Azure Insights, debe estar en el modo ARM.

```
azure config mode arm
```

Para ver una lista de todos los comandos de Azure Insights admitidos, realice lo siguiente.

```
azure insights
```

## Consulta de registros de auditoría para una suscripción

Para ver una lista de registros de auditoría, realice lo siguiente.

```
azure insights logs list [options]
```

Pruebe el siguiente comando para ver todas las opciones disponibles.

```
azure insights logs list -help
```

Se trata de un ejemplo para mostrar los registros por grupo de recursos.

```
azure insights logs list --resourceGroup "myrg1"
```

Ejemplo para mostrar registros por autor de llamada

```
azure insights logs list --caller "myname@company.com"
```

Ejemplo para mostrar registros por tipo de recurso en una fecha de inicio y de finalización

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## Uso de alertas
Puede usar la información de la sección sobre cómo usar las alertas.

### Obtención de reglas de alerta en un grupo de recursos

```
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### Creación de una regla de alerta de métrica

```
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### Creación de una regla de alerta de registro

```
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Creación de una regla de alerta de WebTest

```
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### Creación una regla de alerta

```
azure insights alerts rule delete abhingrgtest123 andy0323
```

## Perfiles de registro
Utilice la información de esta sección para usar perfiles de registro.

### Obtención de un perfil de registro

```
azure insights logprofile list
azure insights logprofile get -n default
```


### Adición de un perfil de registro sin retención de datos

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Eliminación de un perfil de registro

```
azure insights logprofile delete --name default
```

### Adición de un perfil de registro con retención de datos

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### Adición de un perfil de registro con retención y EventHub

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## Diagnóstico
Utilice la información de esta sección para usar la configuración de diagnóstico.

### Obtención de la configuración de diagnóstico

```
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### Deshabilitación de la configuración de diagnóstico

```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### Habilitación de la configuración de diagnóstico sin retención

```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
Utilice la información de esta sección para usar la configuración de escalado automático. Tendrá que modificar estos ejemplos.

### Obtención de la configuración de escalado automático de un grupo de recursos

```
azure insights autoscale setting list montest2
```

### Obtención de valores de escalado automático por su nombre en un grupo de recursos

```
azure insights autoscale setting list montest2 -n setting2
```


### Establecimiento de la configuración de escalado automático

```
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0914_2016-->