Usar la CLI de Azure para obtener la dirección URL de implementación remota para la aplicación de API y, a continuación, configurar la implementación de Git local para que pueda insertar en el servidor remoto.

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

Inserte en la instancia remota de Azure para implementar la aplicación. Se le pedirá la contraseña que proporcionó anteriormente al crear el usuario de implementación. Asegúrese de escribir la contraseña que creó anteriormente en la guía de inicio rápido y no la contraseña que utiliza para iniciar sesión en Azure Portal.

```bash
git push azure master
```
