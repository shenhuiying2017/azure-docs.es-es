Configurar la implementación de Git local en la aplicación web con el comando [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

App Service admite varias maneras de implementar contenido en una aplicación web, como FTP, Git local, GitHub, Visual Studio Team Services y Bitbucket. Para esta guía de inicio rápido, va a realizar la implementación mediante el uso de Git local. Esto significa que va a realizar la implementación con un comando de Git para insertar desde un repositorio local en un repositorio de Azure. 

En el siguiente comando, reemplace  *\<app_name >* por el nombre de la aplicación web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

La salida tiene el siguiente formato:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

`<username>` es el [usuario de implementación](#configure-a-deployment-user) que creó en el paso anterior.

Copie el identificador URI que se muestra; se usará en el paso siguiente.
