## <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

Agregue una instancia remota de Azure en el repositorio de Git local.

```bash
git remote add azure <URI from previous step>
```

Inserte en la instancia remota de Azure para implementar la aplicación. Se le pedirá la contraseña que proporcionó anteriormente al crear el usuario de implementación. Asegúrese de escribir la contraseña que creó en [Configuración de un usuario de implementación](#configure-a-deployment-user), no la contraseña que usa para iniciar sesión en Azure Portal.

```bash
git push azure master
```

El comando anterior muestra información similar a la del ejemplo siguiente:
