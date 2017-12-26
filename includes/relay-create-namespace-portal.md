1. Inicie sesión en [Azure Portal][Azure portal].
2. En el panel de navegación izquierdo del portal, haga clic en **+ Crear un recurso**, luego, en **Enterprise Integration** y, finalmente, en **Relay**.
3. En el cuadro de diálogo **Crear un espacio de nombres** , especifique un nombre para el espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
4. En el campo **Suscripción** elija la suscripción de Azure en la que se va a crear el espacio de nombres.
5. En el campo **[Grupo de recursos](../articles/azure-resource-manager/resource-group-portal.md)**, elija un grupo de recursos existente en el que residirá el espacio de nombres o cree uno.      
6. En **Ubicación**, elija el país o región donde se debe hospedar el espacio de nombres.
   
    ![Crear un espacio de nombres][create-namespace]
7. Haga clic en **Crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Tras unos minutos, el sistema realiza el aprovisionamiento de los recursos para la cuenta.

### <a name="obtain-the-management-credentials"></a>Obtención de las credenciales de administración

1. Haga clic en **Todos los recursos** y, después, en el nombre del espacio de nombres recién creado.
2. En la ventana del espacio de nombres de Relay, haga clic en **Directivas de acceso compartido**.
3. En la ventana **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.
   
    ![información de conexión][connection-info]
4. En la pantalla **Policy: RootManageSharedAccessKey**, haga clic en el botón **Copiar** que hay junto a **Cadena de conexión: clave principal** para copiar la cadena de conexión en el portapapeles, con el fin de usarla posteriormente. Pegue este valor en el Bloc de notas o cualquier otra ubicación temporal.
   
    ![connection-string][connection-string]

5. Repita el paso anterior, copie y pegue el valor de **clave principal** en una ubicación temporal para su uso posterior.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
