En la ventana del terminal local, agregue una instancia remota de Azure al repositorio de Git local. Reemplace _&lt;paste\_copied\_url\_here>_ por la dirección URL del repositorio de Git remoto que guardó en [Creación de una aplicación web](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Inserte en la instancia remota de Azure para implementar la aplicación con el comando siguiente. Cuando se le pida una contraseña, asegúrese de escribir la que creó en [Configuración de un usuario de implementación](#configure-a-deployment-user), no la que usa para iniciar sesión en Azure Portal.

```bash
git push azure master
```

Este comando puede tardar varios minutos en ejecutarse. Durante la ejecución, muestra información similar a la del ejemplo siguiente:
