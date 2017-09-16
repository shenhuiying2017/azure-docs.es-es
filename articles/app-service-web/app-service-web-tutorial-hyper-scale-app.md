---
title: "Creación de una aplicación a gran escala en Azure | Microsoft Docs"
description: "Aprenda a usar diferentes servicios de Azure para optimizar el rendimiento de una aplicación ASP.NET en Azure."
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 17d046f4f5291233cc5dc29c96e2b68d767d5750
ms.contentlocale: es-es
ms.lasthandoff: 09/09/2017

---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Creación de una aplicación web a gran escala en Azure

En este tutorial se muestra cómo escalar horizontalmente una aplicación web ASP.NET en Azure para optimizar las solicitudes del usuario.

Antes de empezar este tutorial, asegúrese de que [la CLI de Azure esté instalada](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) en la máquina. Además, necesitará [Visual Studio](https://www.visualstudio.com/vs/) en la máquina local para ejecutar la aplicación de ejemplo.

## <a name="step-1---get-sample-application"></a>Paso 1: Obtención de la aplicación de ejemplo
En este paso, configurará el proyecto de ASP.NET local.

### <a name="clone-the-application-repository"></a>Clonación del repositorio de aplicación

Abra el terminal de línea de comandos de su elección y `CD` en un directorio de trabajo. A continuación, ejecute los comandos siguientes para clonar la aplicación de ejemplo. 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Ejecución de la aplicación de ejemplo en Visual Studio

Abra la solución en Visual Studio.

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

Escriba `F5` para ejecutar la aplicación.

Esta aplicación web ASP.NET de ejemplo proviene de la plantilla predeterminada, y guarda las sesiones de usuario y usa la caché de resultados. Eche un vistazo a `HighScaleApp\Controllers\HomeController.cs`. El método `Index()` agrega un elemento de datos a la sesión.

```csharp
Session.Add("visited", "true"); 
```

Y los métodos `About()` y `Contact()` almacenan en caché sus resultados.

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>Paso 2: Implementación en Azure
En este paso, creará una aplicación web de Azure e implementará en ella su aplicación ASP.NET de ejemplo.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos   
Use [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) para crear un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) en la región de Europa Occidental. Un grupo de recursos es el lugar donde se colocan todos los recursos de Azure que se quieren administrar juntos, como la aplicación web y el back-end de SQL Database.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Para ver qué valores posibles puede usar para `---location`, use el comando [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#az_appservice_list_locations).

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones
Use [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#az_appservice_plan_create) para crear un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "B1". 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

Un plan de App Service es una unidad de escalado que puede incluir un número cualesquiera de aplicaciones que quiera escalar vertical u horizontalmente juntas sobre la misma infraestructura de App Service. A cada plan se le asigna también un [plan de tarifa](https://azure.microsoft.com/en-us/pricing/details/app-service/). Los niveles superiores incluyen mejor hardware y más características, por ejemplo, más instancias de escalado horizontal.

En este tutorial, B1 es el nivel mínimo que permite el escalado horizontal a tres instancias. Siempre puede subir o bajar el plan de tarifa de su aplicación en otro momento con el comando [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update). 

### <a name="create-a-web-app"></a>Creación de una aplicación web
Use [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_create) para crear una aplicación web con un nombre único en `$appName`.

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>Configurar credenciales de implementación
Use [az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#az_appservice_web_deployment_user_set) para establecer las credenciales de implementación de nivel de cuenta para App Service.

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Configuración de la implementación de Git
Use [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config_local_git) para configurar la implementación de Git local.

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

Este comando proporciona una salida parecida a la siguiente:

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

Use la dirección URL devuelta para configurar el Git remoto. El siguiente comando usa el ejemplo de salida anterior.

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>Implementación de la aplicación de ejemplo
Ahora está listo para implementar la aplicación de ejemplo. Ejecute `git push`.

```powershell
git push azure master
```

Cuando se le solicite una contraseña, use la contraseña que especificó cuando ejecutó `az appservice web deployment user set`.

### <a name="browse-to-azure-web-app"></a>Navegación hasta la aplicación web de Azure
Use [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) para ver la aplicación en funcionamiento en Azure.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>Paso 3: Conexión a Redis
En este paso, configurará Azure Redis Cache como una caché externa colocada en la aplicación web de Azure. Rápidamente, puede usar Redis para almacenar en caché la salida de la página. Además, al escalar horizontalmente las aplicaciones web más adelante, Redis le ayuda a conservar las sesiones de usuario entre varias instancias de forma confiable.

### <a name="create-an-azure-redis-cache"></a>Creación de una instancia de Caché en Redis de Azure
Use [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) para crear una instancia de Azure Redis Cache y guardar la salida JSON. Use un nombre único en `$cacheName`.

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Configuración de la aplicación para usar Redis
Cambie el formato de la cadena de conexión de su caché.

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

La segunda línea debe darle una salida parecida a la siguiente:

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

En Visual Studio, cree un archivo de configuración web en la raíz del proyecto denominado `redis.config` y pegue el siguiente código en él. En `value`, use la cadena de conexión de la salida de PowerShell.

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Si observa el archivo `.gitignore` en el repositorio Git, verá que este archivo está excluido del control de código fuente. De este modo se mantiene segura la información confidencial. 

Abra `Web.config`. Observe el elemento `<appSettings file="redis.config">`, que obtiene la configuración que creó en `redis.config`. 

Busque la sección comentada que incluye `<sessionState>` y `<caching>`. Quite el comentario de esta sección.

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

Este código busca de la cadena de conexión de Redis que ha definido en `RedisConnection`. 

La aplicación usa ahora Redis para administrar sesiones y almacenamiento en caché. Escriba `F5` para ejecutar la aplicación. Si lo desea, puede descargar un cliente de administración de Redis para visualizar los datos que se guardan ahora en la caché.

### <a name="configure-the-connection-string-in-azure"></a>Configuración de la cadena de conexión en Azure

Para que la aplicación funcione en Azure, debe configurar la misma cadena de conexión de Redis en la aplicación web de Azure. Como `redis.config` no se mantiene en el control de código fuente, no se implementa en Azure cuando se ejecuta la implementación de Git.

Use [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) para agregar la cadena de conexión con el mismo nombre (`RedisConnection`).

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

Recuerde que `$connstring` contiene la cadena de conexión formateada.

### <a name="redeploy-the-application-to-azure"></a>Nueva implementación de la aplicación en Azure
Use comandos de Git para insertar los cambios en Azure.

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

Cuando se le solicite una contraseña, use la contraseña que especificó cuando ejecutó `az appservice web deployment user set`.

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure
Use [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) para ver los cambios en vivo en Azure.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>Paso 4: Escalado a varias instancias
El plan de App Service es la unidad de escalado de las aplicaciones web de Azure. Para escalar horizontalmente su aplicación web, escalará el plan de App Service.

Use [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) para escalar horizontalmente el plan de App Service a tres instancias, que es el número máximo permitido por el plan de tarifa B1. Recuerde que B1 es el plan de tarifa que eligió cuando creó anteriormente el plan de App Service. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>Paso 5: Escalado geográfico
Al escalar geográficamente, ejecutará la aplicación en varias regiones de la nube de Azure. Esta configuración equilibra aún más la carga de su aplicación en función de la región geográfica y reduce el tiempo de respuesta al colocar la aplicación más cerca de los exploradores de cliente.

En este paso, escalará la aplicación web ASP.NET a una segunda región con [Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/). Al final del paso, tendrá una aplicación web que se ejecuta en Europa occidental (ya creada) y una aplicación web que se ejecuta en el Sudeste Asiático (aún no creada). Ambas aplicaciones se atenderán desde la misma dirección URL de Traffic Manager.

### <a name="scale-up-the-europe-app-to-standard-tier"></a>Escalado vertical de la aplicación de Europa al nivel Estándar
En App Service, la integración con Azure Traffic Manager requiere el plan de tarifa Estándar. Use [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) para escalar verticalmente su plan de App Service a S1. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Crear un perfil de Traffic Manager 
Use [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) para crear un perfil de Traffic Manager y agregarlo a su grupo de recursos. Use un nombre DNS único en $dnsName.

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` especifica que este perfil [enruta el tráfico del usuario al punto de conexión más cercano](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="get-the-resource-id-of-the-europe-app"></a>Obtención del id. de recurso de la aplicación de Europa
Use [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_show) para obtener el id. de recurso de la aplicación web.

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>Adición de un punto de conexión de Traffic Manager para la aplicación de Europa
Use [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) para agregar un punto de conexión a su perfil de Traffic Manager y utilizar el id. de recurso de la aplicación web como destino.

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Obtención de la dirección URL del punto de conexión de Traffic Manager
El perfil de Traffic Manager ahora tiene un punto de conexión que apunta a la aplicación web existente. Use [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_show) para obtener su dirección URL. 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

Copie la salida en su explorador. Debería ver la aplicación web nuevo.

### <a name="create-an-azure-redis-cache-in-asia"></a>Creación de una instancia de Azure Redis Cache en Asia
Ahora, replicará la aplicación web de Azure en la región del Sudeste Asiático. Para comenzar, use [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) para crear una segunda instancia de Azure Redis Cache en el Sudeste Asiático. Esta caché debe estar colocada con su aplicación en Asia.

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` proporciona a la caché el nombre de la caché de Europa Occidental, con el sufijo `-asia`.

### <a name="create-an-app-service-plan-in-asia"></a>Creación de un plan de App Service en Asia
Use [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) para crear un segundo plan de App Service en la región del Sudeste Asiático, con el mismo nivel S1 que el plan de Europa Occidental.

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>Creación de una aplicación web en Asia
Use [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_create) para crear una segunda aplicación web.

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` proporciona a la aplicación el nombre de la aplicación de Europa occidental, con el sufijo `-asia`.

### <a name="configure-the-connection-string-for-redis"></a>Configuración de la cadena de conexión para Redis
Use [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) para agregar a la aplicación web la cadena de conexión de la caché del Sudeste Asiático.

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>Configuración de la implementación de Git para la aplicación de Asia.
Use [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config_local_git) para configurar la implementación de Git local de la segunda aplicación web.

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

Este comando proporciona una salida parecida a la siguiente:

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

Use la dirección URL devuelta para configurar un segundo Git remoto para el repositorio local. El siguiente comando usa el ejemplo de salida anterior.

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>Implementación de la aplicación de ejemplo
Ejecute `git push` para implementar la aplicación de ejemplo en el segundo Git remoto. 

```bash
git push azure-asia master
```

Cuando se le solicite una contraseña, use la contraseña que especificó cuando ejecutó `az appservice web deployment user set`.

### <a name="browse-to-the-asia-app"></a>Navegación a la aplicación de Asia
Use [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) para comprobar que su aplicación está en funcionamiento en Azure.

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>Obtención del id. de recurso de la aplicación de Asia
Use [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_show) para obtener el id. de recurso de la aplicación web del Sudeste Asiático.

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>Adición de un punto de conexión de Traffic Manager para la aplicación de Asia
Use [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) para agregar un segundo punto de conexión al perfil de Traffic Manager.

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>Adición del identificador de región a las aplicaciones web
Use [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) para agregar una variable de entorno específica de una región.

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

El código de aplicación ya usa esta configuración de aplicación. Eche un vistazo a `HighScaleApp\Views\Home\Index.cshtml`.

### <a name="complete"></a>¡Listo!

Ahora, intente acceder a la dirección URL de su perfil de Traffic Manager desde exploradores de diferentes regiones geográficas. Los exploradores de cliente de Europa deben mostrar "ASP.NET Europa Occidental" y el explorador de cliente de Asia debe mostrar "ASP.NET "Sudeste Asiático".

## <a name="more-resources"></a>Más recursos

