Cree credenciales de implementación con el comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set).

Se requiere un usuario de implementación para la implementación de FTP y Git local en una aplicación web. El nombre de usuario y la contraseña predeterminados están en el nivel de la cuenta. Son diferentes de las credenciales de suscripción de Azure.

En el siguiente comando, reemplace  *\<nombre de usuario>* y * \<contraseña>* por un nuevo nombre de usuario y contraseña.

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

El nombre de usuario debe ser único. La contraseña debe tener al menos ocho caracteres, con dos de los tres elementos siguientes: letras, números y símbolos. Si se produce un error ` 'Conflict'. Details: 409`, cambie el nombre de usuario. Si se produce un error ` 'Bad Request'. Details: 400`, use una contraseña más segura.

Solo es necesario crear este usuario de implementación una vez. Puede usarlo para todas las implementaciones de Azure.

> [!NOTE]
> Registre el nombre de usuario y la contraseña. Los va a necesitar para implementar la aplicación web más adelante.
>
>