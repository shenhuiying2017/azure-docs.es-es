En la ventana del terminal local, agregue una instancia remota de Azure al repositorio de Git local.

```bash
git remote add azure <URI from previous step>
```

Inserte en la instancia remota de Azure para implementar la aplicación con el comando siguiente. Cuando se le pida una contraseña, asegúrese de escribir la que creó en [Configuración de un usuario de implementación](#configure-a-deployment-user), no la que usa para iniciar sesión en Azure Portal.

```bash
git push azure master
```

El comando anterior muestra información similar a la del ejemplo siguiente:
