En Cloud Shell, cree una [aplicación web](../articles/app-service/app-service-web-overview.md) en el plan de App Service `myAppServicePlan` con el comando [az webapp create](/cli/azure/webapp#create). 

La aplicación web ofrece un espacio de hospedaje para el código y proporciona una dirección URL para que pueda ver la aplicación implementada.

En el siguiente comando, reemplace  *\<app_name >* por un nombre único (los caracteres válidos son `a-z`, `0-9` y `-`). Si `<app_name>` no es único, obtendrá el mensaje de error "Ya existe un sitio web con el nombre especificado <app_name>". La dirección URL predeterminada de la aplicación web es `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Cuando se ha creado la aplicación web, la CLI de Azure muestra información similar a la del ejemplo siguiente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ha creado una aplicación web vacía, con la implementación de Git habilitada.

> [!NOTE]
> La dirección URL del repositorio de Git remoto se muestra en la propiedad `deploymentLocalGitUrl`, con el formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Guarde esta dirección URL porque la necesitará más adelante.
>

Vaya al sitio para ver la aplicación web recién creada.

```bash
http://<app_name>.azurewebsites.net
```
