1. Inicie sesión en [Azure Portal][Azure portal].
2. En el menú de la izquierda, seleccione **+ Crear un recurso**. A continuación, seleccione **Integración empresarial** > **Relay**.
3. En **Crear espacio de nombres**, especifique un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
4. En el campo **Suscripción**, elija la suscripción de Azure en la que se va a crear el espacio de nombres.
5. En el cuadro [Grupo de recursos](../articles/azure-resource-manager/resource-group-portal.md), seleccione un grupo de recursos existente en el que se colocará el espacio de nombres o cree uno nuevo.  
6. En **Ubicación**, seleccione el país o región donde se debe hospedar el espacio de nombres.
   
    ![Crear un espacio de nombres][create-namespace]
7. Seleccione **Crear**. El sistema crea el espacio de nombres y lo habilita. Tras unos minutos, el sistema realiza el aprovisionamiento de los recursos para la cuenta.

### <a name="get-management-credentials"></a>Obtención de las credenciales de administración

1. Haga clic en **Todos los recursos** y, después, seleccione el nombre del espacio de nombres recién creado.
2. En el espacio de nombres de Relay, seleccione **Directivas de acceso compartido**.  
3. En **Directivas de acceso compartido**, seleccione **RootManageSharedAccessKey**.
   
    ![información de conexión][connection-info]
4. En **Directiva: RootManageSharedAccessKey**, haga clic en el botón **Copiar** situado junto a **Cadena de conexión: clave principal**. La cadena de conexión se copia en el Portapapeles para su uso posterior. Pegue este valor en el Bloc de notas o cualquier otra ubicación temporal.
   
    ![connection-string][connection-string]

5. Repita el paso anterior para copiar y pegar el valor de **Clave principal** en una ubicación temporal para su uso posterior.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
