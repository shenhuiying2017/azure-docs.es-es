## <a name="create-a-resource-group"></a>Crear un grupo de recursos

 Un [grupo de recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) es un contenedor lógico en el que se administran recursos como aplicaciones web y bases de datos. Implemente, actualice y elimine los recursos de un grupo de recursos.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Para ver las ubicaciones disponibles, use el comando `az appservice list-locations`. Generalmente crea recursos en una región cercana.

## <a name="create-an-azure-app-service-plan"></a>Crear un plan de Azure App Service

Cree un [plan de App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO" con el comando [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

En el siguiente comando se crea un plan de App Service denominado `quickStartPlan` con el plan de tarifa **Gratuito**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Creación de una aplicación web