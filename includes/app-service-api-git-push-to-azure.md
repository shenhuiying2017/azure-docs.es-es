Use la CLI de Azure para obtener la dirección URL de implementación remota para la aplicación de API. En el siguiente comando, reemplace  *\<app_name >* por el nombre de la aplicación web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Configure la implementación de Git local para insertarla en el servidor remoto.

```bash
git remote add azure <URI from previous step>
```

Inserte en la instancia remota de Azure para implementar la aplicación. Se le pedirá la contraseña que proporcionó anteriormente al crear el usuario de implementación. Asegúrese de escribir la contraseña que creó anteriormente en la guía de inicio rápido y no la contraseña que utiliza para iniciar sesión en Azure Portal.

```bash
git push azure master
```
