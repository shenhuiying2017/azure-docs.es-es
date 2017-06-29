Configurar la implementación de Git local en la aplicación de API con el comando [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).   

App Service admite varias maneras de implementar contenido en una aplicación web, como FTP, Git local, GitHub, Visual Studio Team Services y Bitbucket. Para esta guía de inicio rápido, va a realizar la implementación mediante el uso de Git local. Esto significa que va a realizar la implementación con un comando de Git para insertar desde un repositorio local en un repositorio de Azure.  

Utilice el script siguiente para establecer las credenciales de implementación en el nivel de cuenta que se van a utilizar cuando se inserte el código, asegurándose de incluir sus propios valores para el nombre de usuario y la contraseña.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
