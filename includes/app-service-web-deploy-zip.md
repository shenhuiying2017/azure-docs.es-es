## <a name="deploy-uploaded-zip-file"></a>Implementación del archivo ZIP cargado

En Cloud Shell, implemente el archivo ZIP cargado en la aplicación web mediante el comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Asegúrese de sustituir *\<nombre_de_aplicación>* por el nombre de la aplicación web.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Este comando implementa los archivos y directorios del archivo ZIP en la carpeta predeterminada de la aplicación App Service (`\home\site\wwwroot`) y reinicia la aplicación. Si se configura algún proceso de compilación personalizado adicional, se ejecuta también.
