## <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación  

En el caso de FTP y repositorio de Git local, debe tener un usuario de implementación configurado en el servidor para autenticar la implementación.

> [!NOTE]
> Se requiere un usuario de implementación para la implementación de FTP y Git Local en una aplicación web.
> `username` y `password` están en el nivel de cuenta. Son diferentes de las credenciales de suscripción de Azure.
>

Ejecute el comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para crear las credenciales de implementación.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

El nombre de usuario debe ser único y la contraseña debe ser segura. Si se produce un error ` 'Conflict'. Details: 409`, cambie el nombre de usuario. Si se produce un error ` 'Bad Request'. Details: 400`, use una contraseña más segura.

Solo es necesario crear este usuario de implementación una vez. Puede usarlo para todas las implementaciones de Azure.

Anote el nombre de usuario y la contraseña, ya que tendrá que usarlos más adelante en un paso posterior al implementar la aplicación.