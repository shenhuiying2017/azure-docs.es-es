1. Inicie sesión en [Azure Portal][Azure Portal].
2. En el panel de navegación izquierdo del portal, haga clic en **Nuevo**, después, haga clic en **Enterprise Integration** y, finalmente, haga clic en **Relay**.
3. En el cuadro de diálogo **Crear un espacio de nombres** , especifique un nombre para el espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
4. En el campo **Suscripción** elija la suscripción de Azure en la que se va a crear el espacio de nombres.
5. En el campo **[Grupo de recursos](../articles/azure-portal/resource-group-portal.md)**, elija un grupo de recursos existente en el que residirá el espacio de nombres o cree uno.      
6. En **Ubicación**, elija el país o región donde se debe hospedar el espacio de nombres.
   
    ![Crear un espacio de nombres][create-namespace]
7. Haga clic en **Crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

### <a name="obtain-the-management-credentials"></a>Obtención de las credenciales de administración
1. En la lista de espacios de nombres, haga clic en el nombre del espacio de nombres recién creado.
2. En la hoja del espacio de nombres, haga clic en **Directivas de acceso compartido**.
3. En la hoja **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.
   
    ![información de conexión][connection-info]
4. En la hoja **Directiva: RootManageSharedAccessKey**, haga clic en el botón Copiar junto a **Cadena de conexión-clave principal**, para copiar la cadena de conexión en el portapapeles para su uso posterior. Pegue este valor en el Bloc de notas o cualquier otra ubicación temporal.
   
    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure Portal]: https://portal.azure.com


<!--HONumber=Nov16_HO2-->


